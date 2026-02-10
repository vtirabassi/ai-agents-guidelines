# Agents – OpenCode

Esta pasta contém os **agentes padrão de IA utilizados pelo time de Engenharia da XP**, configurados para uso com o OpenCode.

Os agentes têm como objetivo:
- padronizar o uso de IA no time
- reduzir variação de respostas
- transformar boas práticas em processo reutilizável

Cada agente define:
- objetivo e skills
- escopo e nao-escopo
- forma de resposta e criterios de sucesso
- quando usar e limites

---

## Como usar

1. Abrir o repositório local
2. Iniciar o OpenCode
3. Selecionar o agente desejado
4. Fornecer contexto explícito (arquivos, linhas, diff)
5. Interagir de forma iterativa
6. Validar criticamente todas as sugestões

> A responsabilidade final pela decisão técnica é sempre do engenheiro.

---

## Frontmatter padrao

Todos os agents seguem um frontmatter minimo para facilitar a configuracao:

```yaml
---
description: <descricao curta e objetiva do agent>
mode: subagent
temperature: 0.3
tools:
  write: false
  edit: false
  bash: false
---
```

Use `description` para resumir o papel do agent em uma linha.

---

## Agentes disponíveis

| Agente            | Objetivo principal                                       |
|-------------------|----------------------------------------------------------|
| code-reviewer     | Revisao critica de codigo antes do merge                 |
| comunicacao       | Revisao e resposta de textos em PT-BR                    |
| mentor-senior     | Mentoria tecnica e orientacao arquitetural               |
| security-checker  | Identificacao de riscos de seguranca                     |

---

## Padrões

Todos os agentes seguem os princípios:
- clareza
- objetividade
- foco em engenharia
- alinhamento com práticas da XP
