---
name: content-summary
description: Resumir e estruturar conteudo longo (texto colado, anotacoes, artigos, PDFs convertidos em texto e URLs) de forma clara, fiel e acionavel. Use sempre que o usuario pedir resumo, sintese, TL;DR, explicacao rapida, pontos-chave, guia de estudo, perguntas de revisao, flashcards, mapa mental, plano de estudo, ou quando houver muito conteudo para condensar em pouco tempo. Ative tambem quando o usuario compartilhar um link e pedir "me explica isso", "o que tem aqui" ou equivalentes.
---

# Content Summary

Resumir conteudo no idioma do usuario (padrao portugues), com foco em fidelidade, clareza e utilidade pratica para estudo, trabalho ou decisao.

## Entradas esperadas

- Texto colado, anotacoes, artigo, documento convertido em texto, ou URL.
- Objetivo do usuario: `estudo`, `prova`, `trabalho`, `apresentacao`, `decisao`, `revisao rapida`.
- Profundidade: `rapido`, `curto`, `medio` (padrao), `longo`.
- Tom: `didatico` (padrao), `tecnico`, `executivo`.
- Idioma de saida (padrao: idioma do usuario).

Se algo critico nao for informado, assumir padroes e seguir; nao travar pedindo parametro.

## Processo

1. Identificar idioma, tema central, publico e objetivo pratico.
2. Se houver URL, extrair apenas o conteudo principal; ignorar navegacao, anuncios, widgets, rodape e secoes de "leia tambem".
3. Se a URL nao puder ser acessada, pedir o texto colado uma vez e seguir com o que houver.
4. Aplicar profundidade:
   - `rapido`: ate 10 bullets totais; so TL;DR + pontos-chave; foco em decisao imediata.
   - `curto`: TL;DR + pontos-chave + conceitos.
   - `medio`: formato padrao completo.
   - `longo`: formato completo + contexto historico/conceitual + exemplos concretos + ligacoes externas relevantes.
5. Ajustar foco pelo objetivo:
   - `prova` / `estudo`: definicoes, comparacoes, pegadinhas, mnemonicos, perguntas de revisao, flashcards.
   - `trabalho` / `apresentacao`: argumentos, dados, narrativa, implicacoes, citacoes uteis.
   - `decisao`: trade-offs, riscos, criterios e recomendacao objetiva.
   - `revisao rapida`: so o que mudou em relacao ao senso comum.
6. Preservar numeros, datas, nomes, citacoes e termos tecnicos exatamente como no original.
7. Separar com clareza: fato do texto, inferencia sua, opiniao do autor, lacuna de contexto.
8. Entregar no formato abaixo.
9. Fechar oferecendo transformacoes uteis (ver "Fechamento padrao").

## Formato de saida (padrao `medio`)

### TL;DR
2-4 linhas com a ideia central e por que importa.

### Pontos-chave
5-10 bullets objetivos, ordenados por importancia.

### Conceitos importantes
- `termo`: explicacao curta e pratica.

### Resumo expandido
1-3 paragrafos conectando contexto, causa e implicacao.

### Perguntas de revisao *(se objetivo for estudo/prova, ou sob pedido)*
3-5 perguntas para checagem de entendimento, da mais facil para a mais profunda.

### Proximos passos *(se util)*
3-5 acoes praticas para estudar, aplicar ou aprofundar.

## Restricoes

- Nao inventar fatos, numeros, autores, fontes ou conclusoes.
- Nao distorcer a tese original para "simplificar".
- Nao ocultar incertezas: explicitar em uma linha quando faltar contexto ("O texto nao detalha X").
- Nao misturar opiniao do autor com fato sem marcar.
- Nao exceder a profundidade pedida.
- Nao interromper a tarefa por indisponibilidade de URL se houver texto alternativo.

## Checklist de qualidade (antes de entregar)

- Fidelidade ao conteudo original.
- Clareza sem jargao desnecessario.
- Priorizacao do que gera acao ou entendimento.
- Numeros, nomes e citacoes preservados.
- Lacunas e incertezas sinalizadas.
- Profundidade compativel com o pedido.

## Fechamento padrao

Encerrar com uma oferta curta, escolhendo 2-3 opcoes pertinentes ao conteudo:

> Quer que eu transforme isso em flashcards, mapa mental, plano de estudo, slides ou um resumo executivo de 1 paragrafo?
