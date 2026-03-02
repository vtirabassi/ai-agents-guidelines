---
name: incident-status-report
description: Gerar status report objetivo sobre tratamento de incidente (INC) com formato padronizado de uma linha. Usar quando o usuario pedir atualizacao de incidente, follow-up de INC, resumo de andamento ou status para compartilhamento rapido.
---

## Entradas esperadas
- Numero do INC (ex.: INC12345)
- Status atual (ex.: Investigando, Mitigado, Resolvido, Monitorando)
- Problema descrito (sintoma e impacto)
- Analise atual (hipotese, achados, proximo passo)
- Link do Taylor do INC (opcional)

## Processo
1. Validar se os 4 campos obrigatorios foram informados.
2. Se houver link do Taylor, usar o conteudo disponivel para preencher ou confirmar os campos.
3. Se o link nao puder ser acessado ou estiver incompleto, pedir complemento objetivo antes de concluir.
4. Normalizar texto para manter resposta curta, clara e acionavel.
5. Entregar o status no formato padrao.

## Restricoes
- Nao inventar dados de incidente.
- Nao inferir causa raiz sem evidencia.
- Nao omitir campo obrigatorio.
- Nao assumir informacao do Taylor quando o acesso ao link falhar.

## Formato de saida
Entregar nesse formato:

`<Numero do INC> 
<Status> 
<Problema Descrito> 
<Analisa atual>`

Observacao: usar exatamente o rotulo `Analisa atual` quando o solicitante pedir esse formato literal.

## Exemplo de saida
`INC27491 Monitorando Latencia elevada no checkout afetando 18% das transacoes Analisa atual: mitigacao aplicada com rollback parcial e acompanhamento de metricas por 30 min`
