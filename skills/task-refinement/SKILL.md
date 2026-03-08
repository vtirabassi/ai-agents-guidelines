---
name: task-refinement
description: Refinar demandas tecnicas em backlog executavel com escopo claro, dependencias mapeadas, riscos, criterio de aceite verificavel e plano incremental de entrega. Use esta skill sempre que houver card pouco claro, requisito incompleto ou necessidade de preparar implementacao para sprint.
---

## Entradas esperadas
- Descricao inicial da task ou card
- Objetivo de negocio e impacto esperado
- Contexto tecnico (sistema afetado, restricoes, arquitetura)
- Prazo, janela de entrega ou urgencia
- Dependencias conhecidas (times, sistemas, aprovacoes), se houver

## Processo
1. Reescrever problema em linguagem objetiva e testavel.
2. Separar escopo em `Dentro` e `Fora` para evitar expansao.
3. Quebrar a entrega em incrementos pequenos com valor parcial.
4. Identificar dependencias tecnicas e de negocio, com dono e ordem.
5. Mapear riscos relevantes e mitigacoes praticas.
6. Definir criterios de aceite funcionais e tecnicos verificaveis.
7. Propor validacao, rollout e plano de rollback proporcional ao risco.
8. Se faltarem dados criticos, listar perguntas objetivas no final.

## Restricoes
- Nao assumir requisito inexistente sem marcar como suposicao.
- Nao gerar plano sem criterio de aceite verificavel.
- Nao misturar refactor amplo com entrega urgente sem justificativa.
- Nao deixar dependencias implicitas quando impactarem prazo.

## Formato de saida
### Problema e objetivo
- Problema: ...
- Objetivo: ...

### Escopo
- Dentro: ...
- Fora: ...

### Plano incremental
- Etapa 1 (menor entrega viavel): ...
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

### Perguntas em aberto (se necessario)
- ...
