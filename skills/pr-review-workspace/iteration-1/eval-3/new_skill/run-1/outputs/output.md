# Code Review — Refactor notification service to support multiple channels

**Tamanho:** Grande (~600 linhas) — revisao agrupada por area logica.

---

### Resumo

Refatora o servico de notificacao de um canal unico (email) para um dispatcher multi-canal (email, SMS e push), com configuracao por tenant. A mudanca aumenta a superficie de falha pois agora qualquer canal pode falhar silenciosamente durante o envio.

---

### Pontos positivos

- Separacao de responsabilidades bem executada: cada notifier em arquivo proprio (`emailNotifier.ts`, `smsNotifier.ts`, `pushNotifier.ts`) facilita extensao futura sem modificar o dispatcher.
- Configuracao por tenant em `src/config/notifications.ts` e uma decisao arquitetural correta — evita flags globais e permite rollout gradual por cliente.
- O dispatcher itera pelos canais de forma previsivel e nao mescla logica de envio com logica de roteamento.

---

### Pontos de atencao

**(Bloqueador) Falha silenciosa em canais — o erro some sem rastro**
O comentario `// falha silenciosa se canal nao disponivel` indica que excecoes lancadas por `notifier.send()` sao suprimidas. Qualquer falha de envio (rede, credencial invalida, rate limit do provedor) e perdida sem log e sem retry. Em producao, o sistema aparenta funcionar enquanto nenhuma notificacao e entregue.
`src/services/notificationService.ts` — bloco `for...of`

Correcao minima:
```typescript
try {
  await notifier.send(userId, event);
} catch (err) {
  logger.error({ userId, channel, event, err }, 'Notification send failed');
  // decidir: relançar, acumular erros, ou continuar nos demais canais
}
```

**(Bloqueador) Ausencia de rollback em falha parcial — inconsistencia de estado**
Se o tenant tem `['email', 'sms', 'push']` e o email e enviado com sucesso mas o SMS falha, o usuario recebe a notificacao por um canal e nao pelos outros, sem que o sistema saiba ou reaja. Dependendo do tipo de evento (ex.: OTP, alerta critico), isso pode ter impacto direto no usuario.
Definir e documentar a politica: falha em um canal deve abortar os demais? Deve retentar somente os canais que falharam? A resposta depende do produto, mas a ausencia de qualquer politica e um risco confirmado.
`src/services/notificationService.ts`

**(Alto) `getNotifier(channel)` sem tratamento de canal desconhecido**
Se `tenant.enabledChannels` contiver um valor nao mapeado (ex.: `'whatsapp'`, erro de digitacao no banco, novo canal adicionado ao config sem implementacao correspondente), `getNotifier` provavelmente retorna `undefined` ou lanca excecao nao tratada. O loop quebra — ou continua silenciosamente — sem aviso.
`src/services/notificationService.ts`

Verificar que `getNotifier` faz guard explicito:
```typescript
const notifier = getNotifier(channel);
if (!notifier) {
  logger.warn({ channel, userId }, 'Unknown or unregistered notification channel');
  continue;
}
```

**(Alto) `getTenant(userId)` sem tratamento de usuario inexistente ou config ausente**
Se `getTenant` retornar `null`/`undefined` (usuario deletado, tenant sem config), o acesso a `tenant.enabledChannels` lanca `TypeError` nao tratado, derrubando o worker ou deixando a promise pendente sem resolucao.
`src/services/notificationService.ts:3-4`

**(Medio) Loop com I/O sequencial em vez de paralelo — latencia acumulada**
O `await` dentro do `for...of` executa os canais em serie. Para um tenant com 3 canais, a latencia total e a soma das latencias individuais. Se os canais sao independentes, `Promise.allSettled` reduz o tempo para o canal mais lento e ainda captura erros individualmente.

```typescript
const results = await Promise.allSettled(
  channels.map(channel => getNotifier(channel)?.send(userId, event))
);
// inspecionar results para logar falhas
```

Atencao: `allSettled` so faz sentido se a politica de falha parcial for "continuar nos demais canais". Definir a politica primeiro (ver ponto Bloqueador acima).
`src/services/notificationService.ts`

**(Medio) `tenant.enabledChannels` sem validacao de schema**
A config vem do banco via `getTenant`. Se o campo for `null`, array vazio, ou contiver tipos inesperados, o comportamento e indefinido. Adicionar validacao (Zod, io-ts, ou guard manual) na leitura da config impede que dado corrompido no banco cause falha em runtime.
`src/config/notifications.ts` / `src/services/notificationService.ts`

**(Baixo) `notificationWorker.ts` — verificar se o worker tem mecanismo de dead-letter ou retry**
Com a mudanca para dispatcher, falhas que antes eram localizadas no canal de email agora podem acontecer em qualquer canal. Se o worker nao tem retry com backoff e dead-letter queue, eventos perdidos nao sao recuperaveis.

---

### Sugestoes

- Adicionar metrica por canal (`notifications_sent_total{channel}`, `notifications_failed_total{channel}`) para observar taxas de falha por canal em producao sem precisar vasculhar logs.
- Considerar um tipo discriminado (`type Channel = 'email' | 'sms' | 'push'`) em vez de `string` para `enabledChannels` — o compilador TypeScript passaria a rejeitar canais invalidos em tempo de compilacao.
- Documentar no codigo (ou em ADR) a decisao sobre politica de falha parcial — e uma decisao de produto com impacto de engenharia e vai ser esquecida sem registro.

---

### Testes e validacoes recomendadas

- **Canal com falha no meio da lista:** tenant com `['email', 'sms', 'push']`, SMS lanca excecao — verificar que email foi enviado, push foi (ou nao foi) tentado, e o erro foi registrado.
- **Canal desconhecido:** `enabledChannels` contendo valor nao mapeado — garantir que o sistema nao lanca excecao nao tratada.
- **`getTenant` retorna `null`:** verificar comportamento quando usuario nao existe ou nao tem config.
- **`enabledChannels` vazio:** `[]` — garantir que a funcao retorna sem erro e sem efeito colateral.
- **Todos os canais falham:** verificar que o evento nao some silenciosamente e ha registro de falha total.
- **Integracao com worker:** garantir que falha no dispatcher nao deixa a mensagem presa na fila sem reprocessamento.

---

### Perguntas

1. Qual e a politica esperada quando um canal falha — abortar os demais, continuar, ou retentar depois? Essa decisao define se `Promise.allSettled`, `Promise.all` ou loop serial com try/catch e a abordagem correta.
2. `getNotifier` atualmente lanca excecao ou retorna `null`/`undefined` para canal nao mapeado? O tratamento de erro adequado depende do contrato dessa funcao.
