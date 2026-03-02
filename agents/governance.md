# Agents - Governance

## Objetivo
Definir padrao de criacao e evolucao de agentes para manter consistencia em escala.

## Precedencia
1. `rules/` (limites obrigatorios)
2. contrato do agente (arquivo em `agents/`)
3. `skills/` relacionadas

Em caso de conflito, aplicar a fonte de maior precedencia.

## Ownership
- Todo agente deve ter ao menos 1 owner tecnico.
- Owner deve revisar impacto em regras, skills e documentacao.

## Processo de mudanca
1. Abrir PR com motivacao, impacto esperado e cenarios de uso.
2. Garantir formato padrao do agente.
3. Validar conflitos com outros agentes e rules.
4. Atualizar referencias em `agents/README.md` e `docs/agents.md`.

## Criterios de aceite
- Papel do agente e nao-escopo sem ambiguidade.
- Entradas e formato de resposta claros.
- Sem referencia legada quebrada.
- Ao menos 1 exemplo de uso valido.

## Versionamento
- Usar `vMAJOR.MINOR` para o catalogo de agentes.
- `MAJOR`: mudanca de comportamento obrigatorio.
- `MINOR`: ajuste incremental sem quebra de contrato.
