---
name: task-refinement
description: Refinar tasks tecnicas em backlog executavel com escopo claro, riscos, dependencias, criterio de aceite e plano de entrega incremental. Usar quando houver card/vaga descricao de demanda e for necessario preparar implementacao para sprint.
---

## Entradas esperadas
- Descricao inicial da task ou card
- Objetivo de negocio e impacto esperado
- Contexto tecnico (sistema afetado, restricoes, arquitetura)
- Prazo ou janela de entrega

## Processo
1. Reescrever problema em linguagem objetiva e testavel.
2. Separar escopo em dentro/fora para evitar expansao.
3. Quebrar entrega em etapas incrementais pequenas.
4. Identificar dependencias tecnicas e de negocio.
5. Mapear riscos e definir mitigacoes praticas.
6. Definir criterio de aceite funcional e tecnico.
7. Sugerir plano de validacao (testes, monitoracao, rollout).

## Restricoes
- Nao assumir requisito inexistente sem marcar como suposicao.
- Nao gerar plano sem criterio de aceite verificavel.
- Nao misturar refactor amplo com entrega urgente sem justificativa.

## Formato de saida
### Problema e objetivo
- Problema: ...
- Objetivo: ...

### Escopo
- Dentro: ...
- Fora: ...

### Plano incremental
- Etapa 1: ...
- Etapa 2: ...
- Etapa 3: ...

### Riscos e dependencias
- Risco: ... | Mitigacao: ...
- Dependencia: ... | Dono: ...

### Criterios de aceite
- Funcional: ...
- Tecnico: ...

### Validacao e rollout
- Testes: ...
- Monitoracao: ...
- Rollout/rollback: ...
