---
name: carrossel-linkedin
description: Gerador de carrossel premium para LinkedIn. Use quando o usuário quiser criar, transformar ou otimizar slides para LinkedIn a partir de um arquivo Markdown, tópicos, texto corrido ou ideias soltas. Ative também quando o usuário mencionar "carrossel", "slides", "post visual", "conteúdo LinkedIn" ou pedir para transformar qualquer conteúdo em formato visual para redes sociais. Se o usuário tem um .md e quer um carrossel profissional de alto impacto, use este skill. Suporta DESIGN.md (spec do Google Labs / getdesign.md) como fonte de direção visual.
---

# Gerador de Carrossel LinkedIn Premium

Transforma conteúdo em carrossel visual premium para LinkedIn — copy de alto impacto, direção visual precisa e prompts de imagem prontos para geração.

A **direção visual** vem de um arquivo `DESIGN.md` (quando o usuário fornece) ou é definida por perguntas rápidas. Se nada for fornecido, usa o **tema LinkedIn padrão**.

---

## Passo 0 — Perguntar o caminho de saída

**Antes de qualquer coisa**, pergunte ao usuário:

> "Onde você quer que os arquivos `.png` sejam salvos? (ex: `/Users/você/Desktop/carrossel/`)"

Guarde essa resposta. Use-a ao final para listar os arquivos com o caminho completo:
```
/caminho/informado/slide01.png
/caminho/informado/slide02.png
...
```

---

## Passo 1 — Definir a direção visual

A direção visual define **apenas os tokens visuais** (cores, tipografia, raio, sombra, estilo). A **estrutura do carrossel** (contador `N/11`, cards, CTAs sociais) é sempre a mesma desta skill.

### 1.1 — O usuário já forneceu um DESIGN.md?

Considere que sim quando o usuário:
- Anexar/colar o conteúdo de um `DESIGN.md`, **ou**
- Informar o caminho de um arquivo `DESIGN.md` (ex: `./DESIGN.md`), **ou**
- Pedir explicitamente para "usar o DESIGN.md".

➡️ **Se sim:** leia o arquivo e vá direto para o **Passo 1.3 (Resolver tokens)**.

### 1.2 — Se NÃO houver DESIGN.md nem especificações visuais

Pergunte ao usuário qual caminho ele prefere (use uma pergunta de múltipla escolha):

> **Como você quer definir a identidade visual do carrossel?**
> 1. **Responder perguntas rápidas** — eu te faço 5 perguntas (cores, tipografia, fundo, estilo, marca) e gero a partir delas.
> 2. **Usar/gerar um DESIGN.md** — você aponta um `DESIGN.md` existente, ou rodamos um comando para gerar um a partir de um site de referência.
> 3. **Tema LinkedIn padrão** — usar o tema clean azul LinkedIn já pronto.

**Caminho 1 — Perguntas rápidas.** Faça estas perguntas (aceite respostas parciais; use o tema padrão para o que faltar):

| # | Pergunta | Mapeia para |
|---|---|---|
| 1 | Cor de destaque / marca? (hex ou nome) | `accent` |
| 2 | Fundo claro ou escuro? Cor? | `background` + `text` |
| 3 | Tipografia preferida? (ex: Inter, DM Sans, Poppins) | `font.headline` / `font.body` |
| 4 | Estilo visual? (clean/flat, premium/escuro, vibrante, minimalista, corporativo) | `style` |
| 5 | Logo, nome ou @ para assinar os slides? | `brand` |

**Caminho 2 — DESIGN.md.** Se o usuário não tem um arquivo, oriente a gerar:
```bash
# Coleção pronta (getdesign.md) — gera DESIGN.md a partir de um site de referência
npx getdesign@latest add <url-ou-marca>

# Ou a CLI oficial do Google Labs
npx @google/design.md spec
```
Depois que o arquivo existir, leia-o e siga para o **Passo 1.3**.

**Caminho 3 — Tema padrão.** Vá direto para o **Passo 1.3** usando o tema LinkedIn padrão.

### 1.3 — Resolver os tokens de design

Monte a tabela de tokens abaixo. **Cada token vem do DESIGN.md / das respostas; se ausente, use o valor do Tema LinkedIn padrão.**

O DESIGN.md tem frontmatter YAML com tokens (`colors`, `typography`, `rounded`, `spacing`, `components`) + seções em prosa (`Overview`, `Colors`, `Typography`, `Layout`, `Elevation & Depth`, `Shapes`, `Components`, `Do's and Don'ts`). Extraia assim:

| Token | Origem no DESIGN.md | Tema LinkedIn padrão |
|---|---|---|
| `accent` (destaque) | `colors.primary` / `colors.accent` | `#0A66C2` |
| `background` (fundo) | `colors.background` / `colors.surface` | `#FFFFFF` (ou off-white `#F8F9FA`) |
| `text` (texto) | `colors.neutral` / `colors.foreground` | `#111827` |
| `card.bg` (fundo do card) | `components.card.backgroundColor` | `#FFFFFF` |
| `card.border` (borda do card) | `components.card.borderColor` | `#E5E7EB` |
| `accent.soft` (card de destaque) | tom claro de `accent` | `#EEF3FB` |
| `font.headline` | `typography.*.fontFamily` (heading) | `Inter` / `DM Sans` (bold) |
| `font.body` | `typography.body.fontFamily` | `Inter` / `DM Sans` (regular) |
| `rounded` (raio) | `rounded.md` / `rounded.lg` | `12px` |
| `shadow` (sombra) | `elevation` / `components.card.shadow` | sombra leve |
| `style` (personalidade) | seção `Overview` | clean flat design, alta legibilidade |
| `brand` (assinatura) | — (resposta do usuário) | nenhuma |

> **Importante:** se o DESIGN.md trouxer fundo escuro, ajuste automaticamente `text` para um tom claro e garanta contraste AA. Respeite os "Do's and Don'ts" do DESIGN.md como restrições.

**Confirme em 1 linha** os tokens resolvidos antes de gerar, ex:
`Direção visual: destaque #7C3AED, fundo escuro #0B0B0F, tipografia Poppins, estilo premium. Ok?`

Use **sempre** esses tokens nos prompts de imagem (no lugar dos valores fixos antigos).

---

## Entrada esperada

Aceita qualquer formato:
- Arquivo `.md` com slides estruturados (`## Slide N — Título`)
- Tópicos soltos ou bullet points
- Texto corrido para transformar em slides

---

## Padrões visuais esperados

> As cores e fontes abaixo são as do tema padrão. Quando houver DESIGN.md / respostas, **substitua** `accent`, `background`, fonte e raio pelos tokens resolvidos no Passo 1.3.

| Elemento | Como aparece |
|---|---|
| Contador | `N/11` pequeno no canto superior esquerdo |
| Headline | Grande, bold, alinhado à esquerda; palavras-chave em `accent` |
| Subtítulo | Regular weight, logo abaixo do headline |
| Cards de conteúdo | Fundo `card.bg`, borda `card.border`, arredondados (`rounded`), com ícone + texto |
| Card de destaque | Rodapé com fundo `accent.soft` ou borda `accent`, frase de impacto em `accent` |
| Ícones | Estilo 3D clean (emoji-like), posicionados à direita ou dentro dos cards |
| Assinatura | `brand` (logo/@ ) discreta no rodapé, se fornecida |
| Slide de fechamento | Headline grande + card `accent.soft` com frase principal + CTAs sociais (Salve · Compartilhe · Comente · Curta) |

---

## Princípios de copy

- **1 ideia por slide** — sem exceção
- **Primeira linha = anzol** — precisa parar o scroll imediatamente
- **Frases curtas** — máx. 10 palavras por linha; quebras de linha são aliadas
- **Concreto > abstrato** — números, exemplos reais, comparações diretas
- **Contraste** — o que todos pensam vs. o que é verdade
- **Máx. 40 palavras por slide** — se passou, corte

Evite: jargão sem contexto, listas com mais de 3 itens, texto em bloco, informação óbvia.

---

## Estrutura narrativa

| Posição | Função | O que o slide faz |
|---|---|---|
| Capa | Hook + promessa | Faz o leitor querer o próximo |
| Problema | Dor real + dado | Valida que o problema existe |
| Insight | Virada de chave | Muda a perspectiva |
| Solução/Framework | O método (2–4 slides) | Dá a resposta concreta |
| Prática | Como aplicar agora | Gera ação |
| Fechamento | Reflexão + CTA | Fecha o loop + pede engajamento |

Se o MD já tem essa estrutura, respeite. Se não, reorganize — explique brevemente o que mudou.

---

## Especificações visuais

> Estes são os valores **default (tema LinkedIn)**. Sempre que o Passo 1.3 resolver tokens diferentes, use os tokens resolvidos.

- **Formato:** 1080×1080px
- **Fundo:** `background` — branco `#FFFFFF` ou off-white `#F8F9FA`
- **Destaque:** `accent` — azul `#0A66C2` em palavras-chave e elementos de ênfase
- **Tipografia:** `font.headline` / `font.body` — Inter ou DM Sans — bold para headlines, regular para corpo
- **Cards:** `border-radius: rounded` (12px), borda `card.border` (#E5E7EB), padding interno generoso
- **Ícones:** Estilo 3D clean, alinhados com o conteúdo do card
- **Espaçamento:** Margens laterais mínimas de 60px, nunca lotado
- **Estilo:** `style` — clean flat, alta legibilidade, otimizado para mobile
- **Sem grid:** Cada slide é uma imagem independente

---

## Template por slide

Use este formato para **cada slide**:

---

### Slide [N] — [Título]

**Nome do arquivo:** `slide[NN].png` (ex: `slide01.png`, `slide02.png`)

**Copy final**
```
[Texto exato — quebrado em linhas curtas, máx. 40 palavras]
```

**Hierarquia de texto**
- Headline: [frase principal bold — 4 a 8 palavras, palavras-chave em `accent`]
- Corpo: [complemento ou dado de apoio]
- Tag: [contador `N/11` no canto superior esquerdo]
- Rodapé: [frase de impacto no card inferior, se aplicável]

**Layout**
[Descreva: posição do headline, dos cards, dos ícones, do card de rodapé. Seja preciso — "headline no terço superior, dois cards lado a lado no meio, card de destaque no rodapé"]

**Elementos visuais**
[Ícones específicos (ex: 🧩 para agente, 🎯 para objetivo), cards, separadores, estilo do card de rodapé]

**Prompt de imagem**
> Crie uma imagem 1080×1080px, estilo `{style}`, fundo `{background}`, tipografia `{font.headline}`/`{font.body}`.
> `{accent}` como cor de destaque. Sem grade. Alta legibilidade — otimizado para mobile.
>
> **Contador:** "N/11" pequeno, discreto, canto superior esquerdo.
>
> **Conteúdo:** [TEXTO EXATO DO SLIDE]
>
> **Layout:** [DESCRIÇÃO DO POSICIONAMENTO]
>
> **Elementos:** [ÍCONES E CARDS ESPECÍFICOS — cards com borda `{card.border}`, raio `{rounded}`]

**Checklist**
- [ ] 1 ideia única
- [ ] Máx. 40 palavras
- [ ] Contador N/11 no canto superior esquerdo
- [ ] Palavra-chave em `accent` no headline
- [ ] Card de impacto no rodapé
- [ ] Tokens da direção visual aplicados (cor/fonte/raio)
- [ ] Legível em mobile (375px)

---

## Exemplo completo — Slide 1 (Capa)

> Exemplo usando o **tema LinkedIn padrão**. Com outro DESIGN.md, troque `#0A66C2` e `DM Sans` pelos tokens resolvidos.

Input do MD:
```
## Slide 1 — Capa
Como usar IA de verdade no desenvolvimento de software
Insights de um workshop com +3.800 pessoas.
```

---

**Slide 1 — Capa**

**Nome do arquivo:** `slide01.png`

**Copy final**
```
Como usar
IA de verdade
no desenvolvimento
de software

Insights de um workshop
com +3.800 pessoas.
```

**Hierarquia de texto**
- Headline: "Como usar **IA de verdade** no desenvolvimento de software" — "IA de verdade" em `accent` bold
- Corpo: "Insights de um workshop com +3.800 pessoas." em regular, menor
- Tag: "1/11" no canto superior esquerdo

**Layout**
Headline ocupa o lado esquerdo, grande, alinhado à esquerda. Ícone 3D `</>` em card `card.bg` arredondado no canto inferior direito. Subtítulo logo abaixo do headline. Seta `→` em botão circular no rodapé esquerdo.

**Elementos visuais**
Ícone `</>` 3D em `accent` em card `card.bg` com sombra leve. Fundo off-white `#F8F9FA`. Botão circular com seta `→` no canto inferior esquerdo.

**Prompt de imagem**
> Crie uma imagem 1080×1080px, estilo clean flat design, fundo off-white (#F8F9FA), tipografia DM Sans.
> Azul (#0A66C2) como cor de destaque. Sem grade. Alta legibilidade.
>
> **Contador:** "1/11" pequeno, cinza, canto superior esquerdo.
>
> **Conteúdo:**
> Headline grande bold: "Como usar" / "IA de verdade" (em azul) / "no desenvolvimento" / "de software"
> Subtítulo regular: "Insights de um workshop com +3.800 pessoas."
>
> **Layout:** Texto alinhado à esquerda nos dois terços esquerdos. Ícone `</>` 3D em card branco arredondado no canto inferior direito. Botão circular com seta `→` no canto inferior esquerdo.
>
> **Elementos:** Ícone `</>` estilo 3D clean azul, card com sombra leve, fundo off-white.

**Checklist**
- [x] 1 ideia única (capa = promessa do carrossel)
- [x] Máx. 40 palavras
- [x] Contador 1/11 no canto superior esquerdo
- [x] "IA de verdade" em azul no headline
- [x] Seta indicando que há mais slides
- [x] Legível em mobile

---

## Exemplo completo — Slide 11 (Fechamento)

**Slide 11 — Fechamento**

**Nome do arquivo:** `slide11.png`

**Copy final**
```
IA não substitui
engenheiros.

Mas...

amplifica
quem sabe usar.
```

**Hierarquia de texto**
- Headline: "IA não substitui **engenheiros.**" — "engenheiros." em `accent`
- Transição: "Mas..." em regular, peso médio
- Card de destaque: "**amplifica** quem sabe usar." — "amplifica" em `accent` bold
- Rodapé: CTAs sociais — Salve · Compartilhe · Comente · Curta

**Layout**
Headline grande no terço superior esquerdo. "Mas..." logo abaixo como transição. Card `accent.soft` no centro com ícone 🚀 à esquerda e frase de impacto à direita. Rodapé com 4 CTAs sociais com ícones pequenos.

**Elementos visuais**
Card central com fundo `accent.soft` `#EEF3FB`, borda `accent` `#0A66C2` suave, ícone 🚀 3D. Rodapé com ícones de bookmark, compartilhar, comentário e coração, cada um com texto abaixo (Salve, Compartilhe, Comente, Curta).

**Prompt de imagem**
> Crie uma imagem 1080×1080px, estilo clean flat design, fundo branco (#FFFFFF), tipografia DM Sans.
> Azul (#0A66C2) como cor de destaque. Sem grade. Alta legibilidade.
>
> **Contador:** "11/11" pequeno, cinza, canto superior esquerdo.
>
> **Conteúdo:**
> Headline bold: "IA não substitui" / "engenheiros." (engenheiros em azul)
> Transição: "Mas..." em regular
> Card de destaque: "amplifica" (bold azul) + "quem sabe usar."
> Rodapé: 4 CTAs com ícones — Salve | Compartilhe | Comente | Curta
>
> **Layout:** Headline grande no topo esquerdo. Card com fundo azul claro centralizado no meio. Rodapé com 4 colunas de CTAs sociais.
>
> **Elementos:** Ícone 🚀 3D dentro do card. Card com fundo #EEF3FB e borda sutil azul. Ícones de social proof no rodapé.

**Checklist**
- [x] 1 ideia única (amplificação, não substituição)
- [x] Máx. 40 palavras
- [x] Contador 11/11
- [x] "engenheiros" e "amplifica" em azul
- [x] CTAs sociais no fechamento
- [x] Legível em mobile

---

## Processo de execução

1. **Pergunte o caminho de saída** dos `.png` (Passo 0)
2. **Defina a direção visual** (Passo 1): use o DESIGN.md fornecido; se não houver, ofereça as 3 opções (perguntas rápidas / gerar-apontar DESIGN.md / tema padrão) e resolva os tokens
3. **Confirme os tokens resolvidos** em 1 linha
4. **Leia o conteúdo** — identifique tema, público, tom, nível técnico
5. **Mapeie a narrativa** — reorganize se necessário
6. **Gere slide a slide** — use o template, aplicando os tokens resolvidos em cada prompt
7. **Liste os arquivos ao final** com o caminho completo informado pelo usuário