---
component-id: SC-19
component-name: Hero
type: SISP
status: in-figma
sprint: 8
approved-by: [Giuliana]
approved-date: [2026-08-10]
figma-node-id: [969:9236]
---

# Component Spec — Hero

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` — N/A (componente novo, sem violacoes pre-existentes)
> - `docs/analyses/nielsen-analysis.md` — N/A (componente novo)
> - `docs/analyses/inventory.md` — N/A (nao catalogado)
> - Referencia visual: DS Portal Hero (WF-01, node 523:243) — fundo escuro, layout centralizado
> - Referencia visual: DV Hero atual (Tela 1, node 943:8375) — layout horizontal, sem fundo escuro

> **Nota:** Este componente nao existia no inventario original. O spec e baseado na decisao de Giuliana (2026-08-10): "o diferencial do hero section e que ele tenha o fundo escuro e fontes com cores claras, igual o footer." O hero section do DS Portal (WF-01) ja usa esse padrao — o componente reutilizavel generaliza esse pattern para qualquer produto SISP.

> **Relacao com elementos existentes:** O hero do DS Portal (WF-01) e um frame manual dentro do layout — nao e um Component Set reutilizavel. O hero da DV (Tela 1) usa fundo claro/transparente, sem o padrao dark do DS. SC-19 unifica ambos como componente padrao.

---

## O que e

Hero e o componente de destaque visual do topo de paginas SISP. Usa fundo escuro (`dark/surface`) com tipografia clara (`text/inverse`) para criar hierarquia visual forte e identidade institucional. Suporta eyebrow, titulo, subtitulo, CTAs e conteudo auxiliar configuravel (imagem, stats bar, link). E o primeiro elemento visual apos o Header em paginas de entrada.

---

## Audiencia de uso

- **Cidadaos SC:** veem o hero como primeira impressao da Delegacia Virtual — hierarquia visual guia para a acao principal (registrar ocorrencia)
- **Servidores CiASC / POs:** hero transmite identidade institucional consistente entre produtos SISP
- **Devs CiASC / terceiros (implementacao):** instanciam o componente e fazem override de conteudo (textos, CTAs, imagem) para cada produto. Componente auto-suficiente — nao depende de contexto externo

---

## Props / API

> **Padrao de API:** Componente de apresentacao — gerencia layout e hierarquia visual. CTAs sao instancias de BC-05 Button com eventos proprios.

| Prop | Tipo | Obrigatorio | Padrao | Descricao |
|---|---|---|---|---|
| `eyebrow` | `string` | nao | `null` | Texto overline acima do titulo (ex: "DELEGACIA VIRTUAL") |
| `title` | `string` | sim | — | Titulo principal do hero (ex: "Registre sua ocorrencia online") |
| `subtitle` | `string` | nao | `null` | Texto descritivo abaixo do titulo |
| `ctaPrimaryLabel` | `string` | nao | `null` | Label do botao primario — se null, botao nao renderiza |
| `ctaPrimaryAction` | `EventEmitter<void>` | nao | — | Emitido ao clicar CTA primario |
| `ctaSecondaryLabel` | `string` | nao | `null` | Label do botao secundario — se null, botao nao renderiza |
| `ctaSecondaryAction` | `EventEmitter<void>` | nao | — | Emitido ao clicar CTA secundario |
| `auxiliaryText` | `string` | nao | `null` | Texto de link auxiliar abaixo dos CTAs |
| `auxiliaryUrl` | `string` | nao | `null` | URL do link auxiliar |
| `showImage` | `boolean` | nao | `false` | Exibe area de imagem/logo a esquerda (Desktop) ou acima (Mobile) |

**Convencao Angular:**
```html
<sisp-lib-hero
  [eyebrow]="'DELEGACIA VIRTUAL'"
  [title]="'Registre sua ocorrencia online'"
  [subtitle]="'Faca seu Boletim de Ocorrencia sem sair de casa...'"
  [ctaPrimaryLabel]="'Registrar Ocorrencia'"
  (ctaPrimaryAction)="onRegistrar()"
  [auxiliaryText]="'Acompanhar ocorrencia existente'"
  [auxiliaryUrl]="'/acompanhar'">
</sisp-lib-hero>
```

---

## Anatomia do componente

### Desktop (1440px, layout centralizado)

```
Full-width background (dark/surface)
└── Content Container (max-width 800px, centralizado vertical + horizontal)
    ├── [Imagem/Logo]  (opcional — visivel quando showImage=true)
    ├── [Badge]  (opcional — instancia BC-04, visivel quando necessario)
    │
    ├── Eyebrow  (opcional)
    │   └── text node — Overline/XS, fill: text/inverse, uppercase
    │
    ├── Titulo
    │   └── text node — Heading/2XL, fill: text/inverse
    │
    ├── Subtitulo  (opcional)
    │   └── text node — Body/LG/Regular, fill: text/inverse (opacity reduzida via dark/text)
    │
    ├── CTA Row  (frame horizontal, gap: space/3)
    │   ├── BC-05 Button Primary LG — CTA principal
    │   └── BC-05 Button Secondary LG — CTA secundario (opcional)
    │
    └── Link Auxiliar  (opcional)
        └── text node — Body/SM/Regular, fill: text/inverse, underline
```

### Mobile (375px, layout vertical)

```
Full-width background (dark/surface)
└── Content Container (FILL width, padding horizontal page-padding 16px)
    ├── [Imagem/Logo]  (opcional, escala reduzida)
    │
    ├── Eyebrow  (opcional)
    │   └── text node — Overline/XS, fill: text/inverse
    │
    ├── Titulo
    │   └── text node — Heading/XL, fill: text/inverse (reduzido de 2XL para fit)
    │
    ├── Subtitulo  (opcional)
    │   └── text node — Body/Base/Regular, fill: text/inverse
    │
    ├── CTA Stack  (frame VERTICAL, gap: space/3, FILL width)
    │   ├── BC-05 Button Primary LG — full-width
    │   └── BC-05 Button Secondary LG — full-width (opcional)
    │
    └── Link Auxiliar  (opcional)
        └── text node — Body/SM/Regular, fill: text/inverse, underline, center
```

**Composicao atomica (Regra 11):**

| Elemento | Componente DS | Quantidade |
|---|---|---|
| CTA primario | BC-05 Button Primary LG | 1 instancia |
| CTA secundario | BC-05 Button Secondary LG | 1 instancia (opcional, visible=false default) |
| Badge/tag | BC-04 Badge Neutral Subtle SM | 1 instancia (opcional, visible=false default) |
| Icones decorativos | BC-15 Icons | Conforme necessidade (opcional) |

> **Regra 12 — auditoria:** "este elemento ja existe?" Botoes = BC-05 Button. Badge = BC-04 Badge. Icones = BC-15 Icons. O container de conteudo e frame de layout (nao componente). Background e fill direto — nao componente. Eyebrow e text node com text style Overline/XS — nao componente.

---

## Estados e variantes

### Variantes no Figma

| Variante | Properties | Descricao |
|---|---|---|
| Desktop | `Layout=Desktop` | Full-width 1440px, conteudo centralizado, CTAs lado a lado |
| Mobile | `Layout=Mobile` | Full-width 375px, conteudo stack vertical, CTAs empilhados |

> **Nota:** Hero nao tem estados interativos proprios (hover, focus, disabled). Interatividade e delegada aos CTAs (instancias BC-05 Button) e ao link auxiliar. Apenas 2 variantes: Layout=Desktop e Layout=Mobile.

### Cores e tokens

| Elemento | Token Figma | Valor SC |
|---|---|---|
| Fundo do hero | `dark/surface` | #192D22 |
| Texto principal (titulo) | `text/inverse` | #FFFFFF |
| Texto secundario (subtitulo) | `dark/text` | #FFFFFF |
| Texto muted (eyebrow, link) | `text/inverse` | #FFFFFF |
| Botoes CTA | herda BC-05 Primary/Secondary | — |
| Link auxiliar | `text/inverse` | #FFFFFF |

### Verificacao de contraste (WCAG AA)

| Elemento | Foreground | Background | Ratio | Resultado |
|---|---|---|---|---|
| Titulo (Heading/2XL) | #FFFFFF | #192D22 | > 12:1 | AAA |
| Subtitulo (Body/LG) | #FFFFFF | #192D22 | > 12:1 | AAA |
| Eyebrow (Overline/XS) | #FFFFFF | #192D22 | > 12:1 | AAA (texto 12px bold = limiar, passa como uppercase semibold) |
| Link auxiliar | #FFFFFF | #192D22 | > 12:1 | AAA |
| BC-05 Button Primary | herda BC-05 | — | Via BC-05 | AA |
| BC-05 Button Secondary | herda BC-05 | — | Via BC-05 | AA |

> **Nota:** Branco (#FFFFFF) sobre `dark/surface` (#192D22) tem contraste superior a 12:1. Todos os textos passam AAA. Os botoes herdam contraste do BC-05 (ja validado AA).

### Dimensoes

| Propriedade | Desktop | Mobile | Token |
|---|---|---|---|
| Largura total | 1440px (FILL) | 375px (FILL) | — |
| Altura | HUG contents | HUG contents | — |
| Padding vertical | 48px | 32px | `space/12` / `space/8` |
| Padding horizontal | 48px (responsivo) | 16px (responsivo) | `space/page-padding` |
| Gap entre elementos | 16px | 16px | `space/4` |
| Gap entre CTAs | 12px | 12px | `space/3` |
| Content max-width | ~800px | 100% (FILL) | — |
| Titulo text style | Heading/2XL | Heading/XL | Text Styles |
| Subtitulo text style | Body/LG | Body/Base | Text Styles |
| CTA layout | Horizontal (row) | Vertical (stack) | Auto-layout direction |

---

## Violacoes a resolver — WCAG AA

| Dimensao | Violacao atual | Solucao neste spec |
|---|---|---|
| (N/A) | Componente novo — sem violacoes previas | Spec resolve preventivamente: (1) Fundo escuro com texto branco — contraste > 12:1 AAA em todos os elementos. (2) CTAs com labels descritivos ("Registrar Ocorrencia", "Acompanhar ocorrencia existente") — nunca so icone. (3) Focus ring visivel nos interativos (herda BC-05). (4) Link auxiliar com underline para nao depender so de cor. (5) Labels em portugues. (6) Sem dependencia de cor para transmitir informacao — hierarquia via tamanho tipografico + peso |

---

## Violacoes a resolver — Heuristicas Nielsen

| Heuristica | Violacao atual | Solucao neste spec |
|---|---|---|
| (N/A) | Componente novo | Spec resolve preventivamente: |
| H-1 Visibilidade | — | Hierarquia visual clara: eyebrow → titulo → subtitulo → CTA. Fundo escuro diferencia o hero do restante da pagina — usuario identifica imediatamente a secao de destaque |
| H-2 Mundo real | — | Vocabulario institucional em portugues: "Registrar Ocorrencia", "Delegacia Virtual". Sem jargao tecnico. Eyebrow identifica o produto/contexto |
| H-3 Controle | — | CTAs sao links/botoes — usuario decide se interage. Nenhuma acao automatica. Link auxiliar oferece caminho alternativo |
| H-4 Consistencia | — | Botoes seguem padrao BC-05. Fundo escuro segue padrao do footer DV e header. Tokens compartilhados com outros componentes dark |
| H-6 Reconhecimento | — | CTA primario com destaque visual (Button Primary LG). Eyebrow em uppercase identifica contexto. Hierarquia tipografica guia leitura |
| H-8 Estetica | — | Design minimalista — fundo escuro unico, tipografia clara, espacamento generoso. Sem ruido visual. Alinhamento centralizado cria simetria |
| H-10 Ajuda | — | Link auxiliar disponivel para caminhos alternativos (ex: "Acompanhar ocorrencia existente") |

---

## Regras de acessibilidade

- [ ] Contraste minimo 4.5:1 para texto normal / 3:1 para texto grande — todos passam AAA (> 12:1)
- [ ] Focus ring visivel nos CTAs — herda BC-05 Button (`2px solid var(--color-border-focus)`)
- [ ] Link auxiliar com focus ring visivel e underline (nao depende so de cor)
- [ ] Navegavel por teclado — Tab entre CTAs e link auxiliar
- [ ] Hero section com tag semantica `<section>` e `aria-label="[eyebrow ou titulo]"`
- [ ] Ordem de tab logica: CTA primario → CTA secundario → link auxiliar
- [ ] Nao depende apenas de cor — hierarquia via tamanho + peso tipografico (3 niveis visuais)
- [ ] Labels em portugues
- [ ] `prefers-reduced-motion` — componente e estatico, sem animacoes
- [ ] Imagem/logo (quando presente) com `aria-hidden="true"` se decorativa, ou `alt` descritivo se informativa

---

## Comportamentos esperados

### Apresentacao
- Quando pagina carrega → hero renderiza imediatamente como primeiro bloco visual apos Header
- Quando eyebrow e fornecido → texto uppercase exibido acima do titulo (Overline/XS)
- Quando eyebrow nao e fornecido → espaco do eyebrow nao renderiza (auto-layout HUG)
- Quando subtitulo nao e fornecido → espaco do subtitulo nao renderiza
- Quando ctaPrimaryLabel e fornecido → botao primario visivel
- Quando ctaSecondaryLabel e fornecido → botao secundario visivel ao lado (Desktop) ou abaixo (Mobile) do primario
- Quando auxiliaryText e fornecido → link auxiliar visivel abaixo dos CTAs
- Quando showImage=true → area de imagem visivel a esquerda (Desktop) ou acima (Mobile) do conteudo textual

### Interacao
- Quando usuario clica CTA primario → emite `ctaPrimaryAction`
- Quando usuario clica CTA secundario → emite `ctaSecondaryAction`
- Quando usuario clica link auxiliar → navega para `auxiliaryUrl`
- Interatividade dos botoes (hover, focus, active) e gerenciada pelas instancias BC-05

### Responsividade
- Quando viewport < 768px → Layout=Mobile ativa
- Quando viewport >= 1024px → Layout=Desktop ativa
- Tablet (768–1023px) → segue Desktop com padding ajustado via tokens responsivos

---

## Comportamento responsivo

> Regra 13 — Responsividade obrigatoria. Breakpoints: Mobile (<768px), Tablet (768–1023px), Desktop (>=1024px).

| Precisa de variante `Layout=Mobile`? | **Sim** — layout complexo com mudancas em tipografia, direcao dos CTAs e padding |
|---|---|
| **Desktop** | Full-width, conteudo centralizado max-width ~800px, CTAs lado a lado (horizontal), titulo Heading/2XL, padding 48px vertical |
| **Mobile** | Full-width, conteudo stack vertical 100%, CTAs empilhados (vertical full-width), titulo Heading/XL, padding 32px vertical |
| **Tablet** | Segue Desktop — padding ajustado via `space/page-padding` (token responsivo) |

**O que muda entre Desktop e Mobile:**
- Titulo: Heading/2XL → Heading/XL (melhor fit em 375px)
- Subtitulo: Body/LG → Body/Base
- CTAs: horizontal (row, gap space/3) → vertical (stack, gap space/3, full-width)
- Padding vertical: 48px (space/12) → 32px (space/8)
- Padding horizontal: 48px (space/page-padding Desktop) → 16px (space/page-padding Mobile)
- Content width: max ~800px → 100% (FILL)

---

## Casos excepcionais / bordas

- **Titulo muito longo:** text wrapping natural — auto-layout HUG height acomoda. Titulo nunca trunca
- **Sem CTAs:** se nenhum CTA label fornecido, hero renderiza apenas textos. Valido para paginas informativas
- **Sem subtitulo:** gap entre titulo e CTAs reduz naturalmente (auto-layout)
- **Imagem grande:** area de imagem tem dimensoes fixas (max 200×200), imagem e redimensionada com object-fit cover
- **Temas (PC, CBM):** fundo `dark/surface` muda conforme tema (se definido na colecao Colors). Texto `text/inverse` permanece branco. Botoes seguem tema via BC-05

---

## O que esta fora deste spec

- **Animacao de entrada:** hero renderiza imediatamente, sem fade-in ou slide — extensao futura
- **Video de fundo:** hero usa cor solida (`dark/surface`) — sem suporte a video/imagem de fundo full-bleed
- **Busca embutida:** hero nao contem campo de busca — BC-29 Search e componente separado
- **Stats bar:** a barra de estatisticas do DS Portal hero (WF-01) e conteudo especifico daquele layout — nao faz parte do Component Set generico. Pode ser adicionada como slot futuro
- **Carrossel de heroes:** nao ha suporte a multiplos slides — extensao futura
- **Breadcrumb sobre o hero:** breadcrumb fica fora do hero, no layout da pagina

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `dark/surface` | Fundo do hero |
| `text/inverse` | Titulo, eyebrow, link auxiliar |
| `dark/text` | Subtitulo (variacao sutil) |
| `space/page-padding` | Padding horizontal (responsivo: 48px Desktop / 16px Mobile) |
| `space/12` | Padding vertical Desktop (48px) |
| `space/8` | Padding vertical Mobile (32px) |
| `space/4` | Gap entre elementos do content stack |
| `space/3` | Gap entre CTAs |
| `radius/none` | Hero e full-width, sem border-radius |
| Heading/2XL | Text Style — titulo Desktop |
| Heading/XL | Text Style — titulo Mobile |
| Overline/XS | Text Style — eyebrow |
| Body/LG | Text Style — subtitulo Desktop |
| Body/Base | Text Style — subtitulo Mobile |
| Body/SM | Text Style — link auxiliar |

---

## Criterios de aceite

- [ ] 2 variantes no Figma: Layout=Desktop (1440px) e Layout=Mobile (375px)
- [ ] Fundo escuro `dark/surface` (#192D22) com texto claro `text/inverse` (#FFFFFF) — visual alinhado com footer DV e portal hero
- [ ] CTA primario como instancia de BC-05 Button Primary LG (Regra 11)
- [ ] CTA secundario como instancia de BC-05 Button Secondary LG (Regra 11) — visible=false default
- [ ] Badge como instancia de BC-04 Badge Neutral Subtle SM (Regra 11) — visible=false default
- [ ] Tokens aplicados — zero valores hardcoded (Regra 8)
- [ ] 100% padding/gap bound a variaveis Spacing
- [ ] 100% color fills bound a variaveis Colors (dark/surface, text/inverse, dark/text)
- [ ] Text Styles aplicados a todos os text nodes textuais (Regra 15)
- [ ] Contraste verificado > 12:1 AAA em todos os elementos textuais
- [ ] Layout Desktop: centralizado, CTAs lado a lado, Heading/2XL
- [ ] Layout Mobile: stack vertical, CTAs empilhados full-width, Heading/XL
- [ ] Eyebrow usa Overline/XS (text style existente — nao criar novo)
- [ ] Acessibilidade documentada (section semantica, tab order, focus ring)
- [ ] Labels em portugues
- [ ] Composicao atomica verificada — nenhum elemento recriado manualmente
- [ ] Revisado e aprovado por Giuliana
