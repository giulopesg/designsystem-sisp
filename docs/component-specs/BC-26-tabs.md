---
component-id: BC-26
component-name: Tabs
type: Base
status: approved
sprint: 4
approved-by: [Giuliana]
approved-date: [2026-07-14]
figma-node-id: [129:238, 135:222]
---

# Component Spec — Tabs

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → BC-26 (cor **crit** · vis aten)
> - `docs/analyses/nielsen-analysis.md` → BC-26 (H-1 **crit** · H-4 aten · H-8 aten)
> - `docs/analyses/inventory.md` → BC-26

---

## O que é

Tabs é o componente de navegação por abas do DS SISP. Permite alternar entre painéis de conteúdo relacionados sem navegar para outra página. Na DV, tabs organizam seções de um BO (Dados Gerais, Pessoas Envolvidas, Objetos, Anexos), filtros de consulta, e agrupamentos de informação. Atualmente 100% Bootstrap, com aba ativa diferenciada apenas por cor verde — sem indicador posicional, sem ARIA documentado.

---

## Audiência de uso

- **Policial na DV:** navega entre seções de um BO, consulta dados agrupados por abas (dados pessoais, antecedentes, ocorrências)
- **Devs CiASC / terceiros:** usam tabs para organizar formulários longos, separar contextos de informação dentro da mesma página
- **POs (Sommer/Holiwod):** precisam de navegação clara entre seções sem perder o contexto da tela atual

---

## Props / API

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `tabs` | `TabItem[]` | sim | — | Array de abas. Cada item: `{ label: string, content?: TemplateRef, disabled?: boolean, icon?: string }` |
| `activeIndex` | `number` | não | `0` | Índice da aba ativa inicialmente |
| `justified` | `boolean` | não | `false` | Quando `true`, abas esticam para preencher a largura do container |
| `style` | `'underline' \| 'contained'` | não | `'underline'` | Estilo visual — underline (padrão, linha inferior) ou contained (aba com fundo) |
| `onTabChange` | `Function` | não | — | Callback ao trocar de aba. Recebe `{ index: number, label: string }` |

**TabItem:**
```typescript
interface TabItem {
  label: string;          // Texto da aba — curto, 1-3 palavras
  content?: TemplateRef;  // Template Angular do painel
  disabled?: boolean;     // Aba desabilitada (visível mas não clicável)
  icon?: string;          // Classe Font Awesome opcional (ex: 'fa-solid fa-user')
}
```

**Convenção Angular:**
```html
<sisp-lib-tabs [sispLibTabsConfig]="config"></sisp-lib-tabs>
```

**Exemplo de uso:**
```typescript
config: SispLibTabsConfig = {
  tabs: [
    { label: 'Dados Gerais', icon: 'fa-solid fa-file-lines' },
    { label: 'Pessoas Envolvidas', icon: 'fa-solid fa-users' },
    { label: 'Objetos' },
    { label: 'Anexos', disabled: true }
  ],
  style: 'underline',
  justified: false
};
```

---

## Anatomia do componente

```
┌─────────────────────────────────────────────────┐
│  [icon] Tab 1    [icon] Tab 2    Tab 3    Tab 4 │
│  ═══════════                                    │  ← indicador ativo (underline 3px)
├─────────────────────────────────────────────────┤
│  Conteúdo do painel ativo                       │
│                                                 │
└─────────────────────────────────────────────────┘
```

- **Tab bar:** container horizontal com as abas. `role="tablist"`
- **Tab item:** cada aba. `role="tab"`, `aria-selected="true|false"`
- **Indicador ativo:** underline 3px na cor primária (underline style) ou fundo preenchido (contained style)
- **Ícone:** opcional, à esquerda do label, mesmo tamanho do texto
- **Painel:** conteúdo da aba ativa. `role="tabpanel"`, `aria-labelledby` vinculado ao tab
- **Separador:** linha `1px solid --color-border-base` abaixo da tab bar

---

## Estados e variantes

### Estados por aba

| Estado | Descrição visual | Tokens |
|---|---|---|
| **Default (inativo)** | Texto cinza, sem indicador | `text: --color-text-secondary` |
| **Hover** | Fundo sutil, texto escurece | `bg: --color-bg-muted` · `text: --color-text-primary` |
| **Active** | Texto primário bold + indicador underline 3px | `text: --color-primary-base` · `border-bottom: 3px solid --color-primary-base` · `font-weight: --font-semibold` |
| **Focus** | Ring de foco visível | `outline: 2px solid --color-border-focus` · `outline-offset: -2px` |
| **Disabled** | Texto opaco, cursor not-allowed | `text: --color-text-muted` · `opacity: 0.5` · `cursor: not-allowed` |

### Estilos visuais

#### Underline (padrão)

| Elemento | Token |
|---|---|
| Tab bar border bottom | `1px solid --color-border-base` |
| Active indicator | `3px solid --color-primary-base` posicionado sobre o border do tab bar |
| Tab padding | `--space-3` (12px) horizontal · `--space-2` (8px) vertical |
| Gap entre tabs | `--space-1` (4px) |

#### Contained (alternativo)

| Elemento | Token |
|---|---|
| Active tab background | `--color-surface-bg` (branco) com border top/left/right `1px solid --color-border-base` e border-bottom transparente (conecta ao painel) |
| Inactive tab | Sem fundo, sem borda |
| Tab bar background | `--color-bg-muted` |
| Border radius (tab) | `--radius-md` nos cantos superiores |

### Verificação de contraste (WCAG AA)

| Estado | Texto | Fundo | Ratio | Resultado |
|---|---|---|---|---|
| Default (inativo) | #6B7280 | #FFFFFF | ~4.6:1 | ✅ AA |
| Active | #C4000B | #FFFFFF | ~5.2:1 | ✅ AA |
| Hover | #111827 | #F3F4F6 | ~15.4:1 | ✅ AAA |
| Disabled | #6B7280 (50%) | #FFFFFF | — | Decorativo (não interativo) |

### Dimensões

| Propriedade | Valor | Token |
|---|---|---|
| Tab height | 44px (mínimo touch target) | — |
| Padding horizontal (tab) | 12px | `--space-3` |
| Padding vertical (tab) | 8px | `--space-2` |
| Gap entre ícone e label | 8px | `--space-2` |
| Gap entre tabs | 4px | `--space-1` |
| Active indicator height | 3px | — |
| Font size | 14px | `--text-sm` |
| Font weight (inativo) | 400 | `--font-regular` |
| Font weight (ativo) | 600 | `--font-semibold` |
| Ícone size | 14px | — |

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução neste spec |
|---|---|---|
| Uso de cor (cor **crit**) | Aba ativa diferenciada apenas por cor verde — sem indicador de posição adicional | Aba ativa usa **3 canais**: underline 3px primário + cor do texto primário + font-weight semibold. Indicador posicional (underline) é independente de cor |
| Visual (vis aten) | Visual não refinado | Dois estilos (underline/contained), dimensões documentadas, espaçamento por tokens |
| Contraste | ok | Verificado: Default 4.6:1 AA, Active 5.2:1 AA, Hover 15.4:1 AAA |
| Tipografia | ok | Font size 14px, hierarquia regular/semibold entre inativo/ativo |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução neste spec |
|---|---|---|
| H-1 Visibilidade (CRIT) | Aba ativa ambígua — apenas cor verde diferencia | Indicador underline 3px persistente + texto bold + cor primária. 3 canais visuais simultâneos. `aria-selected` para screen readers |
| H-4 Consistência (aten) | Sem padrão documentado de quando usar tabs vs. outras navegações | Guia de uso: tabs para conteúdo no mesmo nível hierárquico, sem dependência entre painéis. Alinhado ao padrão WAI-ARIA Tabs Pattern |
| H-8 Estética (aten) | Visual Bootstrap genérico | Dois estilos refinados (underline/contained), espaçamento por tokens, transição suave no indicador |

---

## Regras de acessibilidade

- [ ] Tab bar com `role="tablist"`
- [ ] Cada aba com `role="tab"` e `aria-selected="true|false"`
- [ ] Aba ativa: `aria-selected="true"` · `tabindex="0"`
- [ ] Abas inativas: `aria-selected="false"` · `tabindex="-1"`
- [ ] Cada painel com `role="tabpanel"` e `aria-labelledby` referenciando o `id` da aba
- [ ] **Navegação por teclado:**
  - `→` / `←` movem foco entre abas (cicla no final)
  - `Home` vai para a primeira aba
  - `End` vai para a última aba
  - `Enter` / `Space` ativa a aba focada
  - Abas desabilitadas são puladas na navegação por seta
- [ ] Focus ring visível: `2px solid var(--color-border-focus)`
- [ ] Indicador ativo não depende apenas de cor (underline 3px + font-weight)
- [ ] Contraste mínimo 4.5:1 para texto de abas — verificado
- [ ] Ícone com `aria-hidden="true"` (decorativo — label textual é o canal primário)

---

## Comportamentos esperados

- Quando usuário clica em aba inativa → aba fica ativa, indicador underline move com `transition: left 200ms ease, width 200ms ease`, painel anterior é ocultado e novo painel é exibido
- Quando `justified = true` → abas esticam igualmente para ocupar 100% da largura do container (`flex: 1`)
- Quando aba tem `disabled = true` → aba visível com estilo desabilitado, não responde a clique, pulada na navegação por teclado
- Quando aba tem `icon` → ícone renderiza à esquerda do label com gap de `--space-2` (8px)
- Quando `onTabChange` definido → callback disparado após a troca, recebendo `{ index, label }`
- Quando há mais abas do que cabem na largura → scroll horizontal com fade indicators nas bordas (comportamento overflow)
- Quando `style = 'contained'` → aba ativa tem fundo branco conectado ao painel, abas inativas sem fundo

---

## Composição com outros componentes

| Componente | Relação |
|---|---|
| BC-06 Cards | Tabs dentro de cards para organizar conteúdo em seções |
| BC-13 Forms | Formulários longos divididos em abas (ex: dados pessoais, endereço, documentos) |
| BC-19 Modals | Tabs dentro de modais para organizar informação em contexto overlay |
| BC-24 Route Selectors | `useRouteSelector` integra tabs com navegação por rota — tabs controlam qual rota está ativa |
| SC-02/03/04 Consultas | Tabs para alternar entre tipos de consulta ou resultados |

---

## Mapeamento de retrocompatibilidade

| Prop atual | Mapeamento novo | Nota |
|---|---|---|
| `tabs: [{label, content}]` | `tabs: TabItem[]` | Extendido com `disabled` e `icon` |
| `useRouteSelector` | Removido da API pública | Integração com BC-24 Route Selectors será feita via composição, não via prop interna |
| `justified` | `justified` | Mantido — mesmo comportamento |
| — | `style` (novo) | Novo prop para escolher entre underline e contained |
| — | `activeIndex` (novo) | Controle externo do tab ativo |
| — | `onTabChange` (novo) | Callback de troca de aba |

---

## Comportamento responsivo

> Regra 13 — Responsividade obrigatória. Breakpoints: Mobile (<768px), Tablet (768–1023px), Desktop (≥1024px).

| Precisa de variante `Layout=Mobile`? | **Sim** — muitas abas não cabem em viewport mobile |
|---|---|
| **Desktop** | Horizontal (349px+), todas as abas visíveis. Tab Items com espaço para labels completos |
| **Mobile** | Horizontal com scroll (280px, `clipsContent: true`). Abas que excedem a largura ficam acessíveis via scroll horizontal |
| **Tablet** | Segue Desktop — largura suficiente para todas as abas |

**O que muda entre Desktop e Mobile:**
- Largura: 349px+ → 280px (abas que não cabem fazem scroll)
- Comportamento: estático → scroll horizontal (`overflow-x: auto`)
- Estrutura: mantida (Tab Items são instâncias BC-26 Tab Item)

**Variantes no Figma:** 4 variantes (2 estilos × 2 layouts)
- `Style=Underline, Layout=Desktop` / `Style=Underline, Layout=Mobile`
- `Style=Contained, Layout=Desktop` / `Style=Contained, Layout=Mobile`

---

## Casos excepcionais / bordas

- **Muitas abas (overflow):** scroll horizontal com botões de seta ou fade nas bordas. Indicador ativo acompanha o scroll
- **Label longo (> 20 caracteres):** trunca com `text-overflow: ellipsis` + `title` com texto completo. Recomendação: máximo 3 palavras
- **Apenas 1 aba:** tabs não renderiza tab bar — exibe diretamente o conteúdo (sem sentido navegar com 1 opção)
- **Mobile (< 640px):** abas em scroll horizontal. Touch target mínimo 44px de altura mantido. `justified` pode ser desativado automaticamente se abas truncarem
- **Conteúdo de painel vazio:** painel renderiza com altura mínima de `--space-8` (32px) para manter o layout estável
- **Troca rápida (spam de cliques):** apenas a última aba clicada é ativada — sem race condition

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-primary-base` | Texto ativo, indicador underline |
| `--color-text-primary` | Texto hover |
| `--color-text-secondary` | Texto inativo |
| `--color-text-muted` | Texto desabilitado |
| `--color-bg-muted` | Fundo hover, fundo tab bar (contained) |
| `--color-surface-bg` | Fundo aba ativa (contained) |
| `--color-border-base` | Separador abaixo da tab bar, bordas (contained) |
| `--color-border-focus` | Ring de foco |
| `--radius-md` | Border radius cantos superiores (contained) |
| `--font-body` | Família tipográfica |
| `--text-sm` | Font size (14px) |
| `--font-regular` | Peso inativo (400) |
| `--font-semibold` | Peso ativo (600) |
| `--space-1` | Gap entre tabs (4px) |
| `--space-2` | Padding vertical, gap ícone→label (8px) |
| `--space-3` | Padding horizontal (12px) |

---

## O que está fora deste spec

- **Tabs verticais:** não identificado na DV. Se surgir necessidade, especificar como variante de layout
- **Tabs com badge/contador:** composição com BC-04 Badge — pode ser adicionado como slot futuro
- **Tabs com close (×):** padrão de IDE, não de aplicação de governo. Fora do escopo
- **Tabs dinâmicas (adicionar/remover):** comportamento de produto, não do componente base
- **Animação de transição entre painéis:** slide ou fade entre conteúdos — pode ser adicionado se demanda surgir

---

## Critérios de aceite

- [ ] 2 estilos visuais (Underline, Contained) no Figma
- [ ] 5 estados de aba (Default, Hover, Active, Focus, Disabled) documentados
- [ ] Indicador ativo com 3 canais (underline + cor + bold) — resolve WCAG cor crit
- [ ] Variante `justified` documentada
- [ ] ARIA documentado: `role="tablist"`, `role="tab"`, `aria-selected`, `role="tabpanel"`
- [ ] Navegação por teclado documentada (→ ← Home End Enter Space)
- [ ] Contraste verificado para todos os estados — mínimo 4.5:1 AA
- [ ] Violação WCAG AA (cor **crit** · vis aten) resolvida
- [ ] Violações Nielsen (H-1 **crit** · H-4 aten · H-8 aten) resolvidas
- [ ] Mapeamento de retrocompatibilidade documentado
- [ ] Revisado e aprovado por Giuliana
