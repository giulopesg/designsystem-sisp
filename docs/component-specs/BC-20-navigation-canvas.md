---
component-id: BC-20
component-name: Navigation Canvas
type: Base
status: approved
sprint: 4.1
approved-by: [Giuliana]
approved-date: [2026-07-16]
figma-node-id: [275:590]
---

# Component Spec — Navigation Canvas

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → BC-20 (cor aten · vis aten — item ativo sem diferenciacao alem de cor)
> - `docs/analyses/nielsen-analysis.md` → BC-20 (H-1 aten · H-4 aten · H-6 aten · H-7 aten · H-8 aten)
> - `docs/analyses/inventory.md` → BC-20

---

## O que e

Navigation Canvas e o componente de navegacao lateral (sidebar) do DS SISP. Exibe a estrutura hierarquica de seções de um produto como menu vertical com items de primeiro nivel e submenus expandiveis. Na DV, o Navigation Canvas e o menu principal lateral — organiza modulos como Ocorrencias, Consultas, Cadastros, Relatorios. Pode ser colapsado para modo mini (apenas icones) em telas grandes, e transforma-se em drawer em mobile.

> **Regra 12 aplicada:** nav items do Navigation Canvas sao funcionalmente equivalentes a Tabs — selecao entre N opcoes com indicador ativo. No Figma, items usam o padrao visual de Tab Item (BC-26) adaptado para orientacao vertical. Icones sao instancias de BC-15 Icons.

---

## Audiencia de uso

- **Policial na DV:** usa o menu lateral para navegar entre modulos (Ocorrencias, Consultas, Cadastros, Relatorios). Precisa encontrar seções rapidamente e saber onde esta
- **Devs CiASC / terceiros:** configuram a arvore de navegacao com items, subitems, icones e rotas. Precisam de API com hierarquia e estado de collapse
- **POs (Sommer/Holiwod):** precisam que a navegacao acomode novos modulos sem perder clareza
- **Demilis:** revisara a estrutura de navegacao da DV (confirmado no inventario)

---

## Props / API

| Prop | Tipo | Obrigatorio | Padrao | Descricao |
|---|---|---|---|---|
| `items` | `NavItem[]` | sim | — | Arvore de items de navegacao |
| `collapsed` | `boolean` | nao | `false` | Modo colapsado (mini) — apenas icones visiveis |
| `activeItem` | `string` | nao | rota atual | ID ou rota do item ativo. Se nao definido, inferido do router |
| `collapsible` | `boolean` | nao | `true` | Permite colapsar/expandir via toggle |
| `header` | `string` | nao | — | Titulo do menu (ex: "Delegacia Virtual") |

**NavItem:**
```typescript
interface NavItem {
  id: string;            // Identificador unico
  label: string;         // Texto exibido — em portugues
  route?: string;        // Path Angular (quando e leaf node)
  icon?: string;         // Classe Font Awesome (obrigatorio para items de 1o nivel)
  children?: NavItem[];  // Sub-items (expande ao clicar)
  badge?: string;        // Badge de contagem (ex: notificacoes pendentes)
  disabled?: boolean;    // Item desabilitado
}
```

**Convencao Angular:**
```html
<sisp-lib-navigation-canvas [sispLibNavigationCanvasConfig]="config"></sisp-lib-navigation-canvas>
```

**Exemplo de uso:**
```typescript
config: SispLibNavigationCanvasConfig = {
  header: 'Delegacia Virtual',
  collapsible: true,
  items: [
    {
      id: 'dashboard',
      label: 'Painel',
      icon: 'fa-solid fa-gauge',
      route: '/dashboard'
    },
    {
      id: 'ocorrencias',
      label: 'Ocorrencias',
      icon: 'fa-solid fa-file-lines',
      badge: '3',
      children: [
        { id: 'novo-bo', label: 'Novo BO', route: '/ocorrencias/novo' },
        { id: 'meus-bos', label: 'Meus BOs', route: '/ocorrencias/meus' },
        { id: 'todos-bos', label: 'Todos os BOs', route: '/ocorrencias/todos' }
      ]
    },
    {
      id: 'consultas',
      label: 'Consultas',
      icon: 'fa-solid fa-magnifying-glass',
      children: [
        { id: 'pessoa', label: 'Por Pessoa', route: '/consultas/pessoa' },
        { id: 'veiculo', label: 'Por Veiculo', route: '/consultas/veiculo' },
        { id: 'registro', label: 'Por Registro', route: '/consultas/registro' }
      ]
    },
    {
      id: 'cadastros',
      label: 'Cadastros',
      icon: 'fa-solid fa-user-plus',
      route: '/cadastros'
    },
    {
      id: 'relatorios',
      label: 'Relatorios',
      icon: 'fa-solid fa-chart-bar',
      route: '/relatorios'
    }
  ]
};
```

---

## Anatomia do componente

### Expandido (default)
```
┌──────────────────────────┐
│  Delegacia Virtual   [«] │  ← header + toggle collapse
│  ────────────────────── │
│  🏠  Painel              │  ← item ativo (fundo + borda esquerda)
│  📄  Ocorrencias    (3)  │  ← item com badge e children
│      ├ Novo BO           │  ← sub-item (indentado)
│      ├ Meus BOs          │
│      └ Todos os BOs      │
│  🔍  Consultas      ▾   │  ← item com children (colapsado)
│  👤  Cadastros           │  ← item leaf
│  📊  Relatorios          │
└──────────────────────────┘
```

### Colapsado (mini)
```
┌──────┐
│  [»] │  ← toggle expand
│ ──── │
│  🏠  │  ← icone ativo (com indicador)
│  📄  │  ← icone com badge dot
│  🔍  │
│  👤  │
│  📊  │
└──────┘
```

- **Header:** titulo do menu + toggle collapse/expand
- **Nav items (1o nivel):** icone + label + badge/chevron opcional
- **Sub-items (2o nivel):** indentados, sem icone, com indicador de item ativo
- **Toggle:** botao com chevron « (colapsar) / » (expandir)
- **Separadores:** dividers opcionais entre grupos

---

## Estados e variantes

### Modos do canvas

| Modo | Descricao | Largura |
|---|---|---|
| **Expanded** | Menu completo com icones + labels | 240px |
| **Collapsed** | Modo mini — apenas icones | 64px |

### Estados dos nav items (1o nivel)

| Estado | Descricao visual | Tokens |
|---|---|---|
| **Default** | Icone + texto neutro | `text: --color-text-secondary` · `bg: transparent` |
| **Hover** | Fundo sutil | `bg: --color-bg-subtle` · `text: --color-text-primary` |
| **Active** | 3 canais: borda esquerda + fundo + bold | `border-left: 3px solid --color-primary` · `bg: --color-primary-muted` · `text: --color-primary` · `font-weight: --font-semibold` |
| **Focus** | Focus ring | `outline: 2px solid --color-border-focus` · `outline-offset: -2px` (inset) |
| **Expanded (com children)** | Chevron ▾ vira ▴, children visiveis | `text: --color-text-primary` |
| **Disabled** | Texto e icone opacos | `text: --color-text-muted` · `opacity: 0.5` |

### Estados dos sub-items (2o nivel)

| Estado | Descricao visual | Tokens |
|---|---|---|
| **Default** | Texto neutro, indentado | `text: --color-text-secondary` · `padding-left: 48px` |
| **Hover** | Fundo sutil | `bg: --color-bg-subtle` |
| **Active** | Texto primario + dot indicator | `text: --color-primary` · `font-weight: --font-semibold` · dot 6px antes do label |
| **Focus** | Focus ring | `outline: 2px solid --color-border-focus` |

### Cores

| Elemento | Token | Valor |
|---|---|---|
| Canvas fundo | `--color-surface` | #FFFFFF |
| Canvas borda direita | `--color-border` | #E5E7EB |
| Header texto | `--color-text-primary` | #08060F |
| Item default | `--color-text-secondary` | #4B5563 |
| Item hover fundo | `--color-bg-subtle` | #F9FAFB |
| Item ativo fundo | `--color-primary-muted` | #FEF2F2 |
| Item ativo texto | `--color-primary` | #C4000B |
| Item ativo borda esquerda | `--color-primary` | #C4000B |
| Chevron | `--color-text-muted` | #9CA3AF |
| Badge | instancia BC-04 Badge | — |
| Toggle | `--color-text-muted` → hover `--color-text-primary` | — |
| Focus ring | `--color-border-focus` | #C4000B |
| Sub-item dot ativo | `--color-primary` | #C4000B |
| Separador | `--color-border` | #E5E7EB |

### Verificacao de contraste (WCAG AA)

| Elemento | Texto | Fundo | Ratio | Resultado |
|---|---|---|---|---|
| Item default | #4B5563 | #FFFFFF | ~7.2:1 | ✅ AAA |
| Item ativo | #C4000B | #FEF2F2 | ~5.0:1 | ✅ AA |
| Item hover | #08060F | #F9FAFB | ~14.8:1 | ✅ AAA |
| Sub-item default | #4B5563 | #FFFFFF | ~7.2:1 | ✅ AAA |
| Borda esquerda ativa | #C4000B | #FEF2F2 | ~5.0:1 | ✅ AA (grafico 3:1) |

### Dimensoes

| Propriedade | Valor | Token |
|---|---|---|
| Canvas largura (expanded) | 240px | — |
| Canvas largura (collapsed) | 64px | — |
| Canvas height | 100vh (menos header) | — |
| Header height | 48px | — |
| Header padding horizontal | 16px | `--space-4` |
| Item height | 40px | — |
| Item padding horizontal | 16px | `--space-4` |
| Item padding vertical | 8px | `--space-2` |
| Gap icone → label | 12px | `--space-3` |
| Icone tamanho | 16px (MD) | — |
| Font size | 14px | `--text-sm` |
| Font weight default | 400 | `--font-regular` |
| Font weight ativo | 600 | `--font-semibold` |
| Active border-left | 3px | — |
| Sub-item indent | 48px | -- |
| Sub-item dot size | 6px | — |
| Sub-item gap dot → label | 8px | `--space-2` |
| Chevron size | 12px | — |
| Badge margin left | 8px | `--space-2` |
| Separador margin vertical | 8px | `--space-2` |
| Toggle button | 32×32px | — |
| Transition collapse | 200ms ease | `--transition-normal` |

---

## Violacoes a resolver — WCAG AA

| Dimensao | Violacao atual | Solucao neste spec |
|---|---|---|
| Uso de cor (cor aten) | Item ativo sem diferenciacao alem de cor | 3 canais para item ativo de 1o nivel: (1) borda esquerda 3px, (2) fundo --color-primary-muted, (3) texto bold + cor primaria. Sub-item ativo: dot indicator + bold + cor primaria |
| Visual (vis aten) | Estados e indicadores visuais insuficientes | Hover com fundo sutil, focus ring inset, expanded/collapsed com chevron direcional, active com borda + fundo. Todos os estados visivelmente distintos |

---

## Violacoes a resolver — Heuristicas Nielsen

| Heuristica | Violacao atual | Solucao neste spec |
|---|---|---|
| H-1 Visibilidade (aten) | Item ativo nao claramente visivel | 3 canais visuais para item ativo. Chevron ▾/▴ indica expanded/collapsed. Badge com contagem visivel. Modo collapsed mantem indicador ativo (borda esquerda + fundo no icone) |
| H-4 Consistencia (aten) | Sem padrao documentado | Nav items seguem padrao visual de BC-26 Tab Item (Regra 12). Mesma hierarquia de estados (default/hover/active/disabled). Composicao atomica com BC-15 Icons e BC-04 Badge |
| H-6 Reconhecimento (aten) | Usuario precisa memorizar localizacao | Icones por modulo reforçam reconhecimento (fa-file-lines = Ocorrencias, fa-magnifying-glass = Consultas). Labels descritivos em portugues. Hierarquia visual com indentacao |
| H-7 Flexibilidade (aten) | Sem atalhos de acesso | Collapse/expand toggle para personalizar espaco. Navegacao por teclado completa (Tab, Enter, ←/→ para expand/collapse children, ↑/↓ entre items). Modo collapsed para usuarios experientes que conhecem os icones |
| H-8 Estetica (aten) | Visual generico | Fundo branco limpo, borda direita sutil, espacamento por tokens, transicao suave de collapse (200ms). Hierarquia visual clara com indentacao e separadores |

---

## Regras de acessibilidade

- [ ] Container com `<nav>` semantico e `aria-label="Menu principal"` (ou descricao contextual)
- [ ] Lista de items com `<ul>` / `<li>`
- [ ] Item ativo com `aria-current="page"` (leaf) ou `aria-expanded="true"` (com children abertos)
- [ ] Items com children: `aria-expanded="true|false"` para indicar estado
- [ ] Items desabilitados com `aria-disabled="true"` e `tabindex="-1"`
- [ ] **Navegacao por teclado:**
  - `Tab` entra no menu, foca primeiro item
  - `↑` / `↓` navega entre items
  - `→` expande item com children (ou entra nos children)
  - `←` colapsa item expandido (ou volta ao parent)
  - `Enter` / `Space` navega para a rota (leaf) ou toggle expand/collapse (parent)
  - `Home` → primeiro item. `End` → ultimo item
  - `Escape` → colapsa todos os submenus abertos
- [ ] Focus ring visivel: `2px solid var(--color-border-focus)` inset
- [ ] Contraste minimo 4.5:1 em todos os estados interativos — verificado
- [ ] Modo collapsed: icones com `aria-label` do item (tooltip nativo)
- [ ] Toggle collapse com `aria-label="Colapsar menu"` / `"Expandir menu"`
- [ ] Labels em portugues

---

## Comportamentos esperados

- Quando usuario clica em item leaf (sem children) → navega para a rota via Angular Router, atualiza indicador ativo
- Quando usuario clica em item com children → toggle expand/collapse dos sub-items. Chevron muda ▾ ↔ ▴
- Quando URL muda externamente (browser back/forward) → item ativo atualiza, parent auto-expande se sub-item ativo
- Quando `collapsed = true` → canvas reduz para 64px, exibe apenas icones. Hover sobre icone exibe tooltip com label
- Quando toggle collapse clicado → transicao suave (200ms ease) entre expanded/collapsed. Labels fazem fade out/in
- Quando item com children esta expandido e usuario clica em sub-item → navega para rota, sub-item fica ativo, parent permanece expandido
- Quando `badge` fornecido → badge exibido ao lado do label (instancia BC-04 Badge Neutral SM). No modo collapsed, dot vermelho sobre icone
- Quando item tem `disabled = true` → estilo desabilitado, nao responde a clique, pulado na navegacao por teclado
- Quando viewport < 1024px (mobile) → canvas vira drawer (BC-22 Offcanvas pattern): oculto por padrao, aberto via hamburger do BC-14 Header
- Quando muitos items (scroll necessario) → scroll interno com fade indicator no topo/base

---

## Composicao com outros componentes

| Componente | Relacao | Composicao no Figma (Regra 11/12) |
|---|---|---|
| **BC-26 Tab Item** | **Nav items seguem padrao visual de Tab Item** | **Referencia de padrao** — items de 1o nivel replicam estrutura label + estados. Adaptado para vertical + icone obrigatorio |
| **BC-15 Icons** | **Icone por modulo em cada item de 1o nivel** | **Font Awesome** — `aria-hidden="true"`, icone como reforço visual |
| **BC-04 Badges** | **Badge de contagem nos items** | **Instancia direta** — Badge Neutral SM |
| BC-14 Headers | Header + Navigation Canvas formam o frame principal da aplicacao | Composicao de layout — Canvas a esquerda, conteudo a direita |
| BC-22 Offcanvas | Em mobile, canvas usa padrao Offcanvas (drawer lateral) | Composicao de uso |

> **Regra 12 — auditoria:** "este elemento ja existe?" Nav items sao funcionalmente equivalentes a Tabs (selecao entre N opcoes). Porem, Navigation Canvas adiciona: hierarquia (children), orientacao vertical, collapse/expand, e icones obrigatorios. No Figma, o padrao visual de Tab Item e referencia, mas o componente e distinto.

---

## Mapeamento de retrocompatibilidade

| Prop atual | Mapeamento novo | Nota |
|---|---|---|
| `items` | `items: NavItem[]` | Extendido com `id`, `badge`, `disabled` |
| `collapsed` | `collapsed` | Mantido |
| — | `activeItem` (novo) | Modo controlado |
| — | `collapsible` (novo) | Permite desabilitar toggle |
| — | `header` (novo) | Titulo do menu |

---

## Comportamento responsivo

> Regra 13 — Responsividade obrigatória. Breakpoints: Mobile (<768px), Tablet (768–1023px), Desktop (≥1024px).

| Precisa de variante `Layout=Mobile`? | **Sim** — sidebar fixa não cabe em viewport mobile |
|---|---|
| **Desktop** | Sidebar fixa lateral. Expanded: 240px com labels. Collapsed: 64px icon-only |
| **Mobile** | Drawer overlay 280px. Abre por cima do conteúdo (slide-in da esquerda). Backdrop semitransparente. Sem modo Collapsed — nav está escondida ou aberta |
| **Tablet** | Segue Desktop — sidebar fixa com toggle |

**O que muda entre Desktop e Mobile:**
- Comportamento: sidebar fixa → drawer overlay (slide-in)
- Largura Expanded: 240px → 280px (mais espaço para touch targets)
- Modo Collapsed: existe no Desktop (64px icon-only) → não existe no Mobile (hamburger no Header abre/fecha)
- Backdrop: não tem no Desktop → semitransparente no Mobile (para fechar ao clicar fora)
- Composição atômica: mantida (BC-04 Badge + BC-15 Icons são instâncias)

**Variantes no Figma:** 3 variantes (2 modos Desktop + 1 modo Mobile)
- `Mode=Expanded, Layout=Desktop` (240px sidebar fixa)
- `Mode=Collapsed, Layout=Desktop` (64px icon-only)
- `Mode=Expanded, Layout=Mobile` (280px drawer overlay)

---

## Casos excepcionais / bordas

- **0 items:** componente nao renderiza
- **Apenas 1 item:** renderiza normalmente — valido para produtos simples
- **3 niveis de hierarquia:** spec cobre 2 niveis (parent → children). 3o nivel nao e recomendado — simplificar arquitetura de informacao
- **Item sem icone (1o nivel):** icone e obrigatorio para 1o nivel — fallback para fa-circle se nao fornecido
- **Item sem rota (sem children):** item inerte — nao clicavel, exibido como label de grupo
- **Label longo:** trunca com ellipsis no modo expanded. Tooltip com label completo
- **Badge com numero alto (> 99):** exibe "99+" como truncamento
- **Mobile < 1024px:** canvas oculto, ativado por hamburger do Header. Overlay escurece conteudo. Swipe left ou click fora fecha
- **Modo collapsed + item ativo com children:** indica item ativo com borda esquerda no icone. Hover expande tooltip com sub-items
- **Resize de janela (desktop ↔ mobile):** transicao suave. Se resize < 1024px, canvas fecha automaticamente
- **Impressao:** canvas oculto em `@media print`

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-surface` | Fundo canvas |
| `--color-bg-subtle` | Fundo hover |
| `--color-primary-muted` | Fundo item ativo |
| `--color-primary` | Texto/borda/dot item ativo |
| `--color-text-primary` | Header, item hover |
| `--color-text-secondary` | Item default |
| `--color-text-muted` | Item disabled, chevron |
| `--color-border` | Borda direita, separadores |
| `--color-border-focus` | Focus ring |
| `--font-body` | Familia tipografica |
| `--text-sm` | Font size (14px) |
| `--font-regular` | Peso default (400) |
| `--font-semibold` | Peso ativo (600) |
| `--space-4` | Padding horizontal |
| `--space-3` | Gap icone→label |
| `--space-2` | Padding vertical, gap dot→label, separador margin, badge margin |
| `--transition-normal` | Transicao collapse (200ms) |

---

## O que esta fora deste spec

- **Navigation Canvas com search/filtro de items:** complexidade desnecessaria no contexto SISP. Se necessario, adicionar como extensao
- **Navigation Canvas com drag-and-drop (reordenar items):** funcionalidade de admin, nao do componente de navegacao
- **Navigation Canvas com favoritos/pinned items:** pode ser adicionado como extensao futura
- **Navigation Canvas com icons customizados (SVG/imagem):** manter Font Awesome Free como padrao
- **Bottom navigation (mobile):** padrao diferente, componente separado se necessario
- **Mega menu (flyout com conteudo rico):** padrão diferente, não é sidebar nav

---

## Criterios de aceite

- [ ] 2 modos no Figma: Expanded (240px) e Collapsed (64px)
- [ ] Items de 1o nivel com icone + label + badge + chevron (quando children)
- [ ] Sub-items (2o nivel) com indentacao e dot indicator quando ativo
- [ ] 3 canais para item ativo: borda esquerda + fundo + bold (resolve cor aten)
- [ ] Toggle collapse/expand com transicao suave
- [ ] Badge como instancia BC-04 Badge Neutral SM (Regra 11)
- [ ] Contraste verificado — 7.2:1 AAA para default, 5.0:1 AA para ativo
- [ ] ARIA documentado: `<nav>`, `aria-current`, `aria-expanded`, `aria-label`
- [ ] Navegacao por teclado completa (↑↓←→ Enter Escape Home End)
- [ ] Mobile: comportamento drawer (< 1024px) documentado
- [ ] Violacoes WCAG (cor aten · vis aten) resolvidas
- [ ] Violacoes Nielsen (H-1 · H-4 · H-6 · H-7 · H-8 aten) resolvidas
- [ ] Mapeamento de retrocompatibilidade documentado
- [ ] Revisado e aprovado por Giuliana
