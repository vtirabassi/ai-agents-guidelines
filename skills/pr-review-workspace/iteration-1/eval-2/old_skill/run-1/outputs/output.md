# Code Review — PR: Add export endpoint for user data (LGPD compliance)

### Resumo

O PR adiciona o endpoint `GET /api/users/:id/export` que retorna todos os dados do usuario (incluindo pedidos, enderecos e metodos de pagamento) para atender o direito de portabilidade previsto na LGPD. A mudanca e de escopo pequeno — 18 linhas novas em `src/routes/users.ts`.

---

### Pontos positivos

- Objetivo claro e alinhado a requisito legal (LGPD — Art. 18, portabilidade de dados).
- Implementacao enxuta e direta, sem complexidade desnecessaria.
- Retorno 404 correto quando o usuario nao e encontrado.

---

### Pontos de atencao (riscos, bugs, impactos)

- **(Bloqueador) Sem autenticacao nem autorizacao no endpoint** — qualquer usuario autenticado (ou nao, dependendo do middleware global) pode requisitar os dados de qualquer outro usuario apenas conhecendo o `id`. Um atacante que enumere IDs consegue exportar dados pessoais, financeiros e enderecos de qualquer conta. `src/routes/users.ts:103`

- **(Bloqueador) Dados de pagamento expostos na resposta bruta** — `paymentMethods` retornado diretamente do banco provavelmente inclui numeros de cartao (mesmo que parciais/tokenizados), CVV hasheados, tokens de gateway ou outros dados PCI-DSS. Expor esses campos em um endpoint de exportacao — sem filtragem explicita — representa risco legal e de seguranca grave. `src/routes/users.ts:107`

- **(Alto) Ausencia de rate limiting no endpoint de exportacao** — endpoints de exportacao de dados sao alvos classicos de scraping e abuso. Sem throttle, um atacante pode fazer milhares de requisicoes e exfiltrar dados de todos os usuarios. `src/routes/users.ts:103`

- **(Alto) `userId` nao validado antes de ser passado ao banco** — o parametro `req.params.id` e usado diretamente na query sem sanitizacao ou validacao de formato (ex: UUID valido). Dependendo do ORM/banco, isso pode abrir superficie para injecao ou erros de runtime inesperados. `src/routes/users.ts:103-105`

- **(Alto) Ausencia de log de auditoria** — a LGPD e boas praticas de compliance exigem rastreabilidade de acesso a dados pessoais. Nao ha nenhum registro de quem solicitou a exportacao, quando e para qual usuario. Sem isso, nao e possivel detectar abuso nem responder a incidentes. `src/routes/users.ts:103`

- **(Medio) Resposta nao e sanitizada — campos internos/sensiveis sao expostos diretamente** — `db.users.findUnique` retorna o registro completo, incluindo campos como `passwordHash`, `internalFlags`, `createdAt`, `updatedAt` e outros campos de infraestrutura que nao deveriam fazer parte de uma exportacao de portabilidade. `src/routes/users.ts:110`

- **(Medio) Sem paginacao ou limite para relacoes com muitos registros** — `orders: true` carrega todos os pedidos do usuario sem limite. Um usuario antigo com milhares de pedidos pode causar resposta gigante, estouro de memoria e timeout. `src/routes/users.ts:106`

- **(Baixo) Handler assincrono sem try/catch** — se `db.users.findUnique` rejeitar (ex: erro de conexao), a promise rejeitada nao e capturada, causando `UnhandledPromiseRejection` e provavelmente resposta 500 sem mensagem util. `src/routes/users.ts:103`

---

### Sugestoes (melhorias, refactor, estilo)

- Adicionar middleware de autorizacao antes do handler (ex: verificar que `req.user.id === userId` ou que o usuario tem role de admin) — protecao minima antes de qualquer outro ajuste.
- Criar um DTO/serializer de exportacao LGPD que liste explicitamente os campos permitidos na resposta, em vez de passar o objeto do banco diretamente.
- Registrar log de auditoria estruturado (userId solicitado, userId do requisitante, timestamp, IP de origem) para toda chamada bem-sucedida.
- Adicionar rate limiting especifico nesta rota (ex: 1 exportacao por usuario por hora).
- Validar `userId` como UUID antes de consultar o banco (ex: com `zod` ou `express-validator`).
- Envolver o handler em try/catch (ou usar wrapper de async-error como `express-async-errors`) para garantir resposta de erro controlada.
- Considerar retornar a exportacao como download (Content-Disposition: attachment) com Content-Type correto, tornando o comportamento mais explicito para o usuario final.

---

### Testes e validacoes recomendadas

- [ ] Testar acesso com usuario nao autenticado — deve retornar 401.
- [ ] Testar acesso de usuario autenticado tentando exportar dados de outro usuario — deve retornar 403.
- [ ] Verificar que nenhum campo sensivel (passwordHash, dados PCI) aparece na resposta.
- [ ] Testar com usuario que possui grande volume de pedidos — verificar tempo de resposta e tamanho do payload.
- [ ] Verificar que o log de auditoria e gravado em toda requisicao ao endpoint.
- [ ] Testar com `id` invalido (nao-UUID, string vazia, SQL injection basico) — deve retornar erro controlado, nao 500.
- [ ] Verificar comportamento quando o banco esta indisponivel — nao deve vazar stack trace na resposta.

---

### Perguntas

- Qual e o mecanismo de autenticacao atual das rotas de usuarios? Ha middleware global de auth ou cada rota precisa aplicar individualmente?
- O modelo `paymentMethods` armazena dados brutos de cartao ou apenas tokens de gateway (ex: Stripe token)? Isso muda a criticidade do item de exposicao PCI.
- Ha requisito de formato especifico para exportacao LGPD (ex: JSON estruturado com campos em portugues, CSV, arquivo ZIP)? A legislacao nao impoe formato, mas o produto pode ter definido um padrao.
- Existe um sistema de auditoria centralizado (ex: tabela de audit logs, servico externo) onde esse evento deve ser registrado?
