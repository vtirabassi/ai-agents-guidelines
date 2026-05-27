---
name: pr-review
description: Revisar Pull Requests com foco em corretude, risco de regressao, seguranca, performance e cobertura de testes, retornando feedback priorizado e acionavel. Use esta skill sempre que houver diff de PR para avaliacao tecnica, especialmente antes de merge. Tambem acione quando o usuario disser "revisa esse PR", "olha esse diff", "review antes de mergear", "code review", "da uma olhada nesse PR", colar link de PR, ou pedir opiniao sobre mudancas de codigo em contexto de pull request.
---

## Entradas esperadas
- Titulo e descricao do PR (incluindo motivacao e o que muda)
- Diff do PR (arquivos e trechos alterados)
- Padroes do repositorio (linters, convencoes, arquitetura, guidelines), se disponiveis

## Processo
1. Resumir em 1-2 frases o objetivo do PR com base em descricao e diff.
2. Mapear arquivos afetados e tipo da mudanca (feature, bugfix, refactor, chore).
3. Para PRs grandes (>500 linhas de diff), agrupar mudancas por area logica e revisar cada grupo separadamente. Isso evita perder detalhes importantes em diffs extensos.
4. Avaliar corretude:
   - null/undefined e estados invalidos
   - indices/limites e condicoes de borda
   - fluxo assincrono, ordem de efeitos e concorrencia
   - tratamento de erro, retry, rollback, idempotencia
5. Avaliar design e manutencao:
   - coesao, responsabilidades e acoplamento
   - duplicacao e oportunidade de extracao
   - legibilidade, nomes e complexidade
6. Avaliar performance:
   - loops e custo algoritmico
   - I/O remoto, consultas repetidas, N+1
   - cache/reuso e trabalho desnecessario
7. Avaliar seguranca:
   - autenticacao/autorizacao
   - validacao/sanitizacao de entrada
   - exposure de dados sensiveis e secrets
   - superficie de injecao e abuso
8. Avaliar testes e observabilidade:
   - cenarios criticos cobertos
   - lacunas de regressao
   - logs/metricas/traces para diagnostico
9. Classificar cada achado por impacto: `Bloqueador`, `Alto`, `Medio`, `Baixo`.
10. Priorizar a apresentacao: bloqueadores e altos primeiro — o autor precisa saber imediatamente o que impede o merge, antes de gastar tempo em ajustes cosmeticos.
11. Sugerir validacoes objetivas para reduzir risco antes do merge.

## Restricoes
- Nao aprovar/reprovar PR com decisao final automatica — a decisao de merge envolve contexto de negocio e urgencia que so o time tem.
- Nao editar arquivo nem gerar patch automatico sem solicitacao.
- Nao inventar contexto de negocio; perguntar quando houver ambiguidade — suposicoes erradas geram sugestoes irrelevantes que o autor precisa filtrar.
- Nao sugerir comandos destrutivos ou inseguros.
- Nao ser pedante com estilo quando o repositorio ja tem linter configurado — confiar nas ferramentas existentes para questoes de formatacao.

## Formato de saida
Use sempre esta estrutura:

### Resumo
- 1-2 frases sobre o que o PR faz e seu contexto

### Pontos positivos
- ...

### Pontos de atencao (riscos, bugs, impactos)
- (Bloqueador|Alto|Medio|Baixo) - descricao + impacto + onde aparece (`arquivo:linha` quando possivel)

### Sugestoes (melhorias, refactor, estilo)
- ...

### Testes e validacoes recomendadas
- ...

### Perguntas (se necessario)
- Usar apenas se faltar contexto para revisar com confianca.

## Exemplo

**Input:** PR que adiciona rate limiting em endpoint de login

**Achado de exemplo:**

### Pontos de atencao (riscos, bugs, impactos)
- (Alto) Rate limit usa IP do cliente sem considerar header `X-Forwarded-For` — em ambiente com load balancer, todos os requests terao o mesmo IP e o rate limit sera aplicado globalmente ao inves de por usuario. `src/middleware/rateLimiter.ts:23`
- (Medio) Contador de tentativas nao e resetado apos login bem-sucedido — usuario legitimo que errou senha 4x fica bloqueado mesmo apos acertar. `src/auth/loginHandler.ts:47`
- (Baixo) Magic number `100` para limite de requests — extrair para configuracao facilita ajuste sem deploy. `src/middleware/rateLimiter.ts:15`
