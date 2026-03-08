---
name: content-summary
description: Resumir e estruturar conteudo longo (texto colado, anotacoes, artigos e URLs) de forma clara, fiel e acionavel. Use esta skill sempre que o usuario pedir resumo, sintese, explicacao rapida, guia de estudo, perguntas de revisao, flashcards, plano de estudo, ou quando houver muito conteudo para condensar em pouco tempo.
---

## Entradas esperadas
- Conteudo em texto bruto, anotacoes, artigo, documento convertido em texto, ou URL
- Objetivo principal (estudo, trabalho, apresentacao, revisao rapida, decisao)
- Nivel de profundidade desejado (`rapido`, `curto`, `medio`, `longo`)
- Idioma de resposta (se nao informado, seguir o idioma do usuario)

## Processo
1. Identificar idioma, tema central, publico e objetivo pratico do usuario.
2. Se houver URL, extrair apenas o conteudo principal e ignorar navegacao, anuncios, widgets e rodape.
3. Se a URL nao puder ser acessada, pedir o texto colado e continuar imediatamente com o material disponivel.
4. Selecionar profundidade:
- `rapido`: ate 10 bullets totais, foco em decisao imediata
- `curto`: TL;DR + pontos-chave essenciais
- `medio`: formato padrao completo
- `longo`: formato completo + contexto + exemplos concretos
5. Ajustar foco pelo objetivo:
- `prova` ou `estudo`: definicoes, comparacoes, memorias de alto valor, perguntas de revisao
- `trabalho` ou `apresentacao`: argumentos, dados, implicacoes e narrativa
- `decisao`: riscos, trade-offs e recomendacao objetiva
6. Preservar sentido original do autor e separar claramente fatos, inferencias e lacunas.
7. Entregar no formato de saida definido abaixo, com linguagem direta e util.

## Restricoes
- Nao inventar fatos, numeros, fontes ou conclusoes.
- Nao distorcer a tese original para "simplificar".
- Nao ocultar incertezas: explicitar em uma linha quando faltar contexto.
- Nao interromper a tarefa por indisponibilidade de URL se houver texto alternativo.
- Nao exceder o nivel de detalhe pedido pelo usuario.

## Formato de saida
Use este formato por padrao:

### TL;DR
- 2-4 linhas com a ideia central e por que importa

### Pontos-chave
- 5-10 bullets objetivos

### Conceitos importantes
- `termo`: explicacao curta e pratica

### Resumo expandido
- 1-3 paragrafos conectando contexto, causa e implicacao

Quando o objetivo for estudo (ou quando o usuario pedir):

### Perguntas de revisao
- 3-5 perguntas para checagem de entendimento

### Proximos passos
- 3-5 acoes praticas para estudar, aplicar ou aprofundar

## Checklist de qualidade
- Fidelidade ao conteudo original
- Clareza sem jargao desnecessario
- Priorizacao do que gera acao
- Sinalizacao explicita de limites e lacunas
