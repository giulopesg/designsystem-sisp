---
component-id: BC-25
component-name: Tables
type: Base
status: approved
sprint: 4
approved-by: [Giuliana]
approved-date: [2026-07-14]
figma-node-id: [141:249]
---

# Component Spec — Tables

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → BC-25 (cor aten · vis aten · tip aten)
> - `docs/analyses/nielsen-analysis.md` → BC-25 (H-1 aten · H-4 **crit** · H-6 aten · H-7 **crit** · H-8 aten)
> - `docs/analyses/inventory.md` → BC-25

---

## O que é

Table é o componente de exibição de dados tabulares do DS SISP. Exibe listas estruturadas com colunas, ordenação, seleção e paginação. Na DV, tabelas são o padrão para listagens de BOs, resultados de consultas policiais, pessoas envolvidas, objetos apreendidos — componente de uso intensivo. Atualmente funcional, mas com ordenação visual inconsistente, seleção apenas por cor, ARIA de tabela não documentado.

---

## Audiência de uso

- **Policial na DV:** consulta listagens de BOs, pessoas, veículos. Precisa ordenar, selecionar registros, paginar resultados extensos
- **Devs CiASC / terceiros:** usam tabelas para qualquer listagem CRUD — é o componente mais usado depois de formulários
- **POs (Sommer/Holiwod):** precisam que resultados sejam claros, ordenáveis e que o policial encontre rapidamente o registro que procura

---

## Props / API

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `columns` | `TableColumn[]` | sim | — | Definição das colunas — label, key, sortable, width, align |
| `data` | `any[]` | sim | — | Array de objetos com os dados. Cada objeto mapeia pelas keys das colunas |
| `selectable` | `boolean` | não | `false` | Habilita seleção de linhas via checkbox |
| `onSelectionChange` | `Function` | não | — | Callback ao selecionar/deselecionar linhas. Recebe `selectedRows[]` |
| `sortable` | `boolean` | não | `false` | Habilita ordenação por colunas (pode ser overridden por coluna) |
| `defaultSort` | `{ key: string, direction: 'asc' \| 'desc' }` | não | — | Ordenação inicial |
| `onSortChange` | `Function` | não | — | Callback ao ordenar. Recebe `{ key, direction }` |
| `pagination` | `PaginationConfig` | não | — | Configuração de paginação (pageSize, totalItems, currentPage) |
| `emptyMessage` | `string` | não | `'Nenhum registro encontrado'` | Mensagem quando `data` é vazio |
| `loading` | `boolean` | não | `false` | Exibe estado de carregamento (skeleton rows) |
| `striped` | `boolean` | não | `true` | Linhas alternadas com fundo sutil |
| `compact` | `boolean` | não | `false` | Reduz padding para tabelas densas |

**TableColumn:**
```typescript
interface TableColumn {
  key: string;           // Chave do dado no objeto
  label: string;         // Texto do cabeçalho
  sortable?: boolean;    // Coluna é ordenável (default: herda do prop global)
  width?: string;        // Largura da coluna (ex: '200px', '30%')
  align?: 'left' | 'center' | 'right';  // Alinhamento (default: 'left')
}
```

**Convenção Angular:**
```html
<sisp-lib-table [sispLibTableConfig]="config"></sisp-lib-table>
```

**Exemplo de uso:**
```typescript
config: SispLibTableConfig = {
  columns: [
    { key: 'protocolo', label: 'Protocolo', sortable: true, width: '150px' },
    { key: 'tipo', label: 'Tipo de Ocorrência', sortable: true },
    { key: 'data', label: 'Data', sortable: true, width: '120px' },
    { key: 'status', label: 'Status', sortable: false, width: '120px' }
  ],
  data: boletins,
  selectable: true,
  sortable: true,
  defaultSort: { key: 'data', direction: 'desc' },
  pagination: { pageSize: 10, totalItems: 234 },
  striped: true
};
```

---

## Anatomia da tabela

```
┌─────────────────────────────────────────────────────────┐
│  [□]  Protocolo ▼    Tipo          Data ▲    Status     │  ← Header row
├─────────────────────────────────────────────────────────┤
│  [□]  2026.001234    Furto         13/07     Finalizado │  ← Row (striped bg)
│  [□]  2026.001233    Roubo         12/07     Em andamento│  ← Row (white bg)
│  [□]  2026.001232    Lesão         11/07     Cancelado  │  ← Row (striped bg)
├─────────────────────────────────────────────────────────┤
│  ← 1  2  3  ...  24 →          10 por página  Total: 234│  ← Pagination
└─────────────────────────────────────────────────────────┘
```

- **Header row:** fundo `--color-bg-muted`, texto bold, ícone de sort (▲/▼ ou ↕)
- **Body rows:** linhas alternadas (striped) ou brancas. Hover com fundo sutil
- **Checkbox:** coluna fixa à esquerda quando `selectable = true`. Header tem checkbox "select all"
- **Sort indicator:** ícone `fa-solid fa-sort` (inativo), `fa-solid fa-sort-up` (ASC), `fa-solid fa-sort-down` (DESC)
- **Pagination:** barra inferior com navegação de páginas, seletor de itens por página, total

---

## Estados e variantes

### Estados da tabela

| Estado | Descrição visual | Tokens |
|---|---|---|
| **Default** | Tabela com dados, header + body + pagination | — |
| **Empty** | Header visível + corpo com mensagem centralizada | `text: --color-text-muted` |
| **Loading** | Header visível + skeleton rows animadas | `bg: --color-bg-muted` com animação pulse |
| **Sorted** | Coluna ativa com ícone ▲ ou ▼ em destaque | Ícone ativo: `--color-primary-base` |

### Estados por linha

| Estado | Descrição visual | Tokens |
|---|---|---|
| **Default** | Fundo branco (ou striped sutil) | `bg: --color-surface-bg` ou `bg: --color-surface-bg-subtle` |
| **Hover** | Fundo sutil | `bg: --color-bg-muted` |
| **Selected** | Fundo sutil + checkbox checked + borda esquerda 3px primária | `bg: --color-primary-muted` · `border-left: 3px solid --color-primary-base` |
| **Disabled** | Texto muted, não interativa | `text: --color-text-muted` · `opacity: 0.5` |

### Verificação de contraste (WCAG AA)

| Elemento | Texto | Fundo | Ratio | Resultado |
|---|---|---|---|---|
| Header | #111827 | #F3F4F6 | ~15.4:1 | ✅ AAA |
| Body row | #111827 | #FFFFFF | ~18.1:1 | ✅ AAA |
| Body row (striped) | #111827 | #F9FAFB | ~17.2:1 | ✅ AAA |
| Sort icon (active) | #C4000B | #F3F4F6 | ~5.0:1 | ✅ AA |
| Empty message | #6B7280 | #FFFFFF | ~4.6:1 | ✅ AA |

### Dimensões

| Propriedade | Valor (default) | Valor (compact) | Token |
|---|---|---|---|
| Header height | 48px | 36px | — |
| Row height | 48px | 36px | — |
| Cell padding horizontal | 16px | 12px | `--space-4` / `--space-3` |
| Cell padding vertical | 12px | 8px | `--space-3` / `--space-2` |
| Font size (header) | 12px | 12px | `--text-xs` |
| Font weight (header) | 600 | 600 | `--font-semibold` |
| Font size (body) | 14px | 13px | `--text-sm` |
| Font weight (body) | 400 | 400 | `--font-regular` |
| Checkbox size | 16px | 16px | — |
| Sort icon size | 12px | 12px | — |
| Border | 1px solid | 1px solid | `--color-border-base` |

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução neste spec |
|---|---|---|
| Uso de cor (cor aten) | Linha selecionada sem indicador além de cor | Seleção com 3 canais: **checkbox checked** + **fundo sutil** + **borda esquerda 3px primária**. Checkbox é o canal primário — não depende de cor |
| Visual (vis aten) | Estados visuais não clarificados | 4 estados de tabela (Default, Empty, Loading, Sorted) + 4 estados de linha (Default, Hover, Selected, Disabled) documentados |
| Tipografia (tip aten) | Cabeçalhos sem scope | `<th scope="col">` obrigatório em cada header. Font hierarchy: header 12px semibold UPPERCASE vs body 14px regular |
| — | Cabeçalhos sem `scope` | Cada `<th>` com `scope="col"`. Tabela com `<caption>` descrevendo o conteúdo |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução neste spec |
|---|---|---|
| H-1 Visibilidade (aten) | Estado de ordenação não claro | Ícone de sort muda de ↕ (inativo, cinza) para ▲/▼ (ativo, cor primária). Texto do header ativo fica na cor primária. `aria-sort` na coluna ordenada |
| H-4 Consistência (CRIT) | Ícone de ordenação ativo vs. inativo inconsistente | Padrão visual único: inativo = `fa-sort` (↕ cinza), ASC = `fa-sort-up` (▲ primário), DESC = `fa-sort-down` (▼ primário). Transição via clique no header: neutro → ASC → DESC → neutro |
| H-6 Reconhecimento (aten) | Significado dos ícones de sort não óbvio | Ícone ▲/▼ com tooltip "Ordenar crescente"/"Ordenar decrescente". `aria-sort="ascending|descending|none"` no header |
| H-7 Flexibilidade (CRIT) | Sem atalho de teclado para ordenação. Sem filtro rápido | Headers sortáveis focáveis via Tab e ativáveis via Enter/Space. Ciclo: none → asc → desc → none. Foco move entre headers com ← → |
| H-8 Estética (aten) | Visual genérico Bootstrap | Linhas striped com token sutil, header com fundo muted, border-radius na tabela, skeleton loading animado |

---

## Regras de acessibilidade

- [ ] Tabela com `<table>`, `<thead>`, `<tbody>` — semântica HTML nativa
- [ ] `<caption>` descrevendo o conteúdo da tabela (pode ser visually hidden)
- [ ] Cada `<th>` com `scope="col"`
- [ ] Coluna ordenada com `aria-sort="ascending|descending|none"`
- [ ] Header sortável focável via Tab com role interativo
- [ ] Enter/Space ativa ordenação no header focado
- [ ] Checkbox de seleção com `aria-label="Selecionar [identificador da linha]"`
- [ ] Checkbox "select all" com `aria-label="Selecionar todos"` e estado `aria-checked="mixed"` quando parcial
- [ ] Linha selecionada com `aria-selected="true"`
- [ ] Estado Empty com `role="status"` para anunciar "Nenhum registro encontrado"
- [ ] Estado Loading com `aria-busy="true"` na tabela e `aria-live="polite"` para anunciar quando dados carregarem
- [ ] Focus ring visível: `2px solid var(--color-border-focus)`
- [ ] Seleção não depende apenas de cor: checkbox + fundo + borda esquerda = 3 canais
- [ ] Contraste verificado para todos os elementos — mínimo 4.5:1 AA

---

## Comportamentos esperados

- Quando coluna sortável é clicada → ciclo: neutro → ASC (▲) → DESC (▼) → neutro (↕). Apenas uma coluna ativa por vez
- Quando `selectable = true` → checkbox aparece como primeira coluna. Header tem "select all"
- Quando checkbox de linha é clicado → linha fica com fundo `--color-primary-muted` + borda esquerda 3px. Callback `onSelectionChange` dispara
- Quando "select all" é clicado → seleciona todas as linhas da página. Se parcialmente selecionado, clique seleciona todos (nunca deseleciona)
- Quando `data` é vazio → header permanece visível, corpo exibe `emptyMessage` centralizado com ícone `fa-solid fa-inbox`
- Quando `loading = true` → header permanece, corpo exibe 5 skeleton rows com animação pulse
- Quando `striped = true` → linhas ímpares com fundo `--color-surface-bg-subtle`, pares com `--color-surface-bg`
- Quando `compact = true` → padding e altura de linha reduzidos (36px em vez de 48px)
- Quando `pagination` definido → barra de paginação no rodapé com navegação e total de registros
- Quando hover em linha → fundo muda para `--color-bg-muted`
- Quando tabela é mais larga que container → scroll horizontal com header fixo

---

## Composição com outros componentes

> **Regra 11 — Composição atômica:** todo elemento visual que já exista como componente no DS deve ser usado como **instância**, nunca recriado. Isso garante propagação automática de mudanças, consistência visual e manutenção centralizada.

| Componente | Relação | Composição no Figma |
|---|---|---|
| BC-13 Checkbox | Checkbox na coluna de seleção (`selectable = true`) | **Instância do Checkbox** (118:2247) — `Checked=Yes/No`, sem label (label vazio) |
| BC-04 Badges | Badges na coluna de status (ex: "Finalizado", "Pendente") — usar tamanho `sm` | **Instância do Badge** (124:2690) — tipo semântico por status |
| BC-05 Buttons | Botões de ação na última coluna (Editar, Excluir) — usar variant Tertiary ou Danger `sm` | **Instância do Button** (116:1862) |
| BC-06 Cards | Tabela dentro de card — card fornece título e collapse | Composição externa |
| BC-16 Loaders | Estado loading usa skeleton rows, não o componente Loader global | Skeleton próprio do Table |
| SC-02/03/04 Consultas | Tabelas de resultado de consultas policiais | Composição externa |

---

## Mapeamento de retrocompatibilidade

| Prop atual | Mapeamento novo | Nota |
|---|---|---|
| `columns: SortColumn[]` | `columns: TableColumn[]` | Tipo renomeado, interface extendida com `align` e `width` |
| `data: []` | `data: any[]` | Mantido |
| `pagination?: object` | `pagination?: PaginationConfig` | Tipagem formal |
| `selectable?: boolean` | `selectable?: boolean` | Mantido |
| — | `sortable` (novo) | Controle global de ordenação |
| — | `defaultSort` (novo) | Ordenação inicial |
| — | `emptyMessage` (novo) | Customização da mensagem vazia |
| — | `loading` (novo) | Estado de carregamento |
| — | `striped` (novo) | Linhas alternadas |
| — | `compact` (novo) | Modo compacto |

---

## Comportamento responsivo

> Regra 13 — Responsividade obrigatória. Breakpoints: Mobile (<768px), Tablet (768–1023px), Desktop (≥1024px).

| Precisa de variante `Layout=Mobile`? | **Sim** — colunas excedem largura do viewport mobile |
|---|---|
| **Desktop** | Tabela em largura total (572px+), todas as colunas visíveis. Cells com padding 12/16px (`space/3`/`space/4`) |
| **Mobile** | Tabela em 343px (375 - 2×16 page-padding), scroll horizontal (`overflow-x: auto`). Cells com padding 12/8px (`space/3`/`space/2`). `clipsContent: true` no Figma indica overflow |
| **Tablet** | Segue Desktop — largura suficiente para todas as colunas |

**O que muda entre Desktop e Mobile:**
- Largura: 572px+ → 343px (conteúdo que excede faz scroll horizontal)
- Cell padding horizontal: 16px → 8px
- Comportamento: colunas que não cabem ficam acessíveis via scroll
- Estrutura: mantida (mesmas colunas, mesma hierarquia Header + Rows)
- Composição atômica: mantida (Checkbox + Badge são instâncias dos BCs)

**Variantes no Figma:** 8 variantes (4 estados × 2 layouts)
- `State=Default, Layout=Desktop` / `State=Default, Layout=Mobile`
- `State=Selectable, Layout=Desktop` / `State=Selectable, Layout=Mobile`
- `State=Empty, Layout=Desktop` / `State=Empty, Layout=Mobile`
- `State=Loading, Layout=Desktop` / `State=Loading, Layout=Mobile`

---

## Casos excepcionais / bordas

- **Muitas colunas (overflow):** scroll horizontal com shadow nas bordas indicando conteúdo oculto. Header acompanha o scroll
- **Texto longo em célula:** trunca com `text-overflow: ellipsis` + `title` com texto completo. Height da linha não muda
- **0 registros:** header permanece, corpo exibe estado Empty
- **1 registro:** tabela normal sem paginação
- **Mobile (< 640px):** scroll horizontal mantido. Considerar card view como alternativa para mobile (fora deste spec — responsabilidade do produto)
- **Muitas linhas sem paginação:** scroll vertical no body com header fixo (sticky)
- **Select all com paginação:** seleciona apenas a página atual. Não seleciona todas as páginas

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-text-primary` | Texto do body |
| `--color-text-secondary` | Texto do header |
| `--color-text-muted` | Texto empty, disabled |
| `--color-primary-base` | Sort icon ativo, borda esquerda seleção |
| `--color-primary-muted` | Fundo linha selecionada |
| `--color-surface-bg` | Fundo linhas pares |
| `--color-surface-bg-subtle` | Fundo linhas ímpares (striped) |
| `--color-bg-muted` | Fundo header, hover de linha |
| `--color-border-base` | Bordas da tabela e separadores |
| `--color-border-focus` | Ring de foco |
| `--radius-md` | Border radius da tabela (6px) |
| `--font-body` | Família tipográfica |
| `--text-xs` | Font size header (12px) |
| `--text-sm` | Font size body (14px) |
| `--font-semibold` | Peso header (600) |
| `--font-regular` | Peso body (400) |
| `--space-2 / --space-3 / --space-4` | Paddings |

---

## O que está fora deste spec

- **Tabela editável (inline edit):** comportamento de produto, não do componente base
- **Filtros por coluna (dropdown no header):** pode ser adicionado como extensão futura
- **Drag-and-drop para reordenar linhas:** não identificado na DV
- **Tabela responsiva em card view:** alternativa mobile — responsabilidade do produto
- **Export (CSV/PDF):** funcionalidade de produto, não do componente
- **Column resize (arrastar largura):** pode ser adicionado como extensão futura
- **Grouped rows / row expand:** composição complexa — especificar separadamente se surgir necessidade

---

## Critérios de aceite

- [ ] 4 estados de tabela (Default, Empty, Loading, Sorted) no Figma
- [ ] 4 estados de linha (Default, Hover, Selected, Disabled) documentados
- [ ] Ordenação com 3 estados visuais (neutro ↕, ASC ▲, DESC ▼) e cores distintas
- [ ] Seleção com 3 canais (checkbox + fundo + borda esquerda) — resolve WCAG cor aten
- [ ] **Composição atômica (Regra 11):** checkboxes no Figma são instâncias de BC-13 Checkbox (118:2247), badges de status são instâncias de BC-04 Badge (124:2690) — nunca elementos manuais
- [ ] `<th scope="col">` e `aria-sort` documentados
- [ ] Navegação por teclado para ordenação (Tab + Enter/Space)
- [ ] Variante compact documentada
- [ ] Paginação integrada
- [ ] Contraste verificado para todos os elementos — mínimo 4.5:1 AA
- [ ] Violações WCAG AA (cor aten · vis aten · tip aten) resolvidas
- [ ] Violações Nielsen (H-4 **crit** · H-7 **crit** · H-1 aten · H-6 aten · H-8 aten) resolvidas
- [ ] Mapeamento de retrocompatibilidade documentado
- [ ] Revisado e aprovado por Giuliana
