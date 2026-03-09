---
name: incident-status-report
description: Gerar status report padronizado e objetivo para incidentes (INC), pronto para compartilhamento rapido em canais operacionais. Use esta skill sempre que o usuario pedir atualizacao de incidente, follow-up, resumo de andamento, status de mitigacao, mensagem curta para alinhar stakeholders, update para war room, ou disser coisas como "manda update do incidente", "preciso de um follow-up", "resumo pro canal", "status da INC", "o que ta acontecendo com o incidente". Tambem acione quando o usuario mencionar numero de INC com contexto de comunicacao.
---

## Entradas esperadas
- Numero do incidente (ex.: `INC12345`)
- Status atual (`Investigando`, `Mitigado`, `Resolvido`, `Monitorando`)
- Problema descrito (sintoma + impacto)
- Analise atual (hipotese, achados, proximo passo)
- Link do sistema de incidentes (opcional)
- Formato desejado: `compacto` (padrao) ou `expandido` (opcional)

## Processo
1. Validar os 4 campos obrigatorios antes de redigir a saida. Se faltar algum, perguntar de forma direta — cada campo existe porque stakeholders precisam de informacao completa para tomar decisoes operacionais.
2. Se houver link, usar apenas para confirmar/complementar dados ja informados.
3. Se o link nao puder ser acessado, continuar com o que ja foi fornecido e sinalizar lacuna objetiva.
4. Normalizar linguagem para frases curtas, factuais e acionaveis.
5. Priorizar estado atual, impacto real e proximo passo operacional.
6. Se houver transicao de status (ex.: de `Investigando` para `Mitigado`), destacar o que mudou e qual acao causou a transicao.
7. Entregar no formato solicitado (compacto ou expandido).

## Restricoes
- Nao inventar dados de incidente — informacao falsa em contexto operacional pode levar a decisoes erradas e atrasar a resolucao real.
- Nao inferir causa raiz sem evidencia — atribuir causa sem dados gera falsa confianca e pode desviar a investigacao.
- Nao omitir campo obrigatorio sem explicitar o que falta.
- Nao assumir informacao de link indisponivel.
- Nao usar linguagem vaga ("aparentemente", "talvez") sem contexto — stakeholders precisam de clareza para agir.

## Formato de saida

### Formato compacto (padrao)
Uma unica linha, ideal para canais de operacao e updates rapidos:

`<INC> <Status> <Problema descrito> Analise atual: <analise atual>`

### Formato expandido (war room / post-mortem / alinhamento detalhado)
Use quando o usuario pedir mais detalhes ou contexto de war room:

**Incidente:** `<INC>` | **Status:** `<Status>`
**Impacto:** <descricao do impacto com metricas quando disponiveis>
**Problema:** <sintoma observado>
**Analise atual:** <hipotese, achados, proximos passos>
**Ultima acao:** <o que foi feito mais recentemente>
**Proximo passo:** <acao imediata planejada com responsavel, se conhecido>
**Atualizacao anterior:** <resumo do ultimo update, se houver>

## Exemplos

**Exemplo 1 — Formato compacto:**

Input: INC27491, Monitorando, latencia no checkout afetando 18% das transacoes, rollback parcial aplicado e acompanhando metricas por 30 min

Output:
`INC27491 Monitorando Latencia elevada no checkout afetando 18% das transacoes Analise atual: mitigacao aplicada com rollback parcial e acompanhamento de metricas por 30 min`

**Exemplo 2 — Formato expandido (transicao de status):**

Input: INC31022, mudou de Investigando para Mitigado, API de pagamento retornando timeout para 40% dos requests, identificado connection pool saturado, aumentamos pool de 50 para 200 conexoes

Output:

**Incidente:** `INC31022` | **Status:** Mitigado (antes: Investigando)
**Impacto:** 40% dos requests da API de pagamento com timeout
**Problema:** Connection pool do banco de dados saturado
**Analise atual:** Causa identificada como subdimensionamento do connection pool (50 conexoes para carga atual)
**Ultima acao:** Pool aumentado de 50 para 200 conexoes — timeout rate caiu para <1%
**Proximo passo:** Monitorar por 1h e avaliar se o dimensionamento e suficiente para pico
