### Resumo
O PR refatora o servico de notificacao monolitico (antes suportava apenas email) para um dispatcher multi-canal que itera sobre os canais habilitados por tenant (email, SMS e push), extraindo cada canal em seu proprio notifier e adicionando configuracao por tenant em `src/config/notifications.ts`.

### Pontos positivos
- Boa separacao de responsabilidades: cada canal tem seu proprio notifier dedicado, seguindo o principio de responsabilidade unica.
- A configuracao por tenant permite ativar/desativar canais sem deploy, o que e flexivel e escalavel.
- O dispatcher e simples de entender e de estender com novos canais no futuro.

### Pontos de atencao (riscos, bugs, impactos)

- **(Bloqueador)** Falha silenciosa no loop de canais — se `getNotifier(channel)` retornar `null`/`undefined` ou o `notifier.send()` lanciar uma excecao, o erro e engolido e os canais subsequentes podem nao ser processados dependendo de como o erro se propaga. O comentario no diff diz "falha silenciosa se canal nao disponivel", mas isso significa que uma notificacao critica (ex: alerta de seguranca) pode simplesmente desaparecer sem que ninguem saiba. Nao ha logging do erro, nenhuma metrica de falha por canal, e nenhum mecanismo de retry. `src/services/notificationService.ts` (bloco do dispatcher)

- **(Bloqueador)** Ausencia de tratamento de erro isolado por canal — se SMS falhar, o erro pode interromper o loop inteiro antes de tentar push. O `await` dentro do `for...of` e sincrono em relacao ao loop: uma excecao nao capturada em `notifier.send()` sobe para o caller sem enviar os canais restantes. O correto seria usar `try/catch` por canal ou `Promise.allSettled` se os canais forem independentes. `src/services/notificationService.ts`

- **(Alto)** Consulta ao tenant dentro da funcao de envio sem cache — `getTenant(userId)` e chamada a cada invocacao de `sendNotification`. Se o worker processar um burst de notificacoes para o mesmo usuario/tenant, isso gera N consultas identicas ao banco/cache externo. Dependendo do volume, isso pode ser um gargalo critico. `src/services/notificationService.ts:3`

- **(Alto)** `getNotifier(channel)` nao tem tipagem ou contrato definido no diff — se um tenant tiver um canal habilitado que nao possui notifier implementado (ex: um canal legado ou com typo na config), o comportamento e indefinido. Nao ha validacao do valor de `channel` contra os canais suportados antes de chamar `getNotifier`. Isso pode causar erros de runtime silenciosos ou comportamento inesperado. `src/services/notificationService.ts:6`

- **(Alto)** Sem idempotencia no dispatcher — se o worker reprocessar um evento (retry apos falha), o usuario pode receber multiplas notificacoes duplicadas pelo mesmo evento. Nao ha verificacao de "ja enviado" antes de disparar. `src/workers/notificationWorker.ts`

- **(Medio)** Ausencia de logs/observabilidade por canal — nao ha logging de qual canal foi enviado, qual falhou, qual foi ignorado. Isso torna diagnostico de problemas em producao muito dificil: nao sera possivel saber se um usuario nao recebeu SMS porque o canal estava desabilitado, porque houve erro, ou porque o notifier nao enviou. `src/services/notificationService.ts`

- **(Medio)** Sem testes visiveis no diff — para um refactor que altera o comportamento de entrega de notificacoes (potencialmente critico dependendo do dominio), nao ha mencao de testes unitarios para o dispatcher, mocks dos notifiers, ou testes de integracao para os novos canais. Regressoes podem nao ser detectadas.

- **(Baixo)** O loop `for...of` com `await` processa canais em serie — se os canais forem independentes (o que parece ser o caso), processar em paralelo com `Promise.all` ou `Promise.allSettled` reduziria a latencia total de envio. Para usuarios com 3 canais habilitados, o tempo de resposta e a soma dos 3 envios ao inves do maior deles.

### Sugestoes (melhorias, refactor, estilo)

- Envolver o `notifier.send()` em `try/catch` por canal, logando o erro com contexto (`userId`, `channel`, `event.type`) e continuando para o proximo canal. Considerar emitir uma metrica de falha por canal para alertas.
- Usar `Promise.allSettled` se os canais forem independentes, o que melhora latencia e isola falhas naturalmente.
- Adicionar validacao de `channel` contra um enum/set de canais suportados antes de chamar `getNotifier`, com fallback explicito (log de warning + skip).
- Considerar cache de curta duracao (ex: 60s em memoria ou Redis) para o resultado de `getTenant(userId)` se chamado em alta frequencia.
- Adicionar campo de idempotencia no evento ou verificacao no dispatcher para evitar envios duplicados em reprocessamentos.

### Testes e validacoes recomendadas

- Teste unitario do dispatcher: simular canal que lanca excecao e verificar que os demais canais ainda sao processados.
- Teste de canal desconhecido: passar canal nao suportado em `enabledChannels` e verificar comportamento (deve logar warning e continuar, nao travar).
- Teste de tenant sem canais habilitados: `enabledChannels = []` deve resultar em nenhum envio sem erro.
- Teste de reprocessamento: chamar `sendNotification` duas vezes com o mesmo evento e verificar que o usuario nao recebe duplicatas.
- Teste de carga/burst: verificar quantas consultas ao banco `getTenant` gera para N notificacoes do mesmo usuario no mesmo periodo.
- Validar em staging com os 3 canais habilitados que todos recebem a notificacao corretamente antes de habilitar em producao.

### Perguntas

- Qual e o comportamento esperado quando um canal falha? O PR deve ser mercado como "entregue" se pelo menos 1 canal teve sucesso, ou apenas se todos tiveram?
- Ha SLA de entrega para notificacoes criticas (ex: alertas de seguranca) que exijam retry automatico por canal?
- A config `enabledChannels` por tenant e validada no momento do cadastro, ou pode conter valores arbitrarios?
- O worker ja tem logica de retry para o job como um todo? Se sim, a ausencia de idempotencia no dispatcher e um risco imediato.
