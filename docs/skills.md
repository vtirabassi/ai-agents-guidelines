# Skills

## O que sao
Skills sao modulos reutilizaveis de instrucoes para tarefas tecnicas recorrentes.

Cada skill deve definir:
- o problema que resolve
- quando deve ser usada
- entradas minimas
- fluxo de execucao
- formato de saida

## Objetivo para engenharia
- acelerar tarefas repetitivas
- manter padrao tecnico entre times
- reduzir retrabalho e respostas inconsistentes

## Catalogo inicial
- `pr-review`: review tecnico padronizado de PR
- `incident-triage`: triagem de incidente de producao
- `task-refinement`: refinamento tecnico de backlog
- `content-summary`: resumo didatico e acionavel de textos e URLs

## Como criar novas skills
1. Copiar `skills/_template/`.
2. Preencher frontmatter (`name`, `description`).
3. Escrever processo objetivo e acionavel.
4. Testar com casos reais do time.
5. Ajustar por feedback e publicar em PR curto.

## Criterio de qualidade
Uma skill boa deve:
- ser focada em um problema especifico
- ter gatilho de uso claro no `description`
- produzir saida consistente e util
- evitar suposicoes nao confirmadas
