# IA, Agentes e Claude Code

Material introdutorio para engenharia sobre uso pratico de IA com agentes.

## Comeco rapido
Para adocao imediata no time, ler primeiro:
1. `docs/getting-started.md`
2. `docs/skills.md`
3. `docs/agents.md`
4. `docs/rules.md`

## Por que falar de IA agora?
- IA ja esta sendo usada, com ou sem padrao.
- Sem processo, o risco cresce.
- Com processo, o ganho escala.

O ponto nao e se vamos usar IA, e como vamos usar.

## IA como amplificador, nao substituto
- IA nao entende o negocio sozinha.
- IA nao assume responsabilidade.
- IA nao decide por conta propria.

O julgamento tecnico continua humano.

## Casos reais em engenharia
- Code review de PR.
- Entendimento de codigo legado e escrita tecnica.
- Debugging e desenvolvimento.
- Comunicacao tecnica clara.

## Modelos disponiveis (maio 2026)
Os modelos Claude operam em tres tiers:

- **Opus** (maior capacidade): Claude Opus 4.7 — modelo mais capaz disponivel publicamente, com adaptive thinking e foco em coding agentivo e workflows de longa duracao.
- **Sonnet** (equilibrio custo/performance): Claude Sonnet 4.6 — recomendado para a maioria das tarefas de desenvolvimento.
- **Haiku** (velocidade e custo): Claude Haiku 4.5 — respostas rapidas e automacoes de alta frequencia.

> Modelos mais antigos (Claude 3.x) foram ou estao sendo descontinuados. Migrar para a familia Claude 4.x.

## Por que Claude Code?
- Trabalha no codigo local, com acesso a arquivos, historico e diffs.
- Suporta MCP para integracoes com sistemas externos.
- Permite padronizar agentes e skills via AGENTS.md e arquivos de configuracao.
- Disponivel como CLI (`claude`) e interface web.

Referencia: https://claude.ai/code

## Fluxo de uso no dia a dia
1. Abrir repositorio local.
2. Selecionar skill/agent adequado.
3. Fornecer contexto explicito (arquivos, linhas, diff).
4. Interagir de forma iterativa.
5. Validar sugestoes com revisao humana e testes.

## Referencias
- Claude Code Docs: https://docs.anthropic.com/en/docs/claude-code
- Modelos Claude: https://docs.anthropic.com/en/docs/about-claude/models
- Guia de agents: `docs/agents.md`
- Guia de skills: `docs/skills.md`
