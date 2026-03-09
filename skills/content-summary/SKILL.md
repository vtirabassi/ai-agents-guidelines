---
name: content-summary
description: Resumir e estruturar conteudo longo de forma clara, fiel e acionavel. Use esta skill sempre que o usuario pedir resumo, sintese, TL;DR, explicacao rapida, guia de estudo, perguntas de revisao, flashcards, plano de estudo, ou quando houver muito conteudo para condensar. Tambem acione quando o usuario colar texto longo, compartilhar URL, ou disser coisas como "me explica isso", "o que esse texto fala", "principais pontos", "faz um resumo disso", "quero entender esse artigo", mesmo que nao use a palavra "resumo" explicitamente.
---

## Entradas esperadas
- Conteudo em texto bruto, anotacoes, artigo, documento convertido em texto, ou URL
- Objetivo principal (estudo, trabalho, apresentacao, revisao rapida, decisao)
- Nivel de profundidade desejado (`rapido`, `curto`, `medio`, `longo`)
- Idioma de resposta (se nao informado, seguir o idioma do usuario)

## Processo
1. Identificar idioma, tema central, publico e objetivo pratico do usuario.
2. Se o conteudo estiver em idioma diferente do usuario, resumir no idioma do usuario e sinalizar que o original esta em outro idioma.
3. Se houver URL, extrair apenas o conteudo principal e ignorar navegacao, anuncios, widgets e rodape.
4. Se a URL nao puder ser acessada, pedir o texto colado e continuar imediatamente com o material disponivel.
5. Se o usuario nao informar profundidade, inferir pelo contexto: perguntas rapidas sugerem `curto`, pedidos de estudo sugerem `medio` ou `longo`.
6. Selecionar profundidade:
   - `rapido`: ate 10 bullets totais, foco em decisao imediata
   - `curto`: TL;DR + pontos-chave essenciais
   - `medio`: formato padrao completo (ver formato de saida)
   - `longo`: formato completo + contexto + exemplos concretos
7. Ajustar foco pelo objetivo:
   - `prova` ou `estudo`: definicoes, comparacoes, memorias de alto valor, perguntas de revisao
   - `trabalho` ou `apresentacao`: argumentos, dados, implicacoes e narrativa
   - `decisao`: riscos, trade-offs e recomendacao objetiva
8. Preservar sentido original do autor e separar claramente fatos, inferencias e lacunas.
9. Entregar no formato de saida definido abaixo, com linguagem direta e util.

## Restricoes
- Nao inventar fatos, numeros, fontes ou conclusoes — o usuario pode tomar decisoes baseado no resumo sem verificar cada ponto, entao qualquer fabricacao gera risco real.
- Nao distorcer a tese original para simplificar — um resumo que muda o argumento do autor e pior do que nenhum resumo.
- Nao ocultar incertezas: explicitar em uma linha quando faltar contexto, porque o usuario precisa saber o que esta incompleto para buscar mais informacao.
- Nao interromper a tarefa por indisponibilidade de URL se houver texto alternativo.
- Nao exceder o nivel de detalhe pedido pelo usuario.

## Formato de saida
Use este formato por padrao (profundidade `medio`):

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

## Exemplo

**Input:** Usuario cola um artigo de 2000 palavras sobre microsservicos vs monolitos e pede "resume pra mim, to estudando pra entrevista" (profundidade inferida: `medio`, objetivo: `estudo`)

**Output:**

### TL;DR
- Microsservicos dividem a aplicacao em servicos independentes com deploy separado. Monolitos mantem tudo junto, o que simplifica o inicio mas dificulta escala. A escolha depende do tamanho do time e da maturidade operacional.

### Pontos-chave
- Monolitos sao mais simples de desenvolver, testar e deployar no inicio
- Microsservicos permitem escalar e deployar servicos individuais
- A comunicacao entre microsservicos adiciona latencia e complexidade
- Monolitos podem se tornar dificeis de manter com crescimento do time
- Microsservicos exigem infraestrutura madura (CI/CD, observabilidade, service mesh)

### Conceitos importantes
- `service mesh`: camada de infraestrutura que gerencia comunicacao entre servicos
- `bounded context`: limite logico que define a responsabilidade de cada servico

### Resumo expandido
O artigo argumenta que a decisao entre microsservicos e monolitos nao e tecnica, mas organizacional. Times pequenos se beneficiam da simplicidade do monolito. A transicao para microsservicos faz sentido quando o monolito vira gargalo para multiplos times.

### Perguntas de revisao
- Qual a principal desvantagem de microsservicos para times pequenos?
- O que e um bounded context e por que e importante na separacao de servicos?
- Quando faz sentido migrar de monolito para microsservicos?

### Proximos passos
- Estudar patterns de comunicacao entre servicos (sync vs async)
- Revisar conceitos de CAP theorem e consistencia eventual
- Praticar explicar trade-offs em voz alta (simulacao de entrevista)

## Checklist de qualidade
- Fidelidade ao conteudo original
- Clareza sem jargao desnecessario
- Priorizacao do que gera acao
- Sinalizacao explicita de limites e lacunas
