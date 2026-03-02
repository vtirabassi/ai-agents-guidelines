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
- `code-reviewer`: revisao tecnica de PR e diffs
- `security-checker`: triagem de riscos de seguranca
- `communication`: revisao e escrita de textos em PT-BR
- `senior-mentor`: apoio a decisoes tecnicas com trade-offs
- `onboarding-helper`: guia de onboarding tecnico no repo

## Como criar novo agent
1. Copiar `agents/agent-template.md`.
2. Definir papel, limites e entrada minima.
3. Conectar `rules/` e `skills/` relevantes.
4. Testar com 2-3 cenarios reais.
5. Publicar em PR curto.

## Governanca
- Ver `agents/governance.md` para precedencia, ownership e processo de mudanca.
