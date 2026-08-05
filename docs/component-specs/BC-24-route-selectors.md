---
component-id: BC-24
component-name: Route Selectors
type: Base
status: approved
sprint: 4.1
approved-by: [Giuliana]
approved-date: [2026-07-16]
figma-node-id: [268:552]
---

# Component Spec — Route Selectors

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → BC-24 (cor aten · vis aten — rota ativa sem diferenciacao alem de cor)
> - `docs/analyses/nielsen-analysis.md` → BC-24 (H-1 aten · H-2 aten · H-4 aten · H-6 aten)
> - `docs/analyses/inventory.md` → BC-24
> - Relacao com BC-26 Tabs via `useRouteSelector` (inventario)

---

## O que e

Route Selector e o componente de navegacao por rotas Angular do DS SISP. Funciona como um seletor visual de seções que controla a rota ativa do router — ao clicar em um item, a URL muda e o conteudo da pagina atualiza. Na DV, route selectors aparecem em paineis com multiplas seções (consultas, cadastros, relatorios). Integra-se com BC-26 Tabs via prop `useRouteSelector` — visualmente identico a Tabs, mas em vez de alternar conteudo inline, navega entre rotas.

> **Regra 12 aplicada:** Route Selector reutiliza o padrao visual de BC-26 Tabs. No Figma, o Component Set usa instancias de Tab Item (129:238) como itens de rota — composicao atomica. A diferenca e comportamental (router navigation vs. inline content switching), nao visual.

---

## Audiencia de uso

- **Policial na DV:** usa route selectors para navegar entre seções de um modulo (ex: consultas por tipo, abas de cadastro, seções de relatorio)
- **Devs CiASC / terceiros:** usam route selectors para criar navegacao por seções com rotas Angular. Precisam vincular cada item a uma rota e saber qual esta ativa
- **POs (Sommer/Holiwod):** precisam que a navegacao entre seções seja clara e consistente

---

## Props / API

| Prop | Tipo | Obrigatorio | Padrao | Descricao |
|---|---|---|---|---|
| `routes` | `RouteItem[]` | sim | — | Lista de rotas disponiveis |
| `activeRoute` | `string` | nao | rota atual | Rota ativa (path). Se nao definida, inferida do router Angular |
| `style` | `'underline' \| 'contained'` | nao | `'underline'` | Estilo visual — herda variantes de BC-26 Tabs |
| `justified` | `boolean` | nao | `false` | Items ocupam largura igual (distribuidos) |

**RouteItem:**
```typescript
interface RouteItem {
  label: string;       // Texto exibido — em portugues
  route: string;       // Path Angular (ex: '/consultas/pessoa')
  icon?: string;       // Classe Font Awesome opcional
  disabled?: boolean;  // Rota desabilitada (nao navegavel)
  badge?: string;      // Texto de badge opcional (ex: contagem de resultados)
}
```

**Convencao Angular:**
```html
<sisp-lib-route-selector [sispLibRouteSelectorConfig]="config"></sisp-lib-route-selector>
```

**Exemplo de uso:**
```typescript
config: SispLibRouteSelectorConfig = {
  style: 'underline',
  routes: [
    { label: 'Por Pessoa', route: '/consultas/pessoa', icon: 'fa-solid fa-user' },
    { label: 'Por Veiculo', route: '/consultas/veiculo', icon: 'fa-solid fa-car' },
    { label: 'Por Registro', route: '/consultas/registro', icon: 'fa-solid fa-file' },
    { label: 'Textual', route: '/consultas/textual', icon: 'fa-solid fa-magnifying-glass' }
  ]
};
```

---

## Anatomia do componente

### Underline style
```
┌─────────────────────────────────────────────────────────────┐
│   Por Pessoa      Por Veiculo    Por Registro     Textual   │
│   ═══════════                                               │  ← underline na rota ativa
└─────────────────────────────────────────────────────────────┘
```

### Contained style
```
┌─────────────────────────────────────────────────────────────┐
│  ┌─────────────┐                                            │
│  │ Por Pessoa  │  Por Veiculo    Por Registro     Textual   │  ← rota ativa com fundo
│  └─────────────┘                                            │
└─────────────────────────────────────────────────────────────┘
```

- **Container:** barra horizontal com items de rota
- **Route Item:** instancia visual de Tab Item (BC-26) — label + icone opcional + badge opcional
- **Indicador ativo:** underline (2px) ou fundo contido — herda padrao de Tabs

---

## Estados e variantes

### Estilos (herdam de BC-26 Tabs)

| Estilo | Descricao | Indicador ativo |
|---|---|---|
| **Underline** | Items com underline no ativo. Padrao. | Borda inferior 2px `--color-primary` + texto bold |
| **Contained** | Item ativo com fundo arredondado | Fundo `--color-primary-muted` + texto `--color-primary` + bold |

### Estados dos route items

| Estado | Descricao visual | Tokens |
|---|---|---|
| **Default** | Texto neutro | `text: --color-text-secondary` · `font-weight: --font-regular` |
| **Active** | Rota ativa — 3 canais (Regra WCAG) | `text: --color-primary` · `font-weight: --font-semibold` · underline ou fundo |
| **Hover** | Texto escurece | `text: --color-text-primary` |
| **Focus** | Focus ring | `outline: 2px solid var(--color-border-focus)` · `outline-offset: 2px` |
| **Disabled** | Nao navegavel | `text: --color-text-muted` · `opacity: 0.5` · `cursor: not-allowed` |

### Cores

| Elemento | Token | Valor |
|---|---|---|
| Container fundo | `--color-surface` | #FFFFFF |
| Container borda inferior | `--color-border` | #E5E7EB |
| Item default | `--color-text-secondary` | #4B5563 |
| Item ativo | `--color-primary` | #C4000B |
| Item hover | `--color-text-primary` | #08060F |
| Underline ativa | `--color-primary` | #C4000B |
| Contained fundo ativo | `--color-primary-muted` | #FEF2F2 |
| Focus ring | `--color-border-focus` | #C4000B |
| Disabled | `--color-text-muted` | #9CA3AF |

### Verificacao de contraste (WCAG AA)

| Elemento | Texto | Fundo | Ratio | Resultado |
|---|---|---|---|---|
| Item default | #4B5563 | #FFFFFF | ~7.2:1 | ✅ AAA |
| Item ativo (underline) | #C4000B | #FFFFFF | ~5.2:1 | ✅ AA |
| Item ativo (contained) | #C4000B | #FEF2F2 | ~5.0:1 | ✅ AA |
| Underline vs fundo | #C4000B | #FFFFFF | ~5.2:1 | ✅ AA (grafico 3:1) |

### Dimensoes

| Propriedade | Valor | Token |
|---|---|---|
| Item height | 40px | — |
| Item padding horizontal | 16px | `--space-4` |
| Item padding vertical | 8px | `--space-2` |
| Gap entre items | 0 (adjacentes) | — |
| Font size | 14px | `--text-sm` |
| Font weight default | 400 | `--font-regular` |
| Font weight ativo | 600 | `--font-semibold` |
| Underline height | 2px | — |
| Container border bottom | 1px | — |
| Contained border-radius | 6px | `--radius-md` |
| Gap icone → label | 8px | `--space-2` |
| Badge margin left | 8px | `--space-2` |

---

## Violacoes a resolver — WCAG AA

| Dimensao | Violacao atual | Solucao neste spec |
|---|---|---|
| Uso de cor (cor aten) | Rota ativa sem diferenciacao alem de cor | 3 canais para rota ativa: (1) cor primaria, (2) underline/fundo contido, (3) font-weight bold. Padrao identico ao BC-26 Tabs (ja resolvido no Sprint 4) |
| Visual (vis aten) | Item ativo sem indicador robusto | Underline 2px ou fundo contido como indicador persistente. Focus ring 2px para navegacao por teclado. Hover diferenciado |

---

## Violacoes a resolver — Heuristicas Nielsen

| Heuristica | Violacao atual | Solucao neste spec |
|---|---|---|
| H-1 Visibilidade (aten) | Rota ativa nao claramente visivel | 3 canais visuais (cor + underline/fundo + bold). `aria-current="page"` para screen readers. Indicador persistente — nao depende de hover |
| H-2 Mundo real (aten) | Labels nao auditados | Labels em portugues, descritivos da secao ("Por Pessoa", "Por Veiculo"). Icones opcionais reforçam o significado (fa-user, fa-car). Vocabulario alinhado ao dominio da DV |
| H-4 Consistencia (aten) | Sem padrao documentado | Herda visual de BC-26 Tabs — mesmo padrao usado em todo o DS. Diferenca e funcional (rota vs. conteudo inline), nao visual. useRouteSelector documentado |
| H-6 Reconhecimento (aten) | Usuario precisa memorizar qual rota esta ativa | Indicador visual persistente (3 canais). Icones opcionais reforçam reconhecimento. Badge com contagem opcional ("Pessoa (3)") ajuda a lembrar onde ha resultados |

---

## Regras de acessibilidade

- [ ] Container com `role="navigation"` e `aria-label="Seletor de rotas"` (ou descricao contextual)
- [ ] Cada item como `<a>` (link) — nao `<button>` (semantica de navegacao)
- [ ] Rota ativa com `aria-current="page"`
- [ ] Items desabilitados com `aria-disabled="true"` e `tabindex="-1"`
- [ ] **Navegacao por teclado:**
  - `Tab` move entre items de rota
  - `Enter` / `Space` navega para a rota
  - `←` / `→` move entre items (quando dentro do grupo)
  - Focus ring visivel: `2px solid var(--color-border-focus)`
- [ ] Contraste minimo 4.5:1 em todos os estados interativos — verificado
- [ ] Nao depende apenas de cor (3 canais: cor + underline/fundo + bold)
- [ ] Labels em portugues

---

## Comportamentos esperados

- Quando usuario clica em um route item → URL muda via Angular Router, rota ativa atualiza visualmente
- Quando URL muda externamente (ex: browser back/forward) → rota ativa atualiza automaticamente
- Quando `activeRoute` e fornecido → sobreescreve inferencia do router (modo controlado)
- Quando item tem `disabled = true` → item visivel com estilo desabilitado, nao responde a clique, nao navegavel por teclado
- Quando item tem `icon` → icone exibido a esquerda do label com gap --space-2
- Quando item tem `badge` → badge exibido a direita do label (instancia de BC-04 Badge Neutral SM)
- Quando `justified = true` → items distribuem largura igualmente
- Quando `style = 'underline'` → borda inferior 2px na rota ativa, borda sutil 1px no container
- Quando `style = 'contained'` → fundo arredondado (--radius-md) na rota ativa
- Quando muitas rotas (> 5) e viewport estreita → scroll horizontal com fade indicator nas bordas

---

## Composicao com outros componentes

| Componente | Relacao | Composicao no Figma (Regra 11/12) |
|---|---|---|
| **BC-26 Tabs / Tab Item** | **Route items sao visualmente Tab Items — instancias de BC-26 Tab Item** | **Instancia direta** — Regra 12 aplicada. Padrao funcional equivalente (selecao horizontal entre N opcoes) |
| BC-04 Badges | Badge de contagem opcional nos route items | Instancia de Badge Neutral SM quando `badge` fornecido |
| BC-15 Icons | Icones opcionais nos route items | Font Awesome — `aria-hidden="true"` |

> **Regra 12 — auditoria:** "este elemento ja existe como componente?" Sim — Route Selector e visualmente identico a Tabs. A diferenca e comportamental: Tabs alterna conteudo inline (`ngbNav`), Route Selector navega rotas Angular (`router.navigate`). No Figma, reutilizar Tab Item como base visual. No Angular, `useRouteSelector: true` na config de Tabs ja existe (inventario).

---

## Mapeamento de retrocompatibilidade

| Prop atual | Mapeamento novo | Nota |
|---|---|---|
| `routes` | `routes: RouteItem[]` | Extendido com `icon`, `disabled`, `badge` |
| `activeRoute` | `activeRoute` | Mantido |
| — | `style` (novo) | Herda variantes visuais de Tabs (underline/contained) |
| — | `justified` (novo) | Distribuicao igual de largura |

**Nota importante:** no Angular, Route Selector ja funciona via `useRouteSelector: true` na config de BC-26 Tabs. Este spec documenta o componente como entidade separada para clareza no Figma e na documentacao, mas a implementacao Angular pode continuar como modo do Tabs.

---

## Comportamento responsivo

> Regra 13 — Responsividade obrigatória. Breakpoints: Mobile (<768px), Tablet (768–1023px), Desktop (≥1024px).

| Precisa de variante `Layout=Mobile`? | **Sim** — opções horizontais não cabem |
|---|---|
| **Desktop** | Horizontal (368-376px), todas as opções visíveis |
| **Mobile** | Horizontal com scroll (280px, `clipsContent`). Scroll para opções que excedem |
| **Tablet** | Segue Desktop |

**Variantes no Figma:** 4 (2 estilos × 2 layouts)

---

## Casos excepcionais / bordas

- **1 rota:** renderiza normalmente — valido para cenarios com rota unica visivel
- **0 rotas:** componente nao renderiza
- **Rota nao encontrada:** nenhum item ativo — sem underline/fundo em nenhum item
- **Rotas com parametros (query params):** matching por path base, ignorando query params
- **Mobile (< 640px):** scroll horizontal com indicador de fade. Touch swipe suportado. Touch targets >= 44px (garantido pelo item height 40px + padding)
- **Muitas rotas (> 5):** scroll horizontal. Considerar agrupar em Dropdown para > 7 rotas
- **Label longo:** trunca com ellipsis. `title` com texto completo
- **Transicao de rota com loading:** nao e responsabilidade do Route Selector — usar BC-16 Loader no conteudo da rota

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-primary` | Texto e underline da rota ativa |
| `--color-primary-muted` | Fundo da rota ativa (contained) |
| `--color-text-secondary` | Texto item default |
| `--color-text-primary` | Texto item hover |
| `--color-text-muted` | Texto item disabled |
| `--color-surface` | Fundo container |
| `--color-border` | Borda inferior container |
| `--color-border-focus` | Focus ring |
| `--radius-md` | Border radius (contained) |
| `--font-body` | Familia tipografica |
| `--text-sm` | Font size (14px) |
| `--font-regular` | Peso default (400) |
| `--font-semibold` | Peso ativo (600) |
| `--space-4` | Padding horizontal item |
| `--space-2` | Padding vertical, gap icone→label, badge margin |

---

## O que esta fora deste spec

- **Route Selector vertical:** usar BC-20 Navigation Canvas para navegacao vertical
- **Route Selector com sub-rotas (nested):** usar Navigation Canvas para hierarquias
- **Route Selector com search/filtro:** usar BC-10 Dropdown para agrupamento de opcoes
- **Breadcrumbs:** componente diferente — mostra caminho hierarquico, nao selecao de seção
- **Route guards / permissoes:** logica de negocio, nao do componente visual

---

## Criterios de aceite

- [ ] 2 estilos (Underline, Contained) no Figma — herdam visual de BC-26 Tabs
- [ ] Route items como instancias de Tab Item (Regra 11/12)
- [ ] 3 canais para rota ativa: cor + underline/fundo + bold
- [ ] Contraste verificado — 5.2:1 AA para item ativo, 7.2:1 AAA para default
- [ ] ARIA documentado: `role="navigation"`, `aria-current="page"`, `aria-disabled`
- [ ] Navegacao por teclado documentada (Tab, Enter, ←/→)
- [ ] Badge de contagem com instancia de BC-04 Badge
- [ ] Violacoes WCAG (cor aten · vis aten) resolvidas — 3 canais visuais
- [ ] Violacoes Nielsen (H-1 · H-2 · H-4 · H-6 aten) resolvidas
- [ ] Relacao com BC-26 Tabs (useRouteSelector) documentada
- [ ] Mapeamento de retrocompatibilidade documentado
- [ ] Revisado e aprovado por Giuliana
