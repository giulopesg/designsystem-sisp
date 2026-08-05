---
component-id: SC-11
component-name: Resource Trees
type: SISP
status: approved
sprint: 7
approved-by: [Giuliana]
approved-date: [2026-08-04]
figma-node-id: [804:7121]
---

# Component Spec — Resource Trees

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → SC-11 (cor aten · vis aten — status Completed/Required/Optional diferenciados por cor)
> - `docs/analyses/nielsen-analysis.md` → SC-11 (H-1 aten · H-2 aten · H-3 aten · H-4 aten · H-6 aten · H-7 aten · H-8 aten — sem críticos)
> - `docs/analyses/inventory.md` → SC-11
> - Screenshot de produção: `uikit-screencapture/...resource-trees...2026-06-02-19_58_07.png`

---

## O que é

Resource Trees é o componente que exibe uma árvore de recursos hierárquica no SISP. Cada nó da árvore é um "recurso" (pasta/categoria) que contém filhos (arquivos/itens). Os nós têm status de completude (Completed, Required, Optional) indicados por badges coloridas. Usado na DV para estruturar documentos de um B.O. ou pastas de evidências, onde cada recurso pode ter estado de preenchimento obrigatório.

---

## Audiência de uso

- **Policial na DV:** visualiza estrutura de documentos do B.O., sabe quais recursos são obrigatórios (Required), opcionais (Optional) e já preenchidos (Completed)
- **Devs CiASC / terceiros:** integram via config object com array de `TreeNode[]`, recebem seleção via `(selectionChange)`
- **Demilis (mantenedor):** confirmar enum de status compartilhado com SC-13 Steppers. Documentar ações dos ícones ℹ️ e ✏️

---

## Props / API

> **Padrão de API:** Híbrido — config object + `@Output` event.

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `nodes` | `TreeNode[]` | sim | — | Array de nós da árvore com filhos aninhados |
| `(selectionChange)` | `EventEmitter<TreeNode>` | não | — | Emitido ao selecionar um nó |

**TreeNode (interface inferida):**
```typescript
interface TreeNode {
  label: string;                 // Nome do recurso
  children?: TreeNode[];         // Filhos aninhados
  status?: 'completed' | 'required' | 'optional';  // Estado do recurso
  expanded?: boolean;            // Inicialmente expandido?
  icon?: string;                 // Ícone Font Awesome (opcional)
}
```

> ⚠️ **Inventário:** enum de status provavelmente compartilhado com SC-13 Steppers — confirmar com Demilis. Propriedades do TreeNode inferidas mas não documentadas oficialmente.

**Convenção Angular (híbrido):**
```html
<sisp-lib-resource-tree
  [sispLibResourceTreeConfig]="{
    nodes: [
      { label: 'Pasta 1', children: [
        { label: 'Arquivo 1-1' },
        { label: 'Arquivo 1-2' }
      ]},
      { label: 'Pasta 2' }
    ]
  }"
  (selectionChange)="onNodeSelect($event)">
</sisp-lib-resource-tree>
```

---

## Anatomia do componente

### Card de recurso (nó)
```
┌──────────────────────────────────────────────────────────┐
│  📋 Resource 1                                     [—]   │
│                                                          │
│  • Resource 1.1                                          │
│  • Resource 1.2                                          │
│  • Resource 1.3                                          │
│                                                          │
│  ┌──────────────┐                          ℹ️  ✏️        │
│  │ ✓ Completed  │                                        │
│  └──────────────┘                                        │
│  (BC-04 Badge)                       (ações: info, edit) │
└──────────────────────────────────────────────────────────┘
```

### Grid de recursos
```
┌────────────────┐  ┌────────────────┐  ┌────────────────┐
│  Resource 1    │  │  Resource 2    │  │  Resource 3    │
│  • Item 1.1   │  │  • Item 2.1   │  │  • Item 3.1   │
│  • Item 1.2   │  │  • Item 2.2   │  │  • Item 3.2   │
│  ✓ Completed  │  │  ✱ Required   │  │  ✓ Completed  │
└────────────────┘  └────────────────┘  └────────────────┘
┌────────────────┐  ┌────────────────┐
│  Resource 4    │  │  Resource 5    │
│  • Item 4.1   │  │  • Item 5.1   │
│  ? Optional   │  │  ✓ Completed  │
└────────────────┘  └────────────────┘
```

### Composição atômica (Regra 11)

| Elemento | Componente DS | Instância |
|---|---|---|
| Badge de status | BC-04 Badge Filled SM | 1× por card — variante por status (Success/Danger/Warning) |
| Ícone do card | BC-15 Icons SM | 1× por card — ícone de pasta/recurso |
| Botão expandir/colapsar | Close Button (frame manual 24×24) | 1× por card — toggle — |
| Ícone info | BC-15 Icons XS | 1× por card — ℹ️ |
| Ícone editar | BC-15 Icons XS | 1× por card — ✏️ |
| Container | Card frame | Frame com tokens (surface, border, radius, shadow) |

---

## Estados e variantes

### Status do recurso
| Status | Badge | Cor | Ícone | Significado |
|---|---|---|---|---|
| **Completed** | BC-04 Success Filled SM | `--color-success` / `--color-success-bg` | ✓ (check) | Recurso preenchido/concluído |
| **Required** | BC-04 Danger Filled SM | `--color-danger` / `--color-danger-bg` | ✱ (asterisco) | Recurso obrigatório pendente |
| **Optional** | BC-04 Warning Filled SM | `--color-warning` / `--color-warning-bg` | ? (interrogação) | Recurso opcional pendente |

### Estados do card
| Estado | Descrição visual |
|---|---|
| **Expanded** | Card mostra lista de filhos |
| **Collapsed** | Card mostra apenas título e badge (toggle —) |
| **Selected** | Card com borda de destaque (`--color-primary`) |
| **Loading** | Skeleton no conteúdo do card |

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução neste spec |
|---|---|---|
| Uso de cor (cor aten) | Status Completed/Required/Optional diferenciados por cor (verde/vermelho/âmbar) | **Badge (BC-04) com 3 canais:** (1) cor do badge, (2) ícone distinto (✓/✱/?), (3) texto do status ("Completed"/"Required"/"Optional"). Nunca apenas cor. Labels em português no DS: "Concluído"/"Obrigatório"/"Opcional" |
| Visual (vis aten) | Ícones ℹ️ e ✏️ sem função documentada | **Ações explícitas:** ℹ️ abre Popover (BC-23) com descrição do recurso. ✏️ navega para edição do recurso. Ambos com `aria-label` e focus ring |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução neste spec |
|---|---|---|
| H-1 Visibilidade (aten) | Quantidade de Required vs. Completed não evidente | **Contador resumo** acima do grid: "3 concluídos · 1 obrigatório · 1 opcional" com badges inline. Visibilidade imediata do progresso |
| H-2 Mundo real (aten) | "Resource Tree" é jargão | Labels em português: "Recursos do Registro" ou "Documentos do B.O." conforme contexto. Status: "Concluído", "Obrigatório", "Opcional" |
| H-3 Controle (aten) | Sem expandir/colapsar todos | **Ação "Expandir todos" / "Colapsar todos"** acima do grid (link texto, não botão). Cada card tem toggle individual (—) |
| H-4 Consistência (aten) | Híbrido config + @Output | Padrão documentado. Config para dados, @Output para eventos — padrão aceito para componentes de seleção |
| H-6 Reconhecimento (aten) | Ícones ℹ️ e ✏️ sem tooltip | **Tooltip** ao hover: "Ver detalhes" (ℹ️), "Editar recurso" (✏️). `aria-label` para screen readers |
| H-7 Flexibilidade (aten) | Sem atalhos | **Enter** seleciona nó, **Space** expande/colapsa, **Arrow keys** navega entre cards |
| H-8 Estética (aten) | Cards Bootstrap padrão | Tokens DS SISP: `--color-surface`, `--radius-lg`, `--shadow-sm`, `--space-4` |

---

## Regras de acessibilidade

- [ ] Grid com `role="tree"`, cards com `role="treeitem"`
- [ ] Card expandido com `aria-expanded="true"`, colapsado com `aria-expanded="false"`
- [ ] Badge de status com `aria-label="Status: Concluído"` (ou Obrigatório/Opcional)
- [ ] Badge com ícone + texto (3 canais — cor não é único)
- [ ] Ícones ℹ️ e ✏️ com `aria-label` e `role="button"`
- [ ] Focus ring: `2px solid var(--color-border-focus)`
- [ ] Navegação por teclado: Arrow keys entre cards, Enter seleciona, Space expande/colapsa
- [ ] Contraste dos badges verificado (Success/Danger/Warning sobre branco ≥ 4.5:1)
- [ ] Labels em português: "Concluído", "Obrigatório", "Opcional"

---

## Comportamentos esperados

### Renderização
- Quando `nodes` recebidos → renderiza grid de cards. Cards com `children` são expansíveis. Cards sem `children` mostram apenas título e badge
- Quando nó expandido → mostra lista de filhos como bullet list
- Quando nó colapsado → mostra apenas título e badge de status

### Seleção
- Quando clica no card → seleção visual (borda `--color-primary`). Emite `(selectionChange)` com o nó selecionado
- Quando clica em outro card → seleção muda. Seleção anterior perde destaque

### Ações
- Quando clica ℹ️ → Popover (BC-23) com descrição do recurso
- Quando clica ✏️ → emite evento ou navega para edição (responsabilidade da app)
- Quando clica — (toggle) → expande/colapsa o card

### Status
- Quando todos os `required` estão `completed` → progresso completo. Contador resumo reflete
- Quando há `required` pendente → contador destaca: "1 obrigatório pendente"

---

## Comportamento responsivo

| Precisa de variante `Layout=Mobile`? | **Sim** — grid de cards 3 colunas precisa adaptar |
|---|---|
| **Desktop** | Grid 3 colunas. Cards 300px+ com auto-flow |
| **Mobile** | Stack vertical 1 coluna. Cards full-width |
| **Tablet** | Grid 2 colunas |

---

## Composição com outros componentes

| Componente | Relação | Composição no Figma |
|---|---|---|
| **BC-04 Badge** | Status do recurso (Success/Danger/Warning) | **Instância** — Filled SM, 3 variantes por status |
| **BC-15 Icons** | Ícone do recurso, ℹ️, ✏️ | **Instância** — SM (recurso) + XS (ações) |
| **BC-23 Popover** | Detalhes do recurso (ao clicar ℹ️) | **Instância** — Title=Yes |

---

## Variantes no Figma

| Variante | Property | Descrição |
|---|---|---|
| **Default Desktop** | `State=Default, Layout=Desktop` | Grid 3 colunas, cards expandidos, status variados |
| **Default Mobile** | `State=Default, Layout=Mobile` | Stack vertical |
| **Collapsed** | `State=Collapsed, Layout=Desktop` | Cards colapsados (apenas título + badge) |

> 3 variantes. Status dos badges variam via override de instância (BC-04 swap).

---

## Tokens utilizados

| Token | Uso |
|---|---|
| `--color-surface` | Fundo dos cards |
| `--color-border` | Borda dos cards |
| `--color-primary` | Borda do card selecionado |
| `--color-success` / `--color-success-bg` | Badge Completed |
| `--color-danger` / `--color-danger-bg` | Badge Required |
| `--color-warning` / `--color-warning-bg` | Badge Optional |
| `--color-text-primary` | Título do recurso |
| `--color-text-secondary` | Lista de filhos |
| `--color-border-focus` | Focus ring |
| `--font-body` | Textos |
| `--text-sm` / `--text-base` | Tamanhos |
| `--space-4` | Padding dos cards |
| `--space-3` | Gap no grid |
| `--radius-lg` | Cards |
| `--shadow-sm` | Sombra dos cards |

---

## Enums compartilhados

| Enum | Usado por | Valores |
|---|---|---|
| `ResourceStatus` | SC-11 · SC-13 | `'completed'` · `'required'` · `'optional'` |

> ⚠️ **Confirmar com Demilis** se SC-11 e SC-13 compartilham o mesmo enum. Se sim, alteração afeta ambos os componentes.

---

## Casos excepcionais / bordas

- **Árvore vazia (`nodes=[]`):** mensagem "Nenhum recurso disponível"
- **Árvore profunda (> 3 níveis):** renderizar apenas 2 níveis no card. Filhos profundos mostram "..." com expansão on-demand
- **Muitos nós (> 20):** scroll vertical no container. Sem paginação — scroll contínuo
- **Nó sem status:** renderizar sem badge. Tratado como neutro
- **Nó sem children:** renderizar como item simples (sem toggle de expansão)
- **Edição concluída (✏️):** após editar um recurso Required, status muda para Completed. Animação sutil de transição do badge

---

## O que está fora deste spec

- **Drag-and-drop:** reordenação de nós — funcionalidade futura
- **Upload de arquivo no nó:** responsabilidade de SC-15 Uploaders integrado na app
- **Criação de novos nós:** responsabilidade da aplicação host
- **Níveis profundos de aninhamento (> 3):** manter 2 níveis por card, expansão on-demand para profundidade
- **Enum compartilhado:** resolver com Demilis antes da implementação Angular

---

## Critérios de aceite

- [ ] Grid de cards com título, lista de filhos, badge de status
- [ ] 3 status visuais: Completed (Success), Required (Danger), Optional (Warning) — cada um com ícone + texto + cor (3 canais)
- [ ] Badges como instâncias de BC-04 Filled SM (Regra 11)
- [ ] Ícones como instâncias de BC-15 (Regra 11)
- [ ] Expand/collapse por card + "Expandir todos"/"Colapsar todos"
- [ ] Contador resumo de status acima do grid
- [ ] Variantes Desktop e Mobile (Regra 13)
- [ ] Tokens aplicados (Regra 8)
- [ ] WCAG (cor aten · vis aten) resolvidas — badges com 3 canais
- [ ] Labels em português: "Concluído", "Obrigatório", "Opcional"
- [ ] Revisado e aprovado por Giuliana
