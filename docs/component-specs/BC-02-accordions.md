---
component-id: BC-02
component-name: Accordions
type: Base
status: approved
sprint: 4.1
approved-by: [Giuliana]
approved-date: [2026-07-16]
figma-node-id: [315:747]
---

# Component Spec — Accordions

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → BC-02 (cor aten · vis aten — estado disabled sem diferenciacao clara)
> - `docs/analyses/nielsen-analysis.md` → BC-02 (H-1 aten · H-4 aten · H-6 aten)
> - `docs/analyses/inventory.md` → BC-02

---

## O que e

Accordion e o componente de conteudo colapsavel do DS SISP. Agrupa seções de conteudo sob headers clicaveis que expandem/recolhem o corpo. Na DV, aparece em formularios longos (dados do BO, detalhes de ocorrencia) e paineis informativos onde o usuario precisa ver uma seção por vez sem perder contexto das demais.

---

## Audiencia de uso

- **Policial na DV:** usa accordions para navegar seções de formularios longos (ex: dados pessoais, detalhes da ocorrencia, testemunhas) sem scroll excessivo
- **Devs CiASC / terceiros:** usam accordions para organizar conteudo colapsavel. Precisam configurar items, comportamento de fechamento automatico (closeOthers) e estado inicial
- **POs (Sommer/Holiwod):** precisam que formularios longos sejam navegaveis e nao intimidem o usuario

---

## Props / API

| Prop | Tipo | Obrigatorio | Padrao | Descricao |
|---|---|---|---|---|
| `items` | `AccordionItem[]` | sim | — | Lista de seções do accordion |
| `closeOthers` | `boolean` | nao | `false` | Quando true, expandir um item recolhe os demais (modo "exclusivo") |
| `destroyOnHide` | `boolean` | nao | `true` | Quando true, conteudo recolhido e removido do DOM (performance) |
| `expandAll` | `boolean` | nao | `false` | Quando true, todos os items iniciam expandidos |

**AccordionItem:**
```typescript
interface AccordionItem {
  title: string;        // Texto do header — em portugues
  body: string | TemplateRef;  // Conteudo da seção (texto ou template Angular)
  expanded?: boolean;   // Estado inicial (expandido ou recolhido)
  disabled?: boolean;   // Seção desabilitada (nao expande/recolhe)
  icon?: string;        // Classe Font Awesome opcional no header
}
```

**Convencao Angular:**
```html
<sisp-lib-accordion [sispLibAccordionConfig]="config"></sisp-lib-accordion>
```

**Exemplo de uso:**
```typescript
config: SispLibAccordionConfig = {
  closeOthers: true,
  items: [
    { title: 'Dados Pessoais', body: dadosPessoaisTemplate, expanded: true },
    { title: 'Detalhes da Ocorrencia', body: detalhesTemplate },
    { title: 'Testemunhas', body: testemunhasTemplate },
    { title: 'Anexos', body: anexosTemplate, icon: 'fa-solid fa-paperclip' }
  ]
};
```

---

## Anatomia do componente

```
┌─────────────────────────────────────────────────────────────┐
│  ▼  Dados Pessoais                                          │  ← header expandido
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  [conteudo da seção — texto, formulario, etc.]             │  ← body
│                                                             │
├─────────────────────────────────────────────────────────────┤
│  ▶  Detalhes da Ocorrencia                                  │  ← header recolhido
├─────────────────────────────────────────────────────────────┤
│  ▶  Testemunhas                                             │  ← header recolhido
├─────────────────────────────────────────────────────────────┤
│  ▶  Anexos                                            [🔒]  │  ← header disabled
└─────────────────────────────────────────────────────────────┘
```

- **Container:** borda 1px --color-border, border-radius --radius-md
- **Header:** area clicavel com titulo + chevron (icone de expand/collapse)
- **Chevron:** icone fa-chevron-down (expandido, rotacionado 180°) ou fa-chevron-right (recolhido)
- **Body:** conteudo visivel quando expandido, com padding interno
- **Separadores:** borda 1px entre items adjacentes

---

## Estados e variantes

### Estados do accordion item

| Estado | Descricao visual | Tokens |
|---|---|---|
| **Collapsed** | Header com chevron ▶, body oculto | `bg: --color-surface` · `text: --color-text-primary` · `border: --color-border` |
| **Expanded** | Header com chevron ▼, body visivel | `bg: --color-surface` · `text: --color-text-primary` · `header-bg: --color-surface-muted` |
| **Hover** | Header com fundo sutil | `bg: --color-surface-hover` |
| **Focus** | Focus ring no header | `outline: 2px solid var(--color-border-focus)` · `outline-offset: -2px` |
| **Disabled** | Header nao clicavel, 3 canais | `text: --color-text-muted` · `opacity: 0.5` · `cursor: not-allowed` · chevron oculto |

### Cores

| Elemento | Token | Valor |
|---|---|---|
| Container fundo | `--color-surface` | #FFFFFF |
| Container borda | `--color-border` | #E5E7EB |
| Header texto | `--color-text-primary` | #08060F |
| Header fundo (expandido) | `--color-surface-muted` | #F9FAFB |
| Header fundo (hover) | `--color-surface-hover` | #F3F4F6 |
| Chevron | `--color-text-secondary` | #4B5563 |
| Body texto | `--color-text-primary` | #08060F |
| Disabled texto | `--color-text-muted` | #9CA3AF |
| Focus ring | `--color-border-focus` | #C4000B |

### Verificacao de contraste (WCAG AA)

| Elemento | Texto | Fundo | Ratio | Resultado |
|---|---|---|---|---|
| Header texto | #08060F | #FFFFFF | >15:1 | ✅ AAA |
| Header expandido | #08060F | #F9FAFB | >14:1 | ✅ AAA |
| Disabled | #9CA3AF | #FFFFFF | ~2.8:1 | ✅ (decorativo — item nao interativo + opacity) |
| Chevron | #4B5563 | #FFFFFF | ~7.2:1 | ✅ AAA |

### Dimensoes

| Propriedade | Valor | Token |
|---|---|---|
| Container border-radius | 8px | `--radius-md` |
| Header height (min) | 48px | — |
| Header padding horizontal | 16px | `--space-4` |
| Header padding vertical | 12px | `--space-3` |
| Body padding | 16px | `--space-4` |
| Font size header | 14px | `--text-sm` |
| Font weight header | 600 | `--font-semibold` |
| Font size body | 14px | `--text-sm` |
| Font weight body | 400 | `--font-regular` |
| Chevron size | 16px | — |
| Gap chevron → titulo | 12px | `--space-3` |
| Gap icone → titulo | 8px | `--space-2` |
| Border separador | 1px solid | `--color-border` |

---

## Violacoes a resolver — WCAG AA

| Dimensao | Violacao atual | Solucao neste spec |
|---|---|---|
| Uso de cor (cor aten) | Estado disabled sem diferenciacao clara | 3 canais para disabled: (1) texto muted, (2) opacity 0.5, (3) chevron oculto. Cursor not-allowed. Nao depende apenas de cor |
| Visual (vis aten) | Estado disabled sem diferenciacao clara | Chevron removido no disabled (canal visual forte). aria-disabled="true" para screen readers. Focus ring visivel nos items ativos |

---

## Violacoes a resolver — Heuristicas Nielsen

| Heuristica | Violacao atual | Solucao neste spec |
|---|---|---|
| H-1 Visibilidade (aten) | Estado expandido/recolhido nao claramente diferenciado | Chevron com rotacao animada (▶ → ▼). Header do item expandido com fundo diferenciado (--color-surface-muted). aria-expanded para screen readers |
| H-4 Consistencia (aten) | Sem padrao documentado | Accordion padronizado com config object (sispLibAccordionConfig). Mesmo visual em todos os produtos. closeOthers documentado como comportamento opcional |
| H-6 Reconhecimento (aten) | Usuario precisa memorizar estado | Chevron visivel em todos os items indica claramente se expandido ou recolhido. Fundo diferenciado no item expandido reforça o estado. Icone opcional no header ajuda a identificar a seção |

---

## Regras de acessibilidade

- [ ] Headers com `role="button"` ou `<button>` semantico
- [ ] `aria-expanded="true|false"` no header de cada item
- [ ] `aria-controls` apontando para o id do body
- [ ] Body com `role="region"` e `aria-labelledby` apontando para o header
- [ ] Items desabilitados com `aria-disabled="true"` e nao focaveis
- [ ] **Navegacao por teclado:**
  - `Tab` move entre headers
  - `Enter` / `Space` expande/recolhe o item focado
  - `↑` / `↓` move entre headers (quando dentro do grupo)
  - `Home` / `End` move para primeiro/ultimo header
- [ ] Focus ring visivel: `2px solid var(--color-border-focus)`
- [ ] Contraste minimo 4.5:1 — verificado
- [ ] Nao depende apenas de cor (3 canais para disabled, 2 canais para expanded)
- [ ] Labels em portugues

---

## Comportamentos esperados

- Quando usuario clica no header → item expande (se recolhido) ou recolhe (se expandido)
- Quando `closeOthers = true` e usuario expande um item → todos os demais recolhem automaticamente
- Quando `closeOthers = false` → multiplos items podem estar expandidos simultaneamente
- Quando `destroyOnHide = true` → conteudo do body e removido do DOM ao recolher (performance)
- Quando `destroyOnHide = false` → conteudo permanece no DOM mas oculto (preserva estado de formularios)
- Quando item tem `disabled = true` → header visivel com estilo disabled, nao responde a clique, nao focavel
- Quando item tem `expanded = true` (inicial) → item inicia expandido
- Quando item tem `icon` → icone exibido a esquerda do titulo com gap --space-2
- Quando `expandAll = true` → todos os items iniciam expandidos (ignora `expanded` individual)
- Quando chevron e clicado → mesma acao de clicar no header (area de clique e todo o header)

---

## Composicao com outros componentes

| Componente | Relacao | Composicao no Figma (Regra 11/12) |
|---|---|---|
| **BC-15 Icons** | Chevron expand/collapse e icones opcionais no header | Font Awesome — `aria-hidden="true"` nos icones |

> **Regra 12 aplicada:** accordion nao reutiliza outros componentes como instancias. Headers nao sao Buttons (comportamento e semantica diferente — expand/collapse vs. acao). Chevrons sao icones Font Awesome nativos. O ícone ≡ do inventario original e decorativo — substituido por chevron funcional neste spec.

---

## Mapeamento de retrocompatibilidade

| Prop atual | Mapeamento novo | Nota |
|---|---|---|
| `content: [{title, body}]` | `items: AccordionItem[]` | Renomeado para clareza. Extendido com `icon`, `disabled`, `expanded` |
| `closeOthers` | `closeOthers` | Mantido |
| `destroyOnHide` | `destroyOnHide` | Mantido |
| — | `expandAll` (novo) | Atalho para expandir todos |

---

## Casos excepcionais / bordas

- **0 items:** componente nao renderiza
- **1 item:** renderiza normalmente — valido para seção unica colapsavel
- **Body vazio:** header renderiza normalmente, body vazio mas area de padding visivel
- **Titulo longo:** wrap natural (multiline header). Min-height 48px garante area de clique
- **Conteudo muito longo no body:** scroll natural da pagina. Nao ha scroll interno no body
- **Todos disabled:** componente renderiza mas nenhum item e interativo
- **Mobile (< 640px):** layout identico — accordion e full-width por natureza. Touch targets >= 48px (header height)
- **Nested accordions:** suportado mas nao recomendado (complexidade cognitiva). Max 2 niveis

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-surface` | Fundo container e headers |
| `--color-surface-muted` | Fundo header expandido |
| `--color-surface-hover` | Fundo header hover |
| `--color-border` | Bordas e separadores |
| `--color-text-primary` | Texto header e body |
| `--color-text-secondary` | Chevron |
| `--color-text-muted` | Texto disabled |
| `--color-border-focus` | Focus ring |
| `--radius-md` | Border radius container |
| `--text-sm` | Font size (14px) |
| `--font-semibold` | Peso header (600) |
| `--font-regular` | Peso body (400) |
| `--space-4` | Padding horizontal e body |
| `--space-3` | Padding vertical header, gap chevron→titulo |
| `--space-2` | Gap icone→titulo |

---

## O que esta fora deste spec

- **Accordion com drag-and-drop:** reordenacao de items nao e padrao (o icone ≡ do inventario era decorativo, nao funcional)
- **Accordion com search/filtro:** usar BC-13 Input + logica de filtro no conteudo
- **Accordion horizontal:** nao e padrao — usar BC-26 Tabs para navegacao horizontal
- **Accordion com stepper:** usar SC-XX Stepper (SISP Component) para progresso sequencial
- **Animacao de transicao:** sugerida (height transition 200ms ease) mas implementacao Angular define

---

## Criterios de aceite

- [ ] 2 variantes no Figma: Collapsed e Expanded (com multiplos items)
- [ ] Chevron rotacao visual (▶ recolhido, ▼ expandido)
- [ ] Header expandido com fundo diferenciado (--color-surface-muted)
- [ ] Estado disabled com 3 canais (texto muted + opacity + chevron oculto)
- [ ] Contraste verificado — >15:1 AAA para texto principal
- [ ] ARIA documentado: aria-expanded, aria-controls, role="region"
- [ ] Navegacao por teclado documentada (Tab, Enter, ↑/↓, Home/End)
- [ ] Violacoes WCAG (cor aten · vis aten) resolvidas — 3 canais para disabled
- [ ] Violacoes Nielsen (H-1 · H-4 · H-6 aten) resolvidas
- [ ] Mapeamento de retrocompatibilidade documentado
- [ ] Revisado e aprovado por Giuliana
