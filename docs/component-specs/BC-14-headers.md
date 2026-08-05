---
component-id: BC-14
component-name: Headers
type: Base
status: approved
sprint: 4
approved-by: [Giuliana]
approved-date: [2026-07-14]
figma-node-id: [175:432]
---

# Component Spec — Headers

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → BC-14 (wcag aten · vis aten)
> - `docs/analyses/nielsen-analysis.md` → BC-14 (H-4 **crit** · H-1 aten · H-6 aten · H-8 aten)
> - `docs/analyses/inventory.md` → BC-14

---

## O que é

Header é o componente de cabeçalho institucional do DS SISP. Presente em toda página do sistema, é o primeiro elemento visual e contém a identidade do produto (logo + título), navegação primária e informações do usuário autenticado. Na DV, o header identifica o sistema, mostra qual módulo o policial está acessando, e dá acesso ao menu de navegação. Atualmente Bootstrap, com logo SISP não alinhado ao padrão SC gov, sem breakpoints documentados e comportamento mobile indefinido.

**Decisão de redesign:** header dividido em 2 zonas — Identity Bar (fundo escuro, contraste >15:1) + Nav Bar (fundo claro, usando instância de BC-26 Tabs). Resolve legibilidade percebida (fundo vermelho saturado causava fadiga visual) e aplica Regra 12 (auditoria de componentes — nav items são funcionalmente idênticos a Tabs).

---

## Audiência de uso

- **Policial na DV:** vê o header em toda tela — identifica o sistema, o módulo atual, e acessa navegação
- **Devs CiASC / terceiros:** usam o header como landmark principal da página, precisam de API clara para título, navegação e identidade visual
- **POs (Sommer/Holiwod):** precisam que o header reflita a identidade institucional correta (padrão SC gov)

---

## Props / API

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `title` | `string` | não | — | Título do sistema/módulo exibido ao lado do logo |
| `showNav` | `boolean` | não | `true` | Controla visibilidade da nav bar (BC-26 Tabs) |
| `tabs` | `TabItem[]` | não | `[]` | Abas de navegação — reutiliza a API de BC-26 Tabs |
| `user` | `UserInfo` | não | — | Dados do usuário autenticado (nome, avatar, role) |
| `logoSrc` | `string` | não | Logo SISP padrão | Caminho para o logo — permite override por tema (PC, CBM) |
| `onMenuToggle` | `Function` | não | — | Callback ao clicar no hamburger (mobile) |

**TabItem:** (reutiliza interface de BC-26 Tabs)
```typescript
interface TabItem {
  label: string;          // Texto da aba — curto, 1-3 palavras
  disabled?: boolean;     // Aba desabilitada
  icon?: string;          // Classe Font Awesome opcional
}
```

**UserInfo:**
```typescript
interface UserInfo {
  name: string;           // Nome do usuário autenticado
  role?: string;          // Cargo/perfil — ex: "Delegado", "Escrivão"
  avatarSrc?: string;     // URL do avatar (fallback: iniciais)
}
```

**Convenção Angular:**
```html
<sisp-lib-header [sispLibHeaderConfig]="config"></sisp-lib-header>
```

**Exemplo de uso:**
```typescript
config: SispLibHeaderConfig = {
  title: 'Delegacia Virtual',
  showNav: true,
  tabs: [
    { label: 'Dados Gerais', icon: 'fa-solid fa-file-lines' },
    { label: 'Pessoas Envolvidas', icon: 'fa-solid fa-users' },
    { label: 'Objetos' },
    { label: 'Anexos' }
  ],
  user: {
    name: 'Del. Maria Silva',
    role: 'Delegada'
  }
};
```

---

## Anatomia do componente

### Desktop (≥ 1024px) — 2 zonas empilhadas

```
┌────────────────────────────────────────────────────────────────────┐
│  [Logo]  Título do Sistema                   Del. Maria Silva [MS] │  ← Identity Bar (escura)
├────────────────────────────────────────────────────────────────────┤
│  Dados Gerais    Pessoas    Objetos    Anexos                      │  ← Nav Bar (BC-26 Tabs)
│  ═══════════                                                       │
└────────────────────────────────────────────────────────────────────┘
```

- **Identity Bar:** fundo escuro (`--color-text-primary` #111827), 48px altura. Logo + título à esquerda, user zone à direita
- **Nav Bar:** fundo branco (`--color-surface-bg`), usa **instância de BC-26 Tabs** (style Underline). Separador inferior `1px solid --color-border-base`

### Mobile (< 1024px) — apenas identity bar

```
┌──────────────────────────────────┐
│  [☰]  [Logo]  Título        [MS] │  ← Identity Bar (escura)
└──────────────────────────────────┘
```

- **Hamburger (☰):** abre nav (Tabs) em sidebar/dropdown
- **Nav Bar oculta:** acessível via hamburger menu

---

## Estados e variantes

### Variantes de layout

| Variante | Descrição | Quando usar |
|---|---|---|
| **Desktop** | Identity bar + nav bar (Tabs) | Viewport ≥ 1024px |
| **Mobile** | Identity bar com hamburger (nav oculta) | Viewport < 1024px |

### Estados dos nav items

Delegados ao componente BC-26 Tabs — não recriados no Header. O Header usa uma instância de Tabs com seus estados nativos (Default, Hover, Active, Focus, Disabled).

### Cores

| Elemento | Token | Valor |
|---|---|---|
| Identity bar fundo | `--color-text-primary` | #111827 (escuro) |
| Identity bar texto | `--color-text-inverse` | #FFFFFF |
| Nav bar fundo | `--color-surface-bg` | #FFFFFF |
| Nav bar separador | `--color-border-base` | #E5E7EB |
| Nav items | Delegados a BC-26 Tabs | — |

### Verificação de contraste (WCAG AA)

| Elemento | Texto | Fundo | Ratio | Resultado |
|---|---|---|---|---|
| Título sobre identity bar | #FFFFFF | #111827 | >15:1 | ✅ AAA |
| User name sobre identity bar | #FFFFFF | #111827 | >15:1 | ✅ AAA |
| Nav items (Tabs) | Delegados a BC-26 | #FFFFFF | Verificado no spec BC-26 | ✅ AA |

### Dimensões

| Propriedade | Desktop | Mobile | Token |
|---|---|---|---|
| Identity bar altura | 48px | 56px | — |
| Nav bar altura | Hug (definida pelo BC-26 Tabs) | — (oculta) | — |
| Padding horizontal identity | 24px | 16px | `--space-6` / `--space-4` |
| Padding horizontal nav bar | 24px | — | `--space-6` |
| Max-width do conteúdo | 1200px | 100% | — |
| Logo height | 28px | 24px | — |
| Gap logo → título | 12px | 8px | `--space-3` / `--space-2` |
| Font size título | 16px | 14px | `--text-base` / `--text-sm` |
| Font weight título | 700 | 700 | `--font-bold` |
| Avatar size | 28px | 28px | — |
| Hamburger touch target | — | 44px × 44px | — |

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução neste spec |
|---|---|---|
| Contraste (wcag aten) | Logo sobre fundo escuro — contraste não verificado | Identity bar usa fundo `--color-text-primary` (#111827). Texto branco: >15:1 ✅ AAA. Muito superior ao mínimo AA. Resolve também a legibilidade percebida (fundo vermelho saturado causava fadiga visual) |
| Visual (vis aten) | Breakpoints não documentados, comportamento mobile indefinido | 2 variantes explícitas: Desktop (≥ 1024px, identity + nav bar) e Mobile (< 1024px, identity bar + hamburger). Dimensões documentadas por breakpoint |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução neste spec |
|---|---|---|
| H-1 Visibilidade (aten) | Sem indicação visual de módulo/página ativa | Nav bar usa BC-26 Tabs — aba ativa com 3 canais (underline + cor + bold). Resolve automaticamente via composição |
| H-4 Consistência (**crit**) | Logo SISP não alinhado com padrão SC gov. Sem documentação de padrão de header | Logo segue padrão SC gov (versão branca sobre fundo institucional escuro). Header padronizado com zones fixas. `logoSrc` permite override por tema. Nav bar reutiliza BC-26 Tabs — consistência com navegação em todo o DS |
| H-6 Reconhecimento (aten) | Usuário não sabe qual módulo está acessando sem ler título | Tabs com aba ativa visível. Título do sistema sempre presente na identity bar |
| H-8 Estética (aten) | Visual Bootstrap genérico | Identity bar escura com alto contraste. Nav bar limpa usando Tabs padronizadas. Separação clara de zonas. Tipografia com hierarquia (título bold) |

---

## Regras de acessibilidade

- [ ] Header com `role="banner"` (ou `<header>` semântico)
- [ ] Nav bar com `role="navigation"` e `aria-label="Navegação principal"` — delegado ao BC-26 Tabs interno
- [ ] Hamburger button com `aria-label="Abrir menu de navegação"` e `aria-expanded="true|false"`
- [ ] Logo com `alt` descritivo: "Logo [Nome do Sistema]"
- [ ] **Navegação por teclado:**
  - `Tab` move entre elementos focáveis (logo, tabs, user menu, hamburger)
  - Nav items herdam navegação de BC-26 Tabs (`→` `←` `Home` `End`)
  - `Enter` / `Space` ativa links e botões
  - `Escape` fecha menus abertos (user dropdown, mobile nav)
- [ ] Focus ring visível: `2px solid var(--color-border-focus)` com `outline-offset: 2px`
- [ ] Contraste mínimo 4.5:1 para texto sobre identity bar — verificado (>15:1 AAA)
- [ ] Skip link (`Pular para conteúdo principal`) como primeiro elemento focável do header (visível apenas no foco)
- [ ] Logo é link para home com `aria-label` descritivo

---

## Comportamentos esperados

- Quando viewport ≥ 1024px → exibe variante Desktop: identity bar + nav bar (BC-26 Tabs)
- Quando viewport < 1024px → exibe variante Mobile: identity bar com hamburger. Nav bar oculta
- Quando usuário clica no hamburger → nav abre (sidebar ou dropdown com Tabs), hamburger muda para ×, `aria-expanded="true"`. Callback `onMenuToggle` disparado
- Quando `user` definido → exibe nome/avatar à direita da identity bar. Click abre dropdown
- Quando `tabs` definido com item ativo → BC-26 Tabs exibe aba ativa com indicador visual nativo
- Quando `logoSrc` fornecido → substitui logo padrão SISP (theming por cliente)
- Quando `showNav = false` → nav bar oculta (header simplificado — ex: tela de login)
- Quando header está em posição fixa (sticky) → sombra `--shadow-md` aparece ao rolar
- Quando `title` é muito longo → trunca com `text-overflow: ellipsis` em mobile

---

## Composição com outros componentes

| Componente | Relação | Composição no Figma (Regra 11/12) |
|---|---|---|
| **BC-26 Tabs** | **Nav bar é uma instância de Tabs (Underline)** | **Instância direta** — nav items são tabs, não recriação. Regra 12 aplicada |
| BC-10 Dropdowns | User menu abre como dropdown | Composição futura com BC-10 quando disponível |
| BC-20 Navigation Canvas | Nav canvas complementa header como sidebar em mobile | Interação via `onMenuToggle` |
| BC-15 Icons | Ícones nos tab items e hamburger | Font Awesome — `aria-hidden="true"` |

---

## Mapeamento de retrocompatibilidade

| Prop atual | Mapeamento novo | Nota |
|---|---|---|
| `title` | `title` | Mantido |
| `showNav` | `showNav` | Mantido — controla visibilidade da nav bar |
| — | `tabs` (novo) | Substitui `navItems`. Reutiliza interface de BC-26 Tabs |
| — | `user` (novo) | Informações do usuário via config |
| — | `logoSrc` (novo) | Override de logo para theming |
| — | `onMenuToggle` (novo) | Callback para integração com sidebar |

---

## Casos excepcionais / bordas

- **Sem título (`title` vazio):** identity bar exibe apenas logo — sem texto ao lado
- **Sem navegação (`showNav = false`, `tabs = []`):** apenas identity bar visível (logo + título + user)
- **Sem usuário (`user` não definido):** user zone não renderiza. Header público (pré-login)
- **Muitas tabs (overflow):** delegado ao comportamento de overflow de BC-26 Tabs (scroll horizontal)
- **Mobile (< 1024px):** hamburger substitui nav bar. Touch target 44px. Título trunca se necessário
- **Header fixo (sticky):** posicionado no topo com `z-index` alto. Sombra aparece ao rolar
- **Múltiplos temas:** `logoSrc` permite logo customizado. Identity bar usa `--color-text-primary` — tema PC pode override para dourado escuro via token

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-text-primary` | Fundo identity bar (#111827) |
| `--color-text-inverse` | Texto sobre identity bar (#FFFFFF) |
| `--color-surface-bg` | Fundo nav bar (#FFFFFF) |
| `--color-border-base` | Separador inferior nav bar |
| `--color-border-focus` | Ring de foco |
| `--font-body` | Família tipográfica |
| `--text-base` | Font size título desktop (16px) |
| `--text-sm` | Font size título mobile (14px) |
| `--font-bold` | Peso título (700) |
| `--space-2` | Gap logo mobile (8px) |
| `--space-3` | Gap logo desktop (12px) |
| `--space-4` | Padding horizontal mobile (16px) |
| `--space-6` | Padding horizontal desktop (24px) |
| `--shadow-md` | Sombra header sticky ao rolar |

---

## O que está fora deste spec

- **Mega menu / sub-navegação:** se necessário, especificar como extensão separada
- **Breadcrumbs no header:** breadcrumbs são componente separado abaixo do header
- **Header transparente / overlay sobre hero image:** padrão de marketing, não de sistema policial
- **Search bar integrada no header:** se necessário, composição com input
- **Notificações (sino/badge):** composição com BC-04 Badge e SC-10 Notificações — slot futuro

---

## Critérios de aceite

- [ ] 2 variantes de layout (Desktop, Mobile) no Figma
- [ ] Identity bar com fundo escuro (#111827) — contraste >15:1 AAA
- [ ] Nav bar usando instância de BC-26 Tabs (Regra 11/12) — não recriação
- [ ] Logo versão branca sobre fundo escuro — contraste verificado
- [ ] Skip link documentado
- [ ] ARIA documentado: `role="banner"`, `aria-current="page"`, `aria-expanded`
- [ ] Navegação por teclado documentada — herda de BC-26 Tabs
- [ ] Contraste verificado — identity bar >15:1, nav bar via BC-26
- [ ] Composição atômica verificada: nav = Tabs instance (Regra 12)
- [ ] Violação WCAG AA (wcag aten · vis aten) resolvida
- [ ] Violações Nielsen (H-4 **crit** · H-1 aten · H-6 aten · H-8 aten) resolvidas
- [ ] Mapeamento de retrocompatibilidade documentado
- [ ] Revisado e aprovado por Giuliana
