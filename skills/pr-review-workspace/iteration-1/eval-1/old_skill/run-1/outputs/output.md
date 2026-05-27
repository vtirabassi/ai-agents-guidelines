# PR Review: Fix user deletion not cascading to sessions

### Resumo
O PR corrige um bug onde a exclusao de um usuario nao removia suas sessoes ativas no Redis, causando tokens orfaos que podiam permanecer validos indefinidamente. A mudanca adiciona a limpeza das chaves de sessao no Redis antes de deletar o registro do usuario no banco de dados.

---

### Pontos positivos
- Aborda um problema real de seguranca: tokens orfaos podiam permitir acesso indevido apos exclusao do usuario.
- A logica e simples e localizada, sem introducao de dependencias desnecessarias.
- A ordem (limpar sessoes antes de deletar o usuario) faz sentido na perspectiva de cleanup.

---

### Pontos de atencao (riscos, bugs, impactos)

- **(Bloqueador) Ausencia de atomicidade / rollback entre Redis e banco de dados** — Se `redis.del(sessions)` for bem-sucedido mas `db.users.delete` falhar (ex: erro de constraint, timeout), o usuario continuara existindo no banco sem sessoes ativas, corrompendo o estado da aplicacao. O caminho inverso tambem e arriscado: se o delete no banco ocorrer antes da limpeza do Redis, uma falha posterior deixa sessoes orfas. Como Redis e banco sao sistemas distintos sem transacao distribuida, e necessario definir uma estrategia explicita de falha (ex: delete o usuario primeiro e tratar sessoes orfas de forma eventual/compensatoria, ou usar um mecanismo de saga). `src/services/userService.ts:45`

- **(Alto) Race condition: novas sessoes criadas entre `redis.keys` e `redis.del`** — Se o usuario (ou um processo concorrente) criar uma nova sessao no intervalo entre a listagem das chaves e a exclusao, essa sessao nao sera removida e continuara valida apos a exclusao do usuario. Em sistemas com alta concorrencia ou onde o usuario pode ter multiplos clientes ativos, isso e uma janela de seguranca real. `src/services/userService.ts:46-48`

- **(Alto) `redis.keys` e bloqueante e nao recomendado em producao** — O comando `KEYS` no Redis e O(N) e bloqueia o servidor enquanto varre todas as chaves. Em instancias com muitas chaves, isso pode causar latencia para todos os clientes Redis durante a execucao. O padrao recomendado e usar `SCAN` com cursor para iterar de forma nao-bloqueante. `src/services/userService.ts:46`

- **(Medio) Ausencia de tratamento de erro explicito** — Se `redis.del` ou `redis.keys` lancarem excecao (ex: Redis indisponivel), a funcao propagara o erro sem log ou contexto adicional, dificultando diagnostico em producao. Seria util ao menos logar o erro com o `userId` antes de propagar. `src/services/userService.ts:46-49`

- **(Medio) Falta de idempotencia** — Se `deleteUser` for chamado duas vezes para o mesmo `userId` (ex: retry por falha de rede), a segunda chamada pode falhar no `db.users.delete` com erro de registro nao encontrado, sem tratamento. A funcao deveria ser idempotente ou documentar explicitamente que nao o e. `src/services/userService.ts:49`

- **(Baixo) O pattern `session:${userId}:*` e um acoplamento implicito** — A convencao de nomenclatura das chaves de sessao esta embutida diretamente nesta funcao. Se o padrao mudar ou houver outros prefixos de sessao, este codigo nao sera atualizado automaticamente. Considerar extrair para uma constante ou funcao utilitaria compartilhada com o modulo que cria as sessoes. `src/services/userService.ts:46`

---

### Sugestoes (melhorias, refactor, estilo)

- Substituir `redis.keys` por `redis.scan` (cursor-based) para evitar bloqueio do Redis em producao.
- Avaliar se a limpeza de sessoes deve ser responsabilidade do `userService` ou de um `sessionService` dedicado — separar responsabilidades facilita testar e reutilizar a logica de invalidacao de sessao (ex: em logout forcado, banimento, rotacao de credenciais).
- Considerar usar um evento de dominio (ex: `UserDeleted`) consumido pelo servico de sessao, desacoplando os dois sistemas e tornando o fluxo mais resiliente a falhas parciais.
- Adicionar log estruturado antes e apos as operacoes criticas para facilitar auditoria e diagnostico:
  ```ts
  logger.info({ userId, sessionCount: sessions.length }, 'Removing sessions before user deletion');
  ```

---

### Testes e validacoes recomendadas

- [ ] Teste unitario: usuario com N sessoes ativas e deletado — verificar que todas as chaves `session:userId:*` sao removidas do Redis.
- [ ] Teste unitario: usuario sem sessoes ativas e deletado — verificar que nao ha erro e que `redis.del` nao e chamado desnecessariamente (ou e chamado com lista vazia sem efeito colateral).
- [ ] Teste de falha: simular falha no `db.users.delete` apos `redis.del` bem-sucedido — verificar o comportamento e se o estado final e aceitavel.
- [ ] Teste de falha: simular Redis indisponivel — verificar que o erro e propagado corretamente e que o usuario NAO e deletado do banco (ou que o comportamento esta documentado).
- [ ] Teste de concorrencia: verificar comportamento quando uma nova sessao e criada durante a execucao de `deleteUser`.
- [ ] Validar em ambiente de staging com Redis real que `KEYS` nao causa impacto perceptivel de latencia (e planejar migracao para `SCAN` se necessario).

---

### Perguntas

- Qual e a estrategia de consistencia esperada entre Redis e banco em caso de falha parcial? O time aceita eventual consistency ou precisa de garantia forte?
- Existe algum mecanismo de TTL nas chaves de sessao no Redis? Se sim, sessoes orfas ja expirariam naturalmente — o que mudaria a urgencia do Bloqueador de atomicidade.
- Ha outros lugares no codigo que criam sessoes com o padrao `session:userId:*`? Centralizar o pattern evita divergencia futura.
