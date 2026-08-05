---
component-id: BC-10
component-name: Dropdowns
type: Base
status: approved
sprint: 4
approved-by: [Giuliana]
approved-date: [2026-07-14]
figma-node-id: [188:492]
---

# Component Spec — Dropdowns

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → BC-10 (contraste aten · cor aten · vis aten)
> - `docs/analyses/nielsen-analysis.md` → BC-10 (H-1 aten · H-3 aten · H-4 aten · H-6 aten)
> - `docs/analyses/inventory.md` → BC-10

---

## O que é

Dropdown é o componente de menu de ações contextuais do DS SISP. Exibe uma lista de ações ao clicar em um botão trigger — editar, excluir, exportar, compartilhar. Na DV, dropdowns aparecem em ações de linha de tabela ("⋮"), ações de card, e menus contextuais. Diferente do Select (BC-13): Select escolhe um valor em formulário, Dropdown dispara ações. Atualmente funcional, mas sem tokens aplicados e item selecionado diferenciado apenas por cor.

> **Regra 12 aplicada:** trigger é instância de BC-05 Button (composição atômica). Menu items são nativos deste componente (padrão funcional distinto de Select).

---

## Audiência de uso

- **Policial na DV:** usa dropdown para ações em BOs (Editar, Excluir, Imprimir, Exportar PDF), ações em linhas de tabela, opções de contexto
- **Devs CiASC / terceiros:** usam dropdown para agrupar ações secundárias que não cabem como botões visíveis. Precisam de API simples com label, ação e ícone por item
- **POs (Sommer/Holiwod):** precisam que ações fiquem acessíveis mas não poluam a interface

---

## Props / API

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `label` | `string` | sim | — | Texto do botão trigger |
| `items` | `DropdownItem[]` | sim | — | Lista de itens do menu |
| `placement` | `'bottom-start' \| 'bottom-end'` | não | `'bottom-start'` | Posição do menu em relação ao trigger |
| `icon` | `string` | não | — | Ícone no trigger (ex: `'fa-solid fa-ellipsis-vertical'` para menu ⋮) |
| `triggerStyle` | `'secondary' \| 'tertiary'` | não | `'secondary'` | Estilo do botão trigger — mapeia para variante de BC-05 Button |

**DropdownItem:**
```typescript
interface DropdownItem {
  label: string;          // Texto do item
  action?: Function;      // Callback ao clicar
  icon?: string;          // Classe Font Awesome opcional (esquerda do label)
  disabled?: boolean;     // Item desabilitado
  danger?: boolean;       // Estilo destrutivo (texto vermelho)
  divider?: boolean;      // Quando true, renderiza separador em vez de item
}
```

**Convenção Angular:**
```html
<sisp-lib-dropdown [sispLibDropdownConfig]="config"></sisp-lib-dropdown>
```

**Exemplo de uso:**
```typescript
config: SispLibDropdownConfig = {
  label: 'Ações',
  icon: 'fa-solid fa-ellipsis-vertical',
  triggerStyle: 'tertiary',
  items: [
    { label: 'Editar', icon: 'fa-solid fa-pen', action: () => this.edit() },
    { label: 'Duplicar', icon: 'fa-solid fa-copy', action: () => this.duplicate() },
    { label: 'Exportar PDF', icon: 'fa-solid fa-file-pdf', action: () => this.export() },
    { divider: true },
    { label: 'Excluir', icon: 'fa-solid fa-trash', danger: true, action: () => this.delete() }
  ]
};
```

---

## Anatomia do componente

### Fechado
```
┌──────────┐
│  Ações ▾  │  ← BC-05 Button (Secondary/Tertiary) com chevron
└──────────┘
```

### Aberto
```
┌──────────┐
│  Ações ▾  │  ← Trigger (ativo)
├──────────────────┐
│  ✏️ Editar        │  ← Item default
│  📋 Duplicar      │  ← Item default
│  📄 Exportar PDF  │  ← Item default
│  ──────────────── │  ← Divider
│  🗑️ Excluir       │  ← Item danger (vermelho)
└──────────────────┘
```

- **Trigger:** instância de BC-05 Button com chevron ▾ indicando menu
- **Menu panel:** container flutuante com sombra, borda, border-radius
- **Menu item:** text + ícone opcional, padding uniforme, hover interativo
- **Divider:** linha separadora entre grupos de ações
- **Item danger:** texto vermelho para ações destrutivas

---

## Estados e variantes

### Estados do dropdown

| Estado | Descrição |
|---|---|
| **Closed** | Apenas trigger visível. Chevron ▾ aponta para baixo |
| **Open** | Trigger ativo + menu panel visível. Chevron pode inverter ▴ |

### Estados dos menu items

| Estado | Descrição visual | Tokens |
|---|---|---|
| **Default** | Texto escuro, sem fundo | `text: --color-text-primary` · `bg: transparent` |
| **Hover** | Fundo sutil | `bg: --color-bg-muted` · `text: --color-text-primary` |
| **Active (pressed)** | Fundo mais forte | `bg: --color-primary-muted` · `text: --color-primary-base` |
| **Disabled** | Texto opaco, não clicável | `text: --color-text-muted` · `opacity: 0.5` · `cursor: not-allowed` |
| **Danger** | Texto vermelho | `text: --color-danger` |
| **Danger hover** | Fundo vermelho sutil | `bg: --color-danger-bg` · `text: --color-danger` |

### Cores do menu panel

| Elemento | Token | Valor |
|---|---|---|
| Fundo | `--color-surface-bg` | #FFFFFF |
| Borda | `--color-border-base` | #E5E7EB |
| Sombra | `--shadow-lg` | Effect Style |
| Divider | `--color-border-base` | #E5E7EB |

### Verificação de contraste (WCAG AA)

| Elemento | Texto | Fundo | Ratio | Resultado |
|---|---|---|---|---|
| Item default | #111827 | #FFFFFF | >15:1 | ✅ AAA |
| Item hover | #111827 | #F3F4F6 | ~15.4:1 | ✅ AAA |
| Item danger | #991B1B | #FFFFFF | ~6.5:1 | ✅ AAA |
| Item disabled | #9CA3AF | #FFFFFF | ~2.9:1 | Decorativo (não interativo) |

### Dimensões

| Propriedade | Valor | Token |
|---|---|---|
| Menu min-width | 180px | — |
| Menu max-width | 280px | — |
| Item height | 36px | — |
| Item padding horizontal | 12px | `--space-3` |
| Item padding vertical | 8px | `--space-2` |
| Gap ícone → label | 8px | `--space-2` |
| Ícone size | 14px | — |
| Font size | 14px | `--text-sm` |
| Font weight | 400 | `--font-regular` |
| Border radius (panel) | 8px | `--radius-md` |
| Divider margin vertical | 4px | `--space-1` |
| Offset trigger → menu | 4px | `--space-1` |

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução neste spec |
|---|---|---|
| Contraste (aten) | Não verificado | Todos os pares verificados: item default >15:1, danger 6.5:1, hover 15.4:1. Mínimo AAA em todos os estados interativos |
| Uso de cor (cor aten) | Item selecionado/ativo sem indicador além de cor | Item hover usa **fundo sutil** (--color-bg-muted) como canal adicional. Item danger usa texto vermelho **+ ícone** reforçando a ação destrutiva. Item ativo (pressed) usa fundo --color-primary-muted — cor + fundo como 2 canais |
| Visual (vis aten) | Sem refinamento visual | Menu panel com sombra --shadow-lg, border-radius --radius-md, padding e dimensões por tokens. Ícones opcionais reforçam reconhecimento |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução neste spec |
|---|---|---|
| H-1 Visibilidade (aten) | Sem feedback de qual item está ativo/selecionado | Hover com fundo sutil (mudança imediata). Active (pressed) com fundo primário-muted. Focus ring visível por teclado. Chevron ▾/▴ indica se menu está aberto |
| H-3 Controle (aten) | Sem documentação de como fechar | Click fora fecha. `Escape` fecha. Click no trigger fecha (toggle). Focus sai do menu → fecha. Múltiplos canais de saída |
| H-4 Consistência (aten) | Sem padrão documentado | Trigger é BC-05 Button (padrão consistente do DS). Menu segue padrão de item height, padding e espaçamento do DS. Ícones Font Awesome |
| H-6 Reconhecimento (aten) | Usuário precisa lembrar ações disponíveis | Ícones opcionais nos itens reforçam reconhecimento (✏️ Editar, 🗑️ Excluir). Labels descritivos em português. Dividers agrupam ações por contexto |

---

## Regras de acessibilidade

- [ ] Trigger com `aria-haspopup="true"` e `aria-expanded="true|false"`
- [ ] Menu panel com `role="menu"`
- [ ] Cada item com `role="menuitem"`
- [ ] Items desabilitados com `aria-disabled="true"`
- [ ] Dividers com `role="separator"`
- [ ] **Navegação por teclado:**
  - `Enter` / `Space` no trigger → abre menu, foca primeiro item
  - `↓` / `↑` navega entre itens (pula disabled e dividers)
  - `Enter` / `Space` em item → executa ação e fecha menu
  - `Escape` → fecha menu, retorna foco ao trigger
  - `Home` → primeiro item. `End` → último item
  - `Tab` → fecha menu e move foco para próximo elemento focável
- [ ] Focus ring visível nos items: `2px solid var(--color-border-focus)`
- [ ] Contraste mínimo 4.5:1 em todos os estados interativos — verificado
- [ ] Items danger com ícone reforçando a ação (não apenas cor vermelha)

---

## Comportamentos esperados

- Quando usuário clica no trigger → menu abre abaixo (placement default), foco move para primeiro item. Chevron inverte (▴)
- Quando clica novamente no trigger → menu fecha (toggle)
- Quando clica fora do menu → menu fecha
- Quando `Escape` pressionado → menu fecha, foco retorna ao trigger
- Quando clica em item → action() executa, menu fecha
- Quando item tem `disabled = true` → item visível com estilo desabilitado, não responde a clique, pulado na navegação por teclado
- Quando item tem `danger = true` → texto vermelho (`--color-danger`), hover com fundo `--color-danger-bg`
- Quando item tem `divider = true` → linha horizontal separadora (não é item clicável)
- Quando menu abre e não cabe na viewport (bottom) → reposiciona para cima (flip automático)
- Quando `triggerStyle = 'tertiary'` e `icon` definido sem label → trigger mostra apenas ícone (menu ⋮)

---

## Composição com outros componentes

| Componente | Relação | Composição no Figma (Regra 11/12) |
|---|---|---|
| **BC-05 Buttons** | **Trigger é instância de Button (Secondary ou Tertiary)** | **Instância direta** — trigger reutiliza Button. Regra 12 aplicada |
| BC-25 Tables | Dropdown de ações por linha (ícone ⋮) | Composição de uso |
| BC-06 Cards | Dropdown de ações no header do card | Composição de uso |
| BC-15 Icons | Ícones nos menu items e trigger | Font Awesome — `aria-hidden="true"` |

---

## Mapeamento de retrocompatibilidade

| Prop atual | Mapeamento novo | Nota |
|---|---|---|
| `label` | `label` | Mantido |
| `items: [{label, action}]` | `items: DropdownItem[]` | Extendido com `icon`, `disabled`, `danger`, `divider` |
| `placement` | `placement` | Mantido — valores padronizados |
| — | `icon` (novo) | Ícone no trigger |
| — | `triggerStyle` (novo) | Estilo do botão trigger (secondary/tertiary) |

---

## Comportamento responsivo

> Regra 13 — Responsividade obrigatória. Breakpoints: Mobile (<768px), Tablet (768–1023px), Desktop (≥1024px).

| Precisa de variante `Layout=Mobile`? | **Sim** — pode virar bottom sheet em mobile |
|---|---|
| **Desktop** | Menu posicional abaixo do trigger (220px). Comportamento padrão dropdown |
| **Mobile** | Mesmo dropdown, levemente mais estreito (200px). Alternativa futura: bottom sheet |
| **Tablet** | Segue Desktop |

**Variantes no Figma:** 4 (2 estados × 2 layouts)

---

## Casos excepcionais / bordas

- **Muitos itens (> 8):** menu panel com `max-height: 320px` e scroll interno. Scroll com fade indicator
- **Item label longo:** trunca com `text-overflow: ellipsis`. Max-width 280px no panel
- **Sem itens:** componente não renderiza (validação Angular)
- **Apenas 1 item:** dropdown funciona normalmente — pode ser válido para ações que precisam de confirmação visual
- **Mobile (< 640px):** mesmo comportamento. Touch target do trigger ≥ 44px (garantido pelo BC-05 Button)
- **Dropdown dentro de modal:** z-index adequado para exibir sobre modal. Focus trap do modal continua ativo
- **Trigger icon-only (menu ⋮):** quando `label` é omitido e `icon` definido, trigger exibe apenas ícone com `aria-label="Abrir menu de ações"`

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-text-primary` | Texto item default |
| `--color-text-muted` | Texto item disabled |
| `--color-bg-muted` | Fundo item hover |
| `--color-primary-muted` | Fundo item active (pressed) |
| `--color-primary-base` | Texto item active |
| `--color-danger` | Texto item danger |
| `--color-danger-bg` | Fundo item danger hover |
| `--color-surface-bg` | Fundo menu panel |
| `--color-border-base` | Borda menu panel, divider |
| `--color-border-focus` | Ring de foco |
| `--radius-md` | Border radius menu panel (8px) |
| `--shadow-lg` | Sombra menu panel |
| `--font-body` | Família tipográfica |
| `--text-sm` | Font size (14px) |
| `--font-regular` | Peso do texto (400) |
| `--space-1` | Divider margin, offset trigger→menu |
| `--space-2` | Padding vertical item, gap ícone→label |
| `--space-3` | Padding horizontal item |

---

## O que está fora deste spec

- **Dropdown com sub-menus (nested):** complexidade desnecessária no contexto SISP. Se surgir necessidade, especificar como extensão
- **Dropdown com search/filtro:** padrão de combobox, não de action menu
- **Dropdown com checkbox (multi-select):** padrão de filtro, não de menu de ações
- **Split button (botão + dropdown):** pode ser adicionado como variante futura
- **Right-click context menu:** comportamento nativo do browser, não do componente

---

## Critérios de aceite

- [ ] 2 estados (Closed, Open) no Figma
- [ ] Menu panel com sombra, borda, border-radius por tokens
- [ ] 5 tipos de item documentados (Default, Hover, Disabled, Danger, Divider)
- [ ] Trigger como instância BC-05 Button (Regra 11/12)
- [ ] Contraste verificado — mínimo 6.5:1 em todos os estados interativos
- [ ] ARIA documentado: `aria-haspopup`, `aria-expanded`, `role="menu"`, `role="menuitem"`
- [ ] Navegação por teclado documentada (↑↓ Enter Escape Home End)
- [ ] Item danger com ícone (não apenas cor)
- [ ] Violações WCAG (contraste aten · cor aten · vis aten) resolvidas
- [ ] Violações Nielsen (H-1 · H-3 · H-4 · H-6 aten) resolvidas
- [ ] Mapeamento de retrocompatibilidade documentado
- [ ] Revisado e aprovado por Giuliana
