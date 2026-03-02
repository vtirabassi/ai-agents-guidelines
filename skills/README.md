# Skills

Skills sao modulos reutilizaveis para padronizar tarefas recorrentes de engenharia.

## Objetivo
- Aumentar velocidade com qualidade consistente
- Reduzir variacao entre pessoas e times
- Capturar boas praticas em instrucoes executaveis

## Estrutura padrao
Cada skill deve ter esta estrutura minima:

```text
skills/<skill-name>/
  SKILL.md
```

Opcionalmente:

```text
skills/<skill-name>/
  SKILL.md
  scripts/
  references/
  assets/
```

## Convencoes
- Nome da pasta: lowercase com hifen (`incident-triage`)
- Frontmatter do `SKILL.md`: apenas `name` e `description`
- `description`: explique o que faz e quando usar (gatilho)
- Corpo da skill: instrucoes diretas e acionaveis, sem texto desnecessario

## Checklist de qualidade
- A skill e especifica para um problema recorrente?
- O gatilho de uso esta claro no `description`?
- O formato de saida esta explicito?
- As restricoes e limites estao documentados?
- Existe criterio de "nao inventar contexto" quando faltar dados?

## Skills atuais
- `skills/pr-review/SKILL.md`
- `skills/incident-triage/SKILL.md`
- `skills/task-refinement/SKILL.md`
- `skills/content-summary/SKILL.md`
- `skills/incident-status-report/SKILL.md`
- `skills/_template/SKILL.md` (base para novas skills)

## Fluxo de contribuicao recomendado
1. Copiar `skills/_template/`.
2. Ajustar `name`, `description` e fluxo.
3. Validar com 2-3 exemplos reais do time.
4. Revisar em PR curto.
5. Evoluir por iteracoes com feedback de uso.
