---
name: incident-status-report
description: Gerar status report padronizado e objetivo para incidentes (INC), pronto para compartilhamento rapido em canais operacionais. Use esta skill sempre que o usuario pedir atualizacao de incidente, follow-up, resumo de andamento, status de mitigacao, ou mensagem curta para alinhar stakeholders.
---

## Entradas esperadas
- Numero do incidente (ex.: `INC12345`)
- Status atual (`Investigando`, `Mitigado`, `Resolvido`, `Monitorando`)
- Problema descrito (sintoma + impacto)
- Analise atual (hipotese, achados, proximo passo)
- Link do sistema de incidentes (opcional)

## Processo
1. Validar os 4 campos obrigatorios antes de redigir a saida.
2. Se houver link, usar apenas para confirmar/complementar dados ja informados.
3. Se o link nao puder ser acessado, continuar com o que ja foi fornecido e sinalizar lacuna objetiva.
4. Normalizar linguagem para uma frase curta, factual e acionavel.
5. Priorizar estado atual, impacto real e proximo passo operacional.
6. Entregar no formato final padronizado.

## Restricoes
- Nao inventar dados de incidente.
- Nao inferir causa raiz sem evidencia.
- Nao omitir campo obrigatorio sem explicitar o que falta.
- Nao assumir informacao de link indisponivel.
- Nao usar linguagem vaga ("aparentemente", "talvez") sem contexto.

## Formato de saida
Entregar em uma linha, neste formato:

`<INC> <Status> <Problema descrito> Analisa atual: <analise atual>`

Observacao: usar exatamente o rotulo `Analisa atual` quando o solicitante pedir esse formato literal.

## Exemplo de saida
`INC27491 Monitorando Latencia elevada no checkout afetando 18% das transacoes Analisa atual: mitigacao aplicada com rollback parcial e acompanhamento de metricas por 30 min`
