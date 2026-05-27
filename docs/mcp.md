# MCP (Model Context Protocol)

## O que e?
MCP e um protocolo aberto que padroniza como modelos de IA:
- Descobrem ferramentas e recursos externos
- Acessam dados de sistemas reais
- Executam acoes de forma segura e auditavel

Ele define:
- Um padrao aberto de integracao (chamadas de ferramenta, recursos, prompts)
- Como o modelo descobre, chama e usa recursos externos
- Integracoes consistentes entre apps, APIs e bases de dados

> MCP para IA e o que HTTP + APIs sao para sistemas distribuidos.

## Status atual (2026)
MCP deixou de ser experimento e se tornou padrao de mercado:

- **Adocao ampla**: suportado nativamente em Claude, ChatGPT, Cursor, Gemini, Microsoft Copilot e VS Code.
- **Governanca aberta**: em dezembro de 2025, a Anthropic doou o MCP para a **Agentic AI Foundation (AAIF)** sob a Linux Foundation. OpenAI, AWS, Google, Microsoft e Cloudflare sao membros fundadores.
- **Escala de uso**: mais de 10.000 servidores MCP ativos e 97 milhoes de downloads mensais do SDK (Python + TypeScript).

## Versao atual da spec (novembro 2025)
A ultima especificacao oficial traz mudancas significativas:

- **Operacoes assincronas (Tasks)**: servidores podem iniciar tarefas de longa duracao; agentes desconectam, reconectam e buscam resultados depois. Essencial para workflows reais como indexacao e relatorios.
- **Streaming HTTP**: servidores MCP agora rodam como servicos remotos, nao apenas processos locais.
- **Autorizacao moderna (OAuth 2.0)**: servidores sao classificados como OAuth Resource Servers; clientes devem implementar Resource Indicators (RFC 8707) para prevenir token mis-redemption.
- **MCP Registry**: catalogo oficial e API para descoberta de servidores MCP (lancado em preview em setembro de 2025).
- **SDK Tiering**: sistema de classificacao de SDKs por nivel de conformidade com a spec e manutencao.
- **Governance formal**: Working Groups, Interest Groups e processo de SEP (Spec Enhancement Proposal) para contribuicoes.

## Na pratica
O MCP facilita integrar modelos de IA com sistemas reais:
- CRM, ERP, bancos de dados
- Repositorios de codigo e CI/CD
- SaaS corporativos (Jira, Slack, Notion)
- Sistemas internos via API

## Consideracoes de seguranca
Com maior adocao vieram riscos documentados:
- **Prompt injection via ferramentas**: servidores maliciosos podem injetar instrucoes no contexto do agente.
- **Escopo de acesso excessivo**: frameworks que facilitam acesso amplo exigem governance explicita.
- **Auditoria de acoes**: todo tool call deve ser logado para rastreabilidade e compliance.

> Avaliar cada servidor MCP antes de conectar ao agente. Tratar como dependency de seguranca, nao apenas integracao tecnica.

## Referencias
- Spec oficial: https://modelcontextprotocol.io/specification/2025-11-25
- MCP Registry: https://registry.modelcontextprotocol.io
- Blog MCP: https://blog.modelcontextprotocol.io
- AAIF (Linux Foundation): https://lfaidata.foundation/projects/agentic-ai-foundation/
