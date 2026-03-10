# Agents

Agentes reutilizaveis para padronizar comportamentos de IA no time de engenharia.

## Objetivo
- reduzir variacao de resposta entre pessoas
- acelerar tarefas recorrentes com qualidade consistente
- transformar boas praticas em execucao repetivel

## Arquivos
- `agents/code-reviewer.md`
- `agents/security-checker.md`
- `agents/communication.md`
- `agents/senior-mentor.md`
- `agents/agent-template.md`
- `agents/governance.md`

## Contrato minimo de cada agente
- Frontmatter valido (`description`, `mode`, `temperature`, `tools`)
- Objetivo e casos de uso claros
- Entradas esperadas
- Forma de resposta esperada
- Limites e nao-escopo
- Referencia para `rules/` e `skills/` quando aplicavel

## Fluxo de evolucao
1. Copiar `agents/agent-template.md`.
2. Definir papel e limites de forma objetiva.
3. Conectar regras e skills relevantes.
4. Testar com 2-3 cenarios reais.
5. Ajustar por feedback e publicar em PR curto.
