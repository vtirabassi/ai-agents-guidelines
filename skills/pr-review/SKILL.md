---
name: pr-review
description: Revisar Pull Requests com foco em corretude, risco de regressao, seguranca, performance e cobertura de testes, retornando feedback priorizado e acionavel. Use esta skill sempre que houver diff de PR para avaliacao tecnica estruturada, especialmente antes de merge em branch principal.
---

## Entradas esperadas
- Titulo e descricao do PR (incluindo motivacao e o que muda)
- Diff do PR (arquivos e trechos alterados)
- Padroes do repositorio (linters, convencoes, arquitetura, guidelines), se disponiveis

## Processo
1. Resumir em 1-2 frases o objetivo do PR com base em descricao e diff.
2. Mapear arquivos afetados e tipo da mudanca (feature, bugfix, refactor, chore).
3. Avaliar corretude:
- null/undefined e estados invalidos
- indices/limites e condicoes de borda
- fluxo assincrono, ordem de efeitos e concorrencia
- tratamento de erro, retry, rollback, idempotencia
4. Avaliar design e manutencao:
- coesao, responsabilidades e acoplamento
- duplicacao e oportunidade de extracao
- legibilidade, nomes e complexidade
5. Avaliar performance:
- loops e custo algoritmico
- I/O remoto, consultas repetidas, N+1
- cache/reuso e trabalho desnecessario
6. Avaliar seguranca:
- autenticacao/autorizacao
- validacao/sanitizacao de entrada
- exposure de dados sensiveis e secrets
- superficie de injecao e abuso
7. Avaliar testes e observabilidade:
- cenarios criticos cobertos
- lacunas de regressao
- logs/metricas/traces para diagnostico
8. Classificar cada achado por impacto: `Bloqueador`, `Alto`, `Medio`, `Baixo`.
9. Sugerir validacoes objetivas para reduzir risco antes do merge.

## Restricoes
- Nao aprovar/reprovar PR com decisao final automatica.
- Nao editar arquivo nem gerar patch automatico sem solicitacao.
- Nao inventar contexto de negocio; perguntar quando houver ambiguidade.
- Nao sugerir comandos destrutivos ou inseguros.

## Formato de saida
Use sempre esta estrutura:

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
