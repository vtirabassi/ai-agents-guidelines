---
name: content-summary
description: Resumir materias, textos longos e conteudos de URLs de forma clara, didatica e acionavel. Usar quando o usuario compartilhar texto, artigo, anotacao ou link e pedir resumo, sintese, explicacao rapida, pontos-chave, guia de estudo, perguntas de revisao, flashcards ou plano de estudo.
---

## Entradas esperadas
- Conteudo em texto, anotacao, artigo ou URL
- Objetivo do usuario (estudo, trabalho, apresentacao, revisao rapida)
- Nivel de profundidade desejado (curto, medio, longo)
- Idioma preferido de resposta (opcional)

## Processo
1. Identificar idioma, tema central e objetivo do usuario.
2. Se for URL, extrair o conteudo principal e ignorar navegacao, anuncios e rodape.
3. Se nao conseguir acessar a URL, pedir o texto colado e continuar com o material recebido.
4. Definir profundidade com base no pedido:
- `rapido`: no maximo 10 bullets no total
- `curto`: TL;DR + pontos-chave
- `medio`: estrutura padrao completa
- `longo`: estrutura padrao + contexto adicional e exemplos
5. Adaptar o foco conforme objetivo informado:
- `prova`: definicoes, comparacoes, pegadinhas e memorizacao
- `trabalho` ou `apresentacao`: argumentos, dados e narrativa
6. Entregar a resposta no formato de saida apropriado.

## Restricoes
- Nao inventar fatos ou conclusoes nao suportadas pelo conteudo.
- Nao distorcer a intencao original do autor.
- Nao esconder lacunas; explicitar incerteza quando faltar contexto.
- Nao bloquear a tarefa por falta de acesso a URL se houver texto alternativo.

## Formato de saida
Usar por padrao:

### TL;DR
- 2-4 linhas

### Pontos-chave
- 5-10 bullets

### Conceitos importantes
- termo: explicacao curta

### Resumo expandido
- 1-3 paragrafos

Se o objetivo for estudo ou se o usuario pedir explicitamente:

### Perguntas de revisao
- 3-5 perguntas

### Proximos passos
- como estudar, aplicar ou aprofundar

## Qualidade e seguranca
- Preservar precisao e neutralidade.
- Declarar suposicoes em 1 linha quando houver ambiguidade.
- Solicitar complemento objetivo quando a base estiver incompleta.
- Priorizar clareza e utilidade pratica no resultado final.
