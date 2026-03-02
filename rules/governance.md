# Rules - Governance

## Objetivo
Definir precedencia, ownership e processo de evolucao das rules para manter consistencia em escala.

## Precedencia
1. `safety.md`
2. `data.md`
3. `tone.md`

Em caso de conflito, aplicar a regra de maior precedencia.

## Ownership
- Toda mudanca em rule deve ter pelo menos 1 owner tecnico responsavel.
- Owner deve revisar impactos em agents, skills e docs.

## Processo de mudanca
1. Propor alteracao em PR curto com motivacao e impacto esperado.
2. Incluir ao menos 1 exemplo "aceito" e 1 "nao aceito" na rule alterada.
3. Revisar com foco em conflitos com regras existentes.
4. Atualizar documentacao relacionada quando necessario.

## Criterios de aceite
- Texto objetivo, sem ambiguidade operacional.
- Checklist de conformidade atualizado.
- Sem conflito de precedencia.

## Versionamento
- Usar versoes semanticas simples: `vMAJOR.MINOR`.
- `MAJOR`: mudanca de comportamento obrigatorio.
- `MINOR`: ajuste incremental sem quebra de diretriz principal.
