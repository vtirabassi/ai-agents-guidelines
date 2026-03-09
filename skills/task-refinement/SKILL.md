---
name: task-refinement
description: Refinar demandas tecnicas em backlog executavel com escopo claro, dependencias mapeadas, riscos, criterio de aceite verificavel e plano incremental de entrega. Use esta skill sempre que houver card pouco claro, requisito incompleto, ou necessidade de preparar implementacao para sprint. Tambem acione quando o usuario disser "refina esse card", "prepara pra sprint", "esse requisito ta vago", "quebra essa task", "grooming", "refinamento", "detalha essa demanda", ou quando colar descricao de tarefa que precisa de estruturacao.
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
3. Avaliar tamanho da demanda: se o card descreve o que na pratica e um epico (multiplas entregas independentes, times diferentes, semanas de trabalho), sinalizar ao usuario e sugerir quebra em cards menores antes de refinar. Refinar um epico como se fosse um card unico gera planos que ninguem consegue executar.
4. Quebrar a entrega em incrementos pequenos com valor parcial.
5. Identificar dependencias tecnicas e de negocio, com dono e ordem.
6. Mapear riscos relevantes e mitigacoes praticas.
7. Definir criterios de aceite funcionais e tecnicos verificaveis.
8. Propor validacao, rollout e plano de rollback proporcional ao risco.
9. Se faltarem dados criticos, listar perguntas objetivas no final.

## Restricoes
- Nao assumir requisito inexistente sem marcar como suposicao — suposicoes escondidas sao a maior causa de retrabalho; quando uma suposicao falha no meio da sprint, o time perde dias.
- Nao gerar plano sem criterio de aceite verificavel — sem criterio claro, o "pronto" vira subjetivo e causa atrito entre dev e produto.
- Nao misturar refactor amplo com entrega urgente sem justificativa — isso dilui o foco e aumenta o risco de ambas as entregas.
- Nao deixar dependencias implicitas quando impactarem prazo — dependencias nao mapeadas sao bloqueios que so aparecem no pior momento.

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

## Exemplo

**Input (card vago):**
"Melhorar performance do dashboard"

**Output:**

### Problema e objetivo
- Problema: Dashboard de metricas operacionais leva >8s para carregar, causando abandono por parte dos analistas e atraso na deteccao de anomalias.
- Objetivo: Reduzir tempo de carregamento para <2s no p95, sem alterar funcionalidades existentes.

### Escopo
- Dentro: otimizacao de queries, cache de dados agregados, lazy loading de graficos abaixo da dobra
- Fora: redesign visual, novas metricas, migracao de banco

### Plano incremental
- Etapa 1 (menor entrega viavel): adicionar indice nas 3 queries mais lentas (identificadas via slow query log) — impacto estimado: -40% no tempo de resposta
- Etapa 2: implementar cache de 5min para dados agregados que nao mudam em tempo real
- Etapa 3: lazy loading dos graficos abaixo da dobra para reduzir payload inicial

### Riscos e dependencias
- Risco: indice novo pode impactar performance de escrita | Mitigacao: testar em staging com carga simulada antes de aplicar em producao
- Dependencia: acesso ao slow query log de producao | Dono: time de DBA

### Criterios de aceite
- Funcional: dashboard carrega com todos os graficos visiveis e dados corretos
- Tecnico: p95 de carregamento <2s medido via APM em producao por 24h apos deploy

### Validacao e rollout
- Testes: benchmark antes/depois com dataset de producao anonimizado
- Monitoracao: alerta se p95 > 3s apos deploy
- Rollout/rollback: feature flag por etapa, rollback individual se regressao detectada
