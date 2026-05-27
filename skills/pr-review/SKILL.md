---
name: pr-review
description: Revisar Pull Requests com foco em corretude, risco de regressao, seguranca, performance e cobertura de testes, retornando feedback priorizado e acionavel. Use esta skill sempre que houver diff de PR para avaliacao tecnica, especialmente antes de merge. Tambem acione quando o usuario disser "revisa esse PR", "olha esse diff", "review antes de mergear", "code review", "da uma olhada nesse PR", colar link de PR, ou pedir opiniao sobre mudancas de codigo em contexto de pull request. Se o usuario colar um diff ou trecho de codigo pedindo opiniao tecnica, acione esta skill mesmo sem mencionar PR explicitamente.
---

## Entradas esperadas
- Titulo e descricao do PR (motivacao e o que muda)
- Diff do PR (arquivos e trechos alterados)
- Padroes do repositorio (linters, convencoes, arquitetura), se disponiveis

## Escala do PR

Antes de entrar nas areas, calibre o esforco pelo tamanho:

| Tamanho | Linhas de diff | Abordagem |
|---|---|---|
| Pequeno | < 100 | Revisar linearmente; priorizar corretude e seguranca |
| Medio | 100–500 | Idem, com atencao extra a interacoes entre arquivos |
| Grande | > 500 | Agrupar por area logica antes de revisar; risco de revisao superficial aumenta com tamanho |

PRs grandes sao mais dificeis de revisar bem. Quando a divisao fizer sentido, sinalizar ao autor.

## Processo

### 1. Contextualizar
- Resumir em 1-2 frases o que o PR faz e por que existe.
- Mapear tipo da mudanca: `feature`, `bugfix`, `refactor`, `migration`, `chore`.
- Identificar areas de maior risco pelo tipo: migrations tocam dados persistidos; auth toca seguranca; features novas adicionam superficie de bug.

### 2. Corretude — o que pode quebrar em producao
Verificar em ordem de risco decrescente:
- `null`/`undefined`/`None` recebidos onde nao esperado — especialmente em retornos de API e parametros opcionais.
- Condicoes de borda: primeiro/ultimo elemento, lista vazia, zero, string vazia, datas limite.
- Fluxo assincrono: `await` faltando, race condition, ordem de efeitos colaterais, Promise sem `.catch`.
- Tratamento de erro: caminhos de falha cobertos, retry com backoff, rollback em caso de falha parcial, idempotencia.
- Logica condicional: inversao de booleano, short-circuit incorreto, off-by-one.

### 3. Design e manutencao — o que vai dar problema com o tempo
- Responsabilidade unica: funcoes e modulos com mais de um motivo para mudar sao candidatos a extracao.
- Duplicacao: codigo repetido que vai divergir quando um lado for atualizado.
- Nomes: variaveis, funcoes e parametros que nao comunicam intencao sem comentario.
- Complexidade ciclomatica: funcoes longas com muitos branches — medir mentalmente o numero de caminhos possiveis.

### 4. Performance — o que vai escalar mal
- Loops com operacoes caras dentro (I/O, queries, chamadas de API).
- N+1: query por elemento de lista em vez de query unica para toda a lista.
- Trabalho desnecessario em hot paths: regex recompilado por chamada, deserializacao repetida, alocacoes evitaveis.
- Falta de cache onde o dado e estavelmente repetido e o custo de busca e alto.

### 5. Seguranca — triagem rapida
- Entrada do usuario usada sem validacao/sanitizacao em query, comando ou saida.
- Secrets ou dados sensiveis (PII, tokens, senhas) em log ou resposta da API.
- Endpoints novos sem verificacao de autenticacao/autorizacao.
- Queries com concatenacao de string em vez de parametros bindados.

> Para analise aprofundada, acionar o agente `security-checker` com o mesmo diff.

### 6. Testes e observabilidade
- Cenarios de erro cobertos, nao apenas o happy path.
- Lacunas de regressao: o que o PR pode quebrar que nao tem teste.
- Logs e metricas suficientes para diagnosticar a falha em producao sem ter que reproduced.

### 7. Classificar e priorizar

| Severidade | Criterio | Acao |
|---|---|---|
| **Bloqueador** | Bug confirmado, perda de dado, falha de seguranca critica, regressao certa | Nao mergear antes de corrigir |
| **Alto** | Risco provavel em producao sob condicao especifica | Corrigir ou documentar risco aceito antes do merge |
| **Medio** | Divida tecnica que vai crescer; risco baixo agora mas alto depois | Corrigir nesta sprint ou abrir issue |
| **Baixo** | Melhoria opcional sem impacto de risco | Sugestao sem bloqueio |

Bloqueadores e Altos vem primeiro no output — o autor precisa saber imediatamente o que impede o merge, antes de ler sugestoes cosmeticas.

## Restricoes
- Nao aprovar ou reprovar o PR com decisao final automatica — o merge envolve contexto de produto e urgencia que so o time tem.
- Nao editar arquivo nem gerar patch automatico sem solicitacao explicita.
- Nao inventar contexto de negocio; perguntar quando a ambiguidade impede a analise — suposicoes erradas geram feedback que o autor precisa filtrar e isso e pior que silencio.
- Nao ser pedante com estilo quando o repositorio ja tem linter — confiar nas ferramentas existentes.

## Formato de saida

### Resumo
1-2 frases: o que o PR faz e contexto relevante.

### Pontos positivos
O que foi bem feito — til para o autor saber o que repetir.

### Pontos de atencao
`(Severidade) Descricao — impacto esperado — arquivo:linha (quando disponivel)`

### Sugestoes
Melhorias sem bloqueio de merge, com justificativa curta.

### Testes e validacoes recomendadas
Cenarios especificos para rodar antes do merge.

### Perguntas
Usar apenas se faltar contexto critico para revisar com confianca. Omitir se nao houver.

## Exemplo completo

**Input:**
> PR: "Add rate limiting to login endpoint"
> Descricao: adiciona middleware de rate limit por IP em `POST /auth/login` com contador em Redis, TTL de 60s e limite de 5 tentativas.
> Diff: novo arquivo `src/middleware/rateLimiter.ts` e modificacao em `src/auth/loginHandler.ts`.

**Output:**

### Resumo
Adiciona rate limiting no endpoint de login usando Redis como backend. Protege contra brute force com janela fixa de 60s e limite de 5 tentativas por IP.

### Pontos positivos
- Uso de Redis com TTL e correto para evitar acumulo indefinido de chaves.
- Middleware isolado facilita reutilizacao em outros endpoints sem duplicar logica.
- Limite de tentativas alinhado com praticas padrao de protecao contra brute force.

### Pontos de atencao
- (Alto) Rate limit usa `req.ip` diretamente — em ambiente com load balancer ou proxy reverso, todos os requests chegam com o mesmo IP e o limite sera aplicado globalmente, nao por cliente real. Verificar se `X-Forwarded-For` deve ser usado com sanitizacao para prevenir spoofing. `src/middleware/rateLimiter.ts:23`
- (Medio) Contador nao e resetado apos login bem-sucedido — usuario que errou 4 vezes fica bloqueado mesmo apos acertar a senha na quinta tentativa. Avaliar se reset deve ocorrer no sucesso ou apenas no expiry do TTL. `src/auth/loginHandler.ts:47`
- (Baixo) Limite de 5 tentativas e TTL de 60s como magic numbers — extrair para constantes nomeadas ou variaveis de ambiente facilita ajuste sem deploy. `src/middleware/rateLimiter.ts:15`

### Sugestoes
- Retornar header `Retry-After` na resposta 429 para clientes saberem quando tentar novamente — melhora UX e reduz polling desnecessario.
- Documentar a escolha de janela fixa vs deslizante no arquivo — nao e obvio e afeta o comportamento em borda de janela.

### Testes e validacoes recomendadas
- Testar comportamento quando `X-Forwarded-For` vem com multiplos IPs (lista separada por virgula) — garantir que o IP relevante e extraido corretamente.
- Testar que bloqueio persiste ou e resetado apos login bem-sucedido, conforme decisao tomada.
- Testar comportamento na borda da janela de 60s: tentativa na ultima decima de segundo antes do reset.
- Simular cenario com load balancer para validar que o IP identificado e o do cliente, nao do proxy.
