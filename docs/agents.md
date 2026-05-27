# Agents

## O que sao
Agents sao perfis reutilizaveis que padronizam como a IA executa tarefas para o time.

Cada agent combina:
- objetivo
- entradas esperadas
- processo
- formato de resposta
- limites de atuacao

## Objetivo para engenharia
- reduzir variacao de qualidade entre interacoes
- acelerar tarefas recorrentes com menor retrabalho
- tornar comportamento da IA previsivel e auditavel

## Catalogo atual
- `code-reviewer`: revisao tecnica aprofundada de PR e diffs, com priorizacao por severidade
- `security-checker`: analise de riscos de seguranca por categoria (injecao, autorizacao, exposicao de dados, configuracao), com mitigacoes concretas

## Modelos recomendados (maio 2026)
- **Claude Opus 4.7**: tarefas complexas, agentico de longa duracao, coding avancado.
- **Claude Sonnet 4.6**: uso geral de engenharia — equilibrio ideal entre custo e qualidade.
- **Claude Haiku 4.5**: automacoes rapidas, alto volume, tarefas simples.

Modelos Claude 3.x foram descontinuados. Usar apenas familia 4.x.

## Como criar novo agent
1. Copiar `agents/agent-template.md`.
2. Definir papel, limites e entrada minima.
3. Conectar `rules/` e `skills/` relevantes.
4. Testar com 2-3 cenarios reais.
5. Publicar em PR curto.
