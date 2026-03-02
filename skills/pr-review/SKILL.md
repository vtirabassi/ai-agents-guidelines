---
name: pr-review
description: Revisar Pull Requests com checklist tecnico consistente (corretude, riscos, seguranca, performance, testes) e gerar feedback estruturado em prioridades. Usar quando houver diff, titulo e contexto de PR para avaliacao rapida e padronizada sem editar codigo.
---

## Entradas esperadas
- Titulo e descricao do PR (incluindo motivacao e o que muda)
- Diff do PR (arquivos e trechos alterados)
- Padroes relevantes do repo (linters, conventions, arquitetura, guidelines), se existirem

## Processo
1. Resumir em 1-2 frases o objetivo do PR com base em descricao e diff.
2. Mapear arquivos afetados e tipo da mudanca (feature, bugfix, refactor, chore).
3. Avaliar corretude:
- null/undefined
- indices e limites
- fluxo assincrono e concorrencia
- tratamento de erro e rollback
- efeitos colaterais inesperados
4. Avaliar design e manutencao:
- coesao e separacao de responsabilidades
- duplicacao
- nomes e legibilidade
- complexidade acidental
5. Avaliar performance:
- loops pesados
- chamadas remotas e I/O
- N+1
- cache e reuso
6. Avaliar seguranca:
- autenticacao/autorizacao
- validacao/sanitizacao
- exposicao de secrets
- logging sensivel
- superficie de injecao
7. Avaliar testes e observabilidade:
- cobertura de cenarios criticos
- testes de regressao
- logs/metricas/traces quando necessario
8. Priorizar feedback por impacto (Bloqueador, Alto, Medio, Baixo).

## Restricoes
- Nao aprovar/reprovar PR (sem LGTM final).
- Nao alterar arquivo nem gerar patch automatico.
- Nao inventar contexto de negocio; perguntar quando houver ambiguidade.
- Nao sugerir comandos destrutivos ou inseguros.

## Formato de saida
Usar sempre esta estrutura:

### Pontos positivos
- ...

### Pontos de atencao (riscos, bugs, impactos)
- (Bloqueador|Alto|Medio|Baixo) - descricao + onde aparece (arquivo/trecho)

### Sugestoes (melhorias, refactor, estilo)
- ...

### Testes e validacoes recomendadas
- ...

### Perguntas (se necessario)
- Usar apenas se faltar contexto para revisar com confianca.
