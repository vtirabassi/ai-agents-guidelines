# Code Review: Add export endpoint for user data (LGPD compliance)

**Tamanho do PR:** Pequeno (< 100 linhas de diff) — revisao linear aplicada com foco em corretude e seguranca.

---

### Resumo

Adiciona endpoint `GET /api/users/:id/export` que retorna todos os dados de um usuario (incluindo pedidos, enderecos e metodos de pagamento) para atender o direito de portabilidade previsto na LGPD. A mudanca e nova rota de leitura sem alteracao de dados existentes.

**Tipo da mudanca:** `feature`
**Areas de maior risco:** seguranca (endpoint novo sem autorizacao visivel, exposicao de PII e dados financeiros).

---

### Pontos positivos

- Consulta usa Prisma com relacionamentos declarados (`include`), evitando N+1 e mantendo a intencao legivel.
- Tratamento de recurso nao encontrado com `404` esta presente, o que e correto.
- Escopo da busca e delimitado por `userId`, nao retornando dados de outros usuarios por design da query.

---

### Pontos de atencao

**(Bloqueador) Ausencia de autorizacao — qualquer usuario autenticado (ou nao autenticado) pode exportar dados de qualquer outro usuario.**
O endpoint aceita `req.params.id` sem verificar se o usuario logado tem permissao para acessar aquele `id`. Um atacante autenticado pode enumerar IDs e exportar dados de todos os usuarios da base, incluindo `paymentMethods`, que provavelmente contem dados financeiros sensiveis. Antes do `findUnique`, e necessario verificar que `req.user.id === userId` (ou equivalente de admin).
`src/routes/users.ts` (rota nova)

**(Bloqueador) Ausencia de autenticacao — endpoint pode estar publico.**
Nao ha middleware de autenticacao visivel no diff. Se a rota nao estiver protegida pelo router pai ou por middleware global, qualquer requisicao sem token obtem os dados completos do usuario. Confirmar e evidenciar no codigo que autenticacao esta garantida antes do handler.
`src/routes/users.ts` (rota nova)

**(Alto) `paymentMethods` retornado em texto plano na resposta JSON.**
Dados de metodos de pagamento (possivelmente numeros de cartao, mesmo que parciais, tokens de gateway, validade) nao devem ser expostos integralmente em uma exportacao de portabilidade LGPD. A LGPD exige portabilidade de dados fornecidos pelo titular, nao necessariamente de tokens internos de processamento. Avaliar o que deve ser incluido e mascarar ou omitir campos sensiveis (`cardNumber`, `cvv`, tokens de terceiros).
`src/routes/users.ts:109` (include: `paymentMethods: true`)

**(Alto) `userId` aceita qualquer string sem validacao de formato.**
Se o ID for UUID, CUID ou numerico, a entrada deve ser validada antes de chegar ao banco. Input malformado pode gerar erros internos expostos ou comportamentos inesperados dependendo do driver do banco.
`src/routes/users.ts:103` (`const userId = req.params.id`)

**(Medio) Sem rate limiting no endpoint de exportacao.**
Exportacoes de dados completos sao operacoes custosas e sensiveis. Sem throttling, o endpoint e vetorialmente exploravel para exfiltracao em massa ou para gerar carga no banco. Recomenda-se rate limiting especifico (ex: 1-5 requisicoes por usuario por hora).
`src/routes/users.ts` (rota nova)

**(Medio) Sem log de auditoria para exportacoes.**
Para conformidade LGPD e rastreabilidade, cada exportacao de dados pessoais deveria gerar um registro de auditoria (quem exportou, quando, para qual usuario). Ausente no diff.
`src/routes/users.ts` (rota nova)

**(Baixo) Resposta nao tem `Content-Disposition` nem cabecalho indicando natureza do dado.**
Para uma exportacao LGPD, e boa pratica retornar `Content-Disposition: attachment; filename="dados-usuario-{id}.json"` para sinalizar ao cliente que o conteudo e um arquivo de exportacao, nao uma resposta de API comum.

---

### Sugestoes

- Considerar paginar ou limitar os relacionamentos retornados (ex: `orders` pode ter milhares de registros) para evitar payloads gigantescos e timeout na requisicao.
- Adicionar um campo `exportedAt` no JSON de resposta com o timestamp da exportacao — util para o usuario e para auditoria.
- Documentar quais campos sao incluidos/excluidos da exportacao e a justificativa LGPD para cada decisao de exclusao (ex: tokens de pagamento omitidos por nao serem dados fornecidos pelo titular).
- Avaliar se o endpoint deve ser assincrono (gera arquivo e envia por e-mail) em vez de sincrono, especialmente se `orders` e `addresses` puderem ser volumosos.

---

### Testes e validacoes recomendadas

- Testar que usuario autenticado **nao consegue** exportar dados de outro usuario (deve retornar `403`).
- Testar que requisicao sem token retorna `401` (ou `403`), nao os dados.
- Testar com usuario que tem muitos pedidos para validar tempo de resposta e ausencia de timeout.
- Testar com `id` invalido (string aleatoria, SQL fragment, UUID malformado) para garantir resposta controlada.
- Testar que `paymentMethods` na resposta nao contem campos que nao deveriam ser expostos.
- Testar comportamento quando o usuario existe mas nao tem `orders`, `addresses` ou `paymentMethods` (listas vazias, nao `null`).

---

### Perguntas

1. Ha middleware de autenticacao aplicado ao router pai desta rota? Se sim, onde esta definido? Isso e critico para avaliar o risco real do bloqueador de autenticacao.
2. O que a tabela `paymentMethods` armazena exatamente? Tokens de gateway (ex: Stripe customer ID), dados de cartao (mesmo que mascarados), ou outro formato? Isso define a severidade real da exposicao.
3. A LGPD exige que a exportacao inclua `paymentMethods`? Ou e uma decisao de produto que pode ser revisitada?
