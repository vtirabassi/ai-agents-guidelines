# PR Review: Fix user deletion not cascading to sessions

## Resumo

Corrige bug onde a exclusao de usuario nao removia sessoes ativas no Redis, resultando em tokens orfaos que persistiam indefinidamente. A mudanca adiciona limpeza explicita das chaves de sessao antes de deletar o usuario no banco.

**Escala:** Pequeno (< 10 linhas de diff). Revisao linear com foco em corretude e seguranca.
**Tipo:** `bugfix`
**Risco principal:** Ordem de operacoes entre Redis e banco de dados — falha parcial pode deixar o sistema em estado inconsistente.

---

## Pontos positivos

- A intencao esta correta: limpar o Redis antes de deletar o usuario evita tokens orfaos.
- Verificacao de `sessions.length > 0` antes do `del` e boa pratica para evitar chamada desnecessaria ao Redis.
- O fix ataca diretamente a causa raiz descrita na descricao do PR.

---

## Pontos de atencao

**(Bloqueador) Ausencia de atomicidade / rollback em falha parcial — perda potencial de dado ou estado inconsistente.**
Se `redis.del(sessions)` falhar apos obter as sessoes, o usuario nao e deletado — OK, estado consistente. Porem, se `db.users.delete` falhar apos o Redis ja ter removido as sessoes, o usuario permanece no banco mas sem sessoes, podendo causar comportamento inesperado (ex.: usuario existente que nao consegue autenticar porque suas sessoes foram apagadas). Nao ha rollback para re-criar as chaves Redis em caso de falha no banco. `src/services/userService.ts:45`

**(Alto) `redis.keys()` nao deve ser usado em producao com bases grandes — bloqueio do event loop do Redis.**
`redis.keys(pattern)` e uma operacao O(N) que itera sobre todas as chaves do Redis e bloqueia o servidor durante a execucao. Em ambientes com muitas chaves, isso pode causar latencia ou timeouts para outras operacoes. A alternativa recomendada e `redis.scan()` com iteracao paginada, que e nao-bloqueante. `src/services/userService.ts:46`

**(Alto) Race condition: novas sessoes podem ser criadas entre o `keys` e o `del`.**
Existe uma janela de tempo entre `redis.keys(...)` e `redis.del(sessions)` onde uma nova sessao pode ser criada para o usuario (ex.: login concorrente). Essa sessao nao estara na lista obtida pelo `keys` e portanto nao sera deletada, resultando em token orfao mesmo apos o fix. `src/services/userService.ts:46-48`

**(Medio) Nenhuma verificacao de existencia do usuario antes da operacao.**
Se `userId` nao existir no banco, a funcao ainda consulta o Redis e tenta deletar sessoes antes de falhar no `db.users.delete`. Embora nao seja um bug critico, pode gerar operacoes desnecessarias e logs confusos em producao. `src/services/userService.ts:45`

**(Baixo) O padrao de chave `session:${userId}:*` depende de convencao implicita.**
Se o formato das chaves de sessao mudar em outro lugar do codigo, este trecho deixa de funcionar silenciosamente. Considerar centralizar o pattern de chave em uma constante ou funcao utilitaria para garantir consistencia. `src/services/userService.ts:46`

---

## Sugestoes

- Encapsular a logica de remocao de sessoes em uma funcao separada (ex.: `clearUserSessions(userId)`), separando responsabilidades e facilitando reutilizacao em outros fluxos (ex.: logout forcado, banimento de usuario).
- Adicionar log estruturado apos a remocao das sessoes com o numero de chaves deletadas — util para auditoria e debugging em producao sem necessidade de reproducao.
- Avaliar uso de transacao ou saga pattern se o banco suportar, para garantir que a limpeza do Redis e a exclusao do usuario ocorram como operacao atomica ou com compensacao automatica.

---

## Testes e validacoes recomendadas

- **Happy path:** Deletar usuario com sessoes ativas e verificar que todas as chaves `session:{userId}:*` foram removidas do Redis e o usuario foi deletado do banco.
- **Usuario sem sessoes:** Deletar usuario sem nenhuma sessao ativa — garantir que o fluxo completa sem erro.
- **Falha no banco apos limpeza do Redis:** Simular falha em `db.users.delete` (ex.: constraint violation) e verificar o estado resultante — usuario no banco sem sessoes e o comportamento esperado pelo time?
- **Sessao criada durante a operacao (race condition):** Simular criacao de sessao concorrente entre o `keys` e o `del` — verificar se token orfao persiste.
- **`redis.keys` com grande volume:** Testar (ou medir em staging) o impacto de latencia do `redis.keys` com base Redis populada para avaliar necessidade de migracao para `redis.scan`.
- **`userId` inexistente:** Chamar `deleteUser` com ID que nao existe no banco e verificar comportamento (erro esperado? silencioso?).

---

## Perguntas

1. Existe mecanismo de lock ou mutex para serializar operacoes de delete de usuario e login concorrente? Se nao, a race condition mencionada e um risco real a ser mitigado.
2. O Redis e compartilhado com outros servicos ou exclusivo para sessoes? Isso afeta o impacto do `redis.keys` em producao.
3. Ha expectativa de comportamento transacional entre Redis e banco (ex.: rollback de sessoes se o delete do usuario falhar)? Isso define se o bloqueador de atomicidade precisa ser resolvido antes do merge.
