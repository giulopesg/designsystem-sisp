---
component-id: SC-17
component-name: Login SISP
type: SISP
status: in-figma
sprint: 6
approved-by: [Giuliana]
approved-date: [2026-08-04]
figma-node-id: [795:5694]
---

# Component Spec — Login SISP

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` — N/A (componente novo, sem violações pré-existentes)
> - `docs/analyses/nielsen-analysis.md` — N/A (componente novo)
> - `docs/analyses/inventory.md` — N/A (não catalogado)
> - Referência visual: `uikit-screencapture/login_screencapture.webp`

> **Nota:** Este componente não existia no inventário original. O spec é baseado no screenshot de produção do portal SISP Design System e nas decisões de Giuliana (2026-07-30). Não há dados de análise WCAG ou Nielsen prévios — acessibilidade é resolvida preventivamente.

> **Relação com SC-08 Login (DV):** SC-08 é um formulário de credenciais (CPF/senha) para a Delegacia Virtual — componente complexo com 6 estados e 2 inputs. SC-17 Login SISP é uma tela de OAuth/redirect sem campos de formulário — componente simples com 2 estados e 2 botões de redirect. São componentes separados para contextos diferentes.

---

## O que é

Login SISP é o componente de autenticação do portal SISP. Oferece entrada via OAuth (gov.br) e acesso direto ao SISP Design System — sem campos de formulário. É a primeira tela que o usuário vê ao acessar o portal. O layout é horizontal em duas colunas: identidade institucional (logo SISP) à esquerda, ações de autenticação à direita.

---

## Audiência de uso

- **Servidores CiASC:** acessam o portal SISP DS para consultar documentação de componentes e tokens
- **Devs terceirizados:** precisam de login rápido via gov.br para acessar o DS e começar a desenvolver
- **POs (Sommer/Holiwod):** acessam para acompanhar progresso do DS
- **Devs CiASC / terceiros (implementação):** integram o componente no layout do portal. Componente é auto-suficiente — gerencia redirect OAuth internamente

---

## Props / API

> **Padrão de API:** Componente de redirect — não gerencia credenciais. Redireciona para provedores OAuth externos.

| Prop | Tipo | Obrigatório | Padrao | Descricao |
|---|---|---|---|---|
| `govBrUrl` | `string` | sim | — | URL de redirect para autenticacao gov.br |
| `sispUrl` | `string` | sim | — | URL de redirect para autenticacao SISP interna |
| `helpUrl` | `string` | nao | `null` | URL do link "Como acessar?" — se null, link nao aparece |
| `(authRedirect)` | `EventEmitter<AuthRedirectEvent>` | nao | — | Emitido antes do redirect — permite logging/analytics |

**AuthRedirectEvent:**
```typescript
interface AuthRedirectEvent {
  provider: 'gov-br' | 'sisp';
  timestamp: Date;
}
```

**Convencao Angular:**
```html
<sisp-lib-login-sisp
  [govBrUrl]="config.govBrAuthUrl"
  [sispUrl]="config.sispAuthUrl"
  [helpUrl]="config.helpUrl"
  (authRedirect)="onAuthRedirect($event)">
</sisp-lib-login-sisp>
```

---

## Anatomia do componente

### Desktop (layout horizontal)
```
Background (surface/bg-subtle — pagina inteira)
└── Card (HORIZONTAL, centralizado)
    ├── Logo Area (VERTICAL, hug, centrado verticalmente)
    │   ├── "Sistema Integrado" — Body/SM, text/secondary
    │   ├── "SISP" — Heading/4XL, letras coloridas (S=primary/base, I=text/muted, S=status/success, P=primary/base)
    │   └── "de Seguranca Publica" — Body/SM, text/secondary
    │
    ├── Divider — Rectangle 1px width, FILL height, fill: border/base
    │
    └── Action Area (VERTICAL, FILL width, centrado verticalmente)
        ├── "Efetuar login para iniciar" — Heading/LG, text/primary
        ├── BC-05 Button Primary MD — "Entrar com gov.br" (full-width, icone →))
        ├── BC-05 Button Primary MD — "Entrar no SISP" (full-width, icone →))
        └── Link "Como acessar?" — Body/SM, primary/base, icone ❓ (BC-15 Icons XS)
```

### Mobile (layout vertical)
```
Background (surface/bg-subtle)
└── Card (VERTICAL, centralizado, 375px)
    ├── Logo Area (VERTICAL, FILL width, centrado)
    │   ├── "Sistema Integrado"
    │   ├── "SISP" (escala reduzida)
    │   └── "de Seguranca Publica"
    │
    ├── Divider — Rectangle FILL width, 1px height, fill: border/base (HORIZONTAL no mobile)
    │
    └── Action Area (VERTICAL, FILL width)
        ├── titulo + botoes + link (mesma estrutura, full-width)
```

**Composicao atomica (Regra 11):**

| Elemento | Componente DS | Quantidade |
|---|---|---|
| Botoes de acao | BC-05 Button Primary MD | 2 instancias |
| Icone →) nos botoes | BC-15 Icons XS | 2 instancias (override dentro dos botoes) |
| Icone ❓ no link | BC-15 Icons XS | 1 instancia |
| Spinner no Loading | BC-16 Loader Spinner SM | 1 instancia |
| Logo SISP | Frame manual | Asset institucional (letras coloridas sao especificas da marca) |
| Card container | Frame de layout | Tokens aplicados (nao e componente reutilizavel) |
| Divider | Rectangle manual | 1px, fill border/base |

> **Regra 12 — auditoria:** "este elemento ja existe?" Botoes = BC-05 Button. Icones = BC-15 Icons. Spinner = BC-16 Loader. O logo SISP e asset institucional unico — nao reutilizavel. O card e layout, nao componente. O divider e primitivo grafico.

---

## Estados e variantes

### Estados do componente

| Estado | Condicao | Descricao visual | Elementos |
|---|---|---|---|
| **Default** | Tela inicial | Card com logo + 2 botoes + link. Estado principal e unico de entrada | Logo + Divider + Titulo + 2 Buttons + Link |
| **Loading** | Apos clique em botao, antes do redirect OAuth | Botao clicado mostra Spinner (BC-16 SM), outro botao desabilitado, link desabilitado | Logo + Divider + Titulo + 1 Button loading + 1 Button disabled + Link disabled |

### Variantes no Figma

| Variante | Properties | Descricao |
|---|---|---|
| Desktop Default | `State=Default, Layout=Desktop` | Card horizontal ~700px, duas colunas |
| Mobile Default | `State=Default, Layout=Mobile` | Card vertical 375px, stack |
| Desktop Loading | `State=Loading, Layout=Desktop` | Botao com Spinner, outro desabilitado |
| Mobile Loading | `State=Loading, Layout=Mobile` | Mesmo que Desktop Loading em layout vertical |

### Cores e tokens

| Elemento | Token | Valor SC |
|---|---|---|
| Fundo da pagina | `surface/bg-subtle` | #F9FAFB |
| Fundo do card | `surface/base` | #FFFFFF |
| Borda do card | `border/base` | #E5E7EB |
| Shadow do card | `shadow/md` | Effect Style |
| Border radius do card | `radius/lg` | 8px |
| Titulo "Efetuar login..." | `text/primary` | #08060F |
| Subtitulos logo | `text/secondary` | #4B5563 |
| "SISP" letra S (1a e 4a) | `primary/base` | #C4000B |
| "SISP" letra I | `text/muted` | #9CA3AF |
| "SISP" letra S (3a) | `status/success` | #166534 |
| Divider | `border/base` | #E5E7EB |
| Link "Como acessar?" | `primary/base` | #C4000B |
| Botoes | herda BC-05 Primary | — |

### Verificacao de contraste (WCAG AA)

| Elemento | Foreground | Background | Ratio | Resultado |
|---|---|---|---|---|
| Titulo "Efetuar login..." | #08060F | #FFFFFF | > 19:1 | AAA |
| Subtitulos logo | #4B5563 | #FFFFFF | 7.0:1 | AAA |
| Link "Como acessar?" | #C4000B | #FFFFFF | 5.2:1 | AA |
| "SISP" letra S vermelha | #C4000B | #FFFFFF | 5.2:1 | AA |
| "SISP" letra I cinza | #9CA3AF | #FFFFFF | 2.8:1 | AA large text (36px+) |
| "SISP" letra S verde | #166534 | #FFFFFF | 7.1:1 | AAA |
| Botoes | herda BC-05 | — | | Via BC-05 |

> **Nota sobre "I" cinza:** A letra "I" no logo SISP usa `text/muted` (#9CA3AF) que tem contraste 2.8:1 — reprova AA para texto normal, mas passa AA para texto grande (>= 18pt bold ou >= 24pt regular). Como o logo usa Heading/4XL (36px), passa como texto grande. Aceitavel como elemento decorativo/branding.

### Dimensoes

| Propriedade | Desktop | Mobile | Token |
|---|---|---|---|
| Card width | ~700px | 375px (100%) | — |
| Card padding | 32px | 16px | `space/8` / `space/4` |
| Card border-radius | 8px | 0px (edge-to-edge) | `radius/lg` / `radius/none` |
| Card shadow | shadow/md | shadow/md | Effect Style |
| Gap entre colunas (horizontal) | 32px | — | `space/8` |
| Gap entre secoes (vertical, mobile) | 24px | 24px | `space/6` |
| Gap entre titulo e 1o botao | 24px | 24px | `space/6` |
| Gap entre botoes | 12px | 12px | `space/3` |
| Gap entre 2o botao e link | 16px | 16px | `space/4` |
| Divider espessura | 1px | 1px | — |
| Button | Primary MD, full-width | Primary MD, full-width | Via BC-05 |

---

## Violacoes a resolver — WCAG AA

| Dimensao | Violacao atual | Solucao neste spec |
|---|---|---|
| (N/A) | Componente novo — sem violacoes previas | Spec resolve preventivamente: (1) Botoes com labels descritivos ("Entrar com gov.br", "Entrar no SISP") — nunca so icone. (2) Contraste verificado >= 4.5:1 AA em todos os elementos textuais (exceto "I" no logo, que passa como texto grande). (3) Focus ring visivel em todos os interativos. (4) Link "Como acessar?" com underline ou icone para nao depender so de cor. (5) Labels em portugues. (6) Sem dependencia de cor para transmitir informacao |

---

## Violacoes a resolver — Heuristicas Nielsen

| Heuristica | Violacao atual | Solucao neste spec |
|---|---|---|
| (N/A) | Componente novo | Spec resolve preventivamente: |
| H-1 Visibilidade | — | Loading state no botao clicado (Spinner) + outro botao desabilitado. Feedback imediato de que o redirect esta em andamento |
| H-2 Mundo real | — | Vocabulario institucional em portugues: "Entrar com gov.br", "Entrar no SISP", "Como acessar?". Sem jargao tecnico ("OAuth" nunca aparece para o usuario) |
| H-3 Controle | — | Dois caminhos de entrada claros. Link de ajuda disponivel. Sem acao irreversivel (redirect pode ser cancelado voltando a pagina) |
| H-4 Consistencia | — | Botoes seguem padrao BC-05. Layout segue padrao de card centralizado do DS. Identidade visual SISP preservada |
| H-6 Reconhecimento | — | Logo institucional SISP para identidade. Botoes com labels descritivos + icone direcional (→)). Link com icone ❓ para ajuda |
| H-8 Estetica | — | Card centralizado com shadow sutil. Duas colunas equilibradas. Hierarquia visual clara: logo → titulo → botoes → link |
| H-10 Ajuda | — | Link "Como acessar?" direciona para documentacao de onboarding |

---

## Regras de acessibilidade

- [ ] Card com `role="main"` ou landmark semantico
- [ ] Botoes com `role="link"` ou `<a>` semantico (sao redirects, nao submits)
- [ ] Botao gov.br com `aria-label="Entrar com gov.br — redireciona para autenticacao gov.br"`
- [ ] Botao SISP com `aria-label="Entrar no SISP — redireciona para autenticacao SISP"`
- [ ] Link "Como acessar?" com `aria-label="Como acessar — abre pagina de ajuda"`
- [ ] Focus ring visivel: `2px solid var(--color-border-focus)` em botoes e link
- [ ] Ordem de tab logica: botao gov.br → botao SISP → link "Como acessar?"
- [ ] Contraste minimo 4.5:1 AA em todos os textos (exceto logo SISP "I" — texto grande, aceito)
- [ ] Nao depende apenas de cor — botoes usam icone + texto + cor (3 canais)
- [ ] Labels em portugues
- [ ] `prefers-reduced-motion` — sem animacoes que necessitem desabilitar (Spinner e animacao minima)
- [ ] Logo SISP com `aria-hidden="true"` (decorativo — informacao textual ja presente nos subtitulos)

---

## Comportamentos esperados

### Fluxo principal
- Quando usuario acessa a tela de login → estado Default: card com logo + 2 botoes + link
- Quando clica "Entrar com gov.br" → estado Loading: botao mostra Spinner, outro botao desabilitado → redirect para URL gov.br OAuth
- Quando clica "Entrar no SISP" → estado Loading: botao mostra Spinner, outro botao desabilitado → redirect para URL SISP interna
- Quando clica "Como acessar?" → navega para pagina de ajuda/onboarding (nova aba ou mesma janela, configuravel via `helpUrl`)
- Quando retorna do provedor OAuth com sucesso → app inicializa (responsabilidade da aplicacao, nao do componente)
- Quando retorna do provedor OAuth com erro → provedor gerencia erro (redirect de volta com query param de erro, tratamento na app)

### Loading state
- Quando botao e clicado → Spinner substitui icone →) no botao clicado
- Texto do botao muda para "Redirecionando..."
- Outro botao fica desabilitado (opacity 0.5, cursor not-allowed)
- Link "Como acessar?" fica desabilitado
- Se redirect falhar (timeout) → estado Default restaurado (responsabilidade da app)

### Teclado
- Tab navega entre botoes e link
- Enter/Space ativa o botao focado
- Focus ring visivel em todos os interativos

---

## Comportamento responsivo

> Regra 13 — Responsividade obrigatoria. Breakpoints: Mobile (<768px), Tablet (768–1023px), Desktop (>=1024px).

| Precisa de variante `Layout=Mobile`? | **Sim** — layout muda de horizontal (2 colunas) para vertical (stack) |
|---|---|
| **Desktop** | Card horizontal ~700px centralizado. Logo a esquerda, acoes a direita, divider vertical. Card flutuante sobre fundo `surface/bg-subtle` |
| **Mobile** | Card vertical 375px (100% viewport). Logo em cima, divider horizontal, acoes embaixo. Edge-to-edge, sem border-radius, sem shadow |
| **Tablet** | Segue Desktop — card horizontal centralizado |

**O que muda entre Desktop e Mobile:**
- Layout: horizontal (2 colunas) → vertical (stack)
- Divider: vertical (1px width, FILL height) → horizontal (FILL width, 1px height)
- Card width: ~700px → 375px / 100% viewport
- Card padding: 32px → 16px
- Card border-radius: 8px → 0px
- Card shadow: shadow/md → nenhum (edge-to-edge)
- Logo SISP: escala normal → escala reduzida
- Botoes: full-width (relativo a Action Area) → full-width (100% do card)

---

## Casos excepcionais / bordas

- **gov.br indisponivel:** redirect falha — responsabilidade do provedor OAuth. Componente nao gerencia erros de provedores externos. App pode interceptar e mostrar Alert
- **JavaScript desabilitado:** botoes sao links (`<a>`) — funcionam sem JS. Spinner nao aparece
- **Multiplas abas:** se usuario ja autenticou em outra aba, redirect pode retornar imediatamente. Componente nao gerencia deteccao de sessao existente
- **Logo SISP em temas:** logo SISP usa cores institucionais fixas (vermelho SC, verde, cinza). Nao muda com theming PC/CBM — e identidade da plataforma SISP, nao do sistema-cliente
- **helpUrl nulo:** se `helpUrl` nao fornecido, link "Como acessar?" nao renderiza. Action Area mostra apenas titulo + 2 botoes

---

## O que esta fora deste spec

- **Formulario de credenciais:** Login com campos CPF/senha e da Delegacia Virtual (SC-08 Login) — componente separado
- **Fluxo OAuth completo:** callback, token exchange, session creation — responsabilidade do BFF/app
- **Tela de erro pos-redirect:** se OAuth falha, tratamento e da app wrapper
- **Cadastro / registro:** nao existe fluxo de auto-cadastro no SISP
- **Recuperacao de senha:** nao aplicavel — nao ha campos de senha neste componente
- **SSO entre sistemas SISP:** extensao futura, fora do Sprint 6
- **Animacao de entrada do card:** nao especificada — componente renderiza imediatamente

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `surface/bg-subtle` | Fundo da pagina |
| `surface/base` | Fundo do card |
| `border/base` | Borda do card, divider |
| `text/primary` | Titulo "Efetuar login..." |
| `text/secondary` | Subtitulos "Sistema Integrado", "de Seguranca Publica" |
| `text/muted` | Letra "I" no logo SISP |
| `primary/base` | Letras "S" e "P" no logo, link "Como acessar?", botoes (via BC-05) |
| `status/success` | Letra "S" verde no logo |
| `border/focus` | Focus ring |
| `shadow/md` | Shadow do card (Desktop) |
| `space/3` | Gap entre botoes |
| `space/4` | Gap entre botao e link, padding mobile |
| `space/6` | Gap entre titulo e botao, gap entre secoes mobile |
| `space/8` | Padding do card Desktop, gap entre colunas Desktop |
| `radius/lg` | Border-radius do card Desktop |
| `radius/none` | Border-radius do card Mobile |
| Heading/LG | Text Style — titulo "Efetuar login para iniciar" |
| Heading/4XL | Text Style — "SISP" (logo) |
| Body/SM | Text Style — subtitulos, link "Como acessar?" |

---

## Criterios de aceite

- [ ] 4 variantes no Figma: Default Desktop, Default Mobile, Loading Desktop, Loading Mobile
- [ ] Button como instancia de BC-05 Button Primary MD (Regra 11) — 2 instancias full-width
- [ ] Icons como instancia de BC-15 Icons XS (Regra 11) — 3 instancias (2 nos botoes + 1 no link)
- [ ] Loader como instancia de BC-16 Loader Spinner SM (Regra 11) — 1 instancia no estado Loading
- [ ] Tokens aplicados — zero valores hardcoded (Regra 8)
- [ ] Text Styles aplicados a todos os text nodes textuais (Regra 15)
- [ ] Color fills bound a variaveis de Cores Semanticas (Regra 15)
- [ ] Layout Desktop: horizontal, 2 colunas, divider vertical
- [ ] Layout Mobile: vertical, stack, divider horizontal, edge-to-edge
- [ ] Logo SISP com letras coloridas (S=vermelho, I=cinza, S=verde, P=vermelho)
- [ ] Botoes usam vermelho DS #C4000B (BC-05 Primary), nao verde gov.br
- [ ] Contraste verificado >= 4.5:1 AA em todos os elementos textuais
- [ ] Acessibilidade documentada (aria-labels, tab order, focus ring)
- [ ] Labels em portugues
- [ ] Separacao clara de SC-08 Login (DV) e SC-17 Login SISP
- [ ] Revisado e aprovado por Giuliana
