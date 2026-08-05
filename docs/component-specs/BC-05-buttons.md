---
component-id: BC-05
component-name: Buttons
type: Base
status: approved
sprint: 3
approved-by: [Giuliana]
approved-date: [2026-07-13]
figma-node-id: [116:1862]
---

# Component Spec — Buttons

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → BC-05 (cor aten · vis aten)
> - `docs/analyses/nielsen-analysis.md` → BC-05 (H-1 aten · H-4 crit · H-5 aten)
> - `docs/analyses/inventory.md` → BC-05

---

## O que é

Botão é o componente de ação primário do DS. Dispara ações do usuário: submeter formulários, navegar, confirmar, cancelar, excluir. É o componente mais crítico para identidade visual — atualmente 100% Bootstrap sem identidade SISP.

---

## Audiência de uso

- **Devs CiASC / terceiros:** consomem o componente em todas as telas dos produtos SISP
- **Policial na DV:** interage com botões em cada etapa do BO — "Consultar", "Salvar", "Avançar", "Cancelar"
- **POs (Sommer/Holiwod):** precisam de consistência visual que comunique hierarquia de ação

---

## Hierarquia de ação — 4 níveis

> **Decisão de design:** substituir as 8 variantes Bootstrap por 4 níveis semânticos de ação. O componente Angular mantém retrocompatibilidade com `SispLibStyleType`, mas a documentação e o Figma orientam exclusivamente os 4 níveis abaixo.

| Nível | Nome | Uso | Regra |
|---|---|---|---|
| 1 | **Primary** | Ação principal da tela | Máximo 1 por viewport visível |
| 2 | **Secondary** | Ação complementar | Acompanha o Primary ou agrupa ações de igual peso |
| 3 | **Tertiary** | Ação de baixa ênfase | "Cancelar", "Voltar", links de ação |
| 4 | **Danger** | Ação destrutiva | "Excluir", "Remover" — sempre exige confirmação |

---

## Props / API

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `type` | `SispLibStyleType` | não | `'primary'` | Variante visual — usar `primary`, `secondary`, `tertiary`, `danger` |
| `label` | `string` | sim | — | Texto do botão. Verbo no imperativo: "Consultar", "Salvar", "Excluir" |
| `icon` | `string` | não | — | Classe Font Awesome (ex: `fa-solid fa-search`). Posiciona à esquerda do label |
| `iconPosition` | `'left' \| 'right'` | não | `'left'` | Posição do ícone em relação ao label |
| `disabled` | `boolean` | não | `false` | Desabilita interação e aplica visual de opacidade reduzida |
| `loading` | `boolean` | não | `false` | Substitui ícone por spinner, desabilita clique, mantém largura |
| `size` | `'sm' \| 'md' \| 'lg'` | não | `'md'` | Tamanho do botão |
| `fullWidth` | `boolean` | não | `false` | Botão ocupa 100% da largura do container |

**Convenção Angular:**
```html
<sisp-lib-button [sispLibButtonConfig]="config"></sisp-lib-button>
```

**Exemplo de uso:**
```typescript
config: SispLibButtonConfig = {
  type: 'primary',
  label: 'Consultar',
  icon: 'fa-solid fa-search',
  size: 'md'
};
```

---

## Estados e variantes

### Primary (filled)

| Estado | Descrição visual | Tokens |
|---|---|---|
| Default | Fundo vermelho SC, texto branco, radius-md | `bg: --color-primary` · `text: --color-primary-contrast` · `radius: --radius-md` |
| Hover | Fundo vermelho escuro | `bg: --color-primary-hover` |
| Focus | Ring 2px offset vermelho SC | `ring: --color-border-focus` · `offset: 2px` |
| Active | Fundo vermelho escuro, leve escala (scale 0.98) | `bg: --color-primary-hover` |
| Disabled | Opacidade 0.5, cursor not-allowed | `opacity: 0.5` |
| Loading | Spinner branco no lugar do ícone, label mantido, clique bloqueado | `spinner: --color-primary-contrast` |

### Secondary (outlined)

| Estado | Descrição visual | Tokens |
|---|---|---|
| Default | Borda 1px vermelho SC, fundo transparente, texto vermelho SC | `border: --color-primary` · `text: --color-primary` · `bg: transparent` |
| Hover | Fundo vermelho sutil | `bg: --color-primary-muted` |
| Focus | Ring 2px offset vermelho SC | `ring: --color-border-focus` |
| Active | Fundo vermelho sutil, borda escura | `bg: --color-primary-muted` · `border: --color-primary-hover` |
| Disabled | Opacidade 0.5, cursor not-allowed | `opacity: 0.5` |
| Loading | Spinner vermelho SC no lugar do ícone | `spinner: --color-primary` |

### Tertiary (ghost)

| Estado | Descrição visual | Tokens |
|---|---|---|
| Default | Sem borda, sem fundo, texto vermelho SC, underline opcional | `text: --color-primary` · `bg: transparent` · `border: none` |
| Hover | Fundo sutil neutro | `bg: --color-bg-subtle` |
| Focus | Ring 2px offset vermelho SC | `ring: --color-border-focus` |
| Active | Fundo neutro mais forte | `bg: --color-bg-muted` |
| Disabled | Opacidade 0.5, cursor not-allowed | `opacity: 0.5` |
| Loading | Spinner vermelho SC no lugar do ícone | `spinner: --color-primary` |

### Danger (filled destrutivo)

| Estado | Descrição visual | Tokens |
|---|---|---|
| Default | Fundo danger, texto branco | `bg: --color-danger` · `text: #FFFFFF` |
| Hover | Fundo danger escurecido (~15%) | `bg: --color-danger` darkened |
| Focus | Ring 2px offset danger | `ring: --color-danger` |
| Active | Fundo danger escurecido | `bg: --color-danger` darkened |
| Disabled | Opacidade 0.5, cursor not-allowed | `opacity: 0.5` |
| Loading | Spinner branco no lugar do ícone | `spinner: #FFFFFF` |

### Tamanhos

| Tamanho | Altura | Padding horizontal | Font size | Ícone |
|---|---|---|---|---|
| `sm` | 32px | `--space-3` (12px) | `--text-sm` (14px) | 14px |
| `md` | 40px | `--space-4` (16px) | `--text-base` (16px) | 16px |
| `lg` | 48px | `--space-6` (24px) | `--text-lg` (18px) | 18px |

### Variante icon-only

Quando `label` está vazio e `icon` está preenchido:
- Botão assume formato quadrado (largura = altura)
- `aria-label` **obrigatório** (fallback do label visual)
- Tooltip com o nome da ação no hover

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução neste spec |
|---|---|---|
| Contraste (wcag) | Não verificado formalmente | Primary: branco (#FFF) sobre #C4000B = 5.2:1 ✅ AA. Danger: branco sobre #991B1B = 7.8:1 ✅ AAA. Secondary/Tertiary: #C4000B sobre branco = 5.2:1 ✅ AA |
| Uso de cor (cor) | Visual 100% Bootstrap sem identidade SISP | 4 hierarquias visuais distintas por forma (filled, outlined, ghost, filled danger) — não apenas por cor |
| Visual (vis) | Estado loading não documentado | Loading state especificado: spinner + label mantido + `aria-busy="true"` + clique bloqueado |
| Tipografia (tip) | ok | Font sizes definidos por token: sm=14px, md=16px, lg=18px — todos ≥ mínimo WCAG |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução neste spec |
|---|---|---|
| H-1 Visibilidade de estado | Sem feedback visual de loading/processando | Estado Loading com spinner animado, label mantido ("Salvando..."), `aria-busy="true"`. Width preservada para evitar layout shift |
| H-4 Consistência (CRIT) | Visual 100% Bootstrap. 8 variantes sem hierarquia. `SispLibStyleType` usado sem guia semântico | 4 níveis de hierarquia documentados. Regra: máx. 1 Primary por viewport. Retrocompatibilidade com enum mantida, mas documentação orienta os 4 níveis |
| H-5 Prevenção de erros | Estado disabled sem diferenciação clara | Disabled: `opacity: 0.5` + `cursor: not-allowed` + `aria-disabled="true"`. Danger sempre exige confirmação (via BC-09 Confirmation Modal) |

---

## Regras de acessibilidade

- [x] Contraste mínimo 4.5:1 para texto sobre fundo — verificado para todas as 4 variantes
- [ ] Focus ring visível: `2px solid var(--color-border-focus)` com `offset: 2px` — nunca removido
- [ ] Navegável por teclado: `Tab` para focar, `Enter` ou `Space` para ativar
- [ ] `aria-disabled="true"` quando disabled (não remover do DOM — manter focável para screen reader)
- [ ] `aria-busy="true"` quando loading
- [ ] `aria-label` obrigatório em botão icon-only
- [ ] Label em português: verbos no imperativo ("Consultar", "Salvar", "Excluir")
- [ ] Não depende apenas de cor: Primary (filled), Secondary (outlined), Tertiary (ghost) são formas distintas

---

## Comportamentos esperados

- Quando `loading = true` → ícone substituído por spinner, label mantido (pode mudar para gerúndio: "Salvando..."), clique bloqueado, largura preservada
- Quando `disabled = true` → opacidade 0.5, cursor not-allowed, `aria-disabled="true"`, não dispara evento
- Quando `type = 'danger'` → sempre usado em par com BC-09 Confirmation Modal para ações destrutivas
- Quando `fullWidth = true` → botão ocupa 100% do container pai
- Quando `icon` + `label` → ícone à esquerda (padrão) ou à direita do label, com `--space-2` (8px) de gap
- Quando `icon` sem `label` → botão quadrado, `aria-label` obrigatório, tooltip no hover

---

## Agrupamento de botões

Quando múltiplos botões aparecem juntos:
- Ordem: **Primary à direita**, Secondary/Tertiary à esquerda (padrão ocidental de leitura → ação principal por último)
- Gap entre botões: `--space-3` (12px)
- Em mobile (< 640px): stack vertical, Primary embaixo (mais acessível ao polegar)
- Máximo 1 Primary visível por viewport

---

## Mapeamento de retrocompatibilidade

| SispLibStyleType antigo | Mapeamento novo | Nota |
|---|---|---|
| `primary` | **Primary** | Direto |
| `secondary` | **Secondary** | Direto |
| `danger` | **Danger** | Direto |
| `success` | Secondary (com ícone ✓) | Botões não são semânticos de status |
| `warning` | Secondary (com ícone ⚠) | Botões não são semânticos de status |
| `info` | Secondary | Sem uso real em botões |
| `dark` | Secondary | Visual escuro não pertence a botões |
| `light` | Tertiary | Ghost substitui variante Light |

---

## Casos excepcionais / bordas

- **Label muito longo:** texto trunca com `text-overflow: ellipsis` + `title` com texto completo. Máximo recomendado: 3 palavras
- **Sem ícone e sem label:** componente não renderiza (validação no Angular)
- **Mobile (< 640px):** tamanho mínimo `md` (40px de altura) para target area de toque (WCAG 2.5.8: ≥ 24×24px, recomendado 44×44px)
- **Dentro de formulário:** `type="submit"` no HTML nativo — comportamento controlado pelo BC-13 Forms
- **Loading prolongado:** se loading > 10s, considerar feedback adicional (toast ou mensagem inline) — responsabilidade do produto, não do componente

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-primary` | Fundo Primary, texto/borda Secondary, texto Tertiary |
| `--color-primary-hover` | Hover Primary/Secondary |
| `--color-primary-muted` | Fundo hover Secondary |
| `--color-primary-contrast` | Texto sobre Primary |
| `--color-danger` | Fundo Danger |
| `--color-border-focus` | Ring de foco (todas as variantes) |
| `--color-bg-subtle` | Hover Tertiary |
| `--color-bg-muted` | Active Tertiary |
| `--radius-md` | Border radius (6px) |
| `--text-sm / --text-base / --text-lg` | Font size por tamanho |
| `--font-body` | Família tipográfica (Arial) |
| `--font-semibold` | Peso do label (600) |
| `--space-2 / --space-3 / --space-4 / --space-6` | Gaps e paddings |
| `--transition-fast` | Transições de hover/active (150ms) |

---

## O que está fora deste spec

- **Toggle button / switch:** componente separado (não é variante de botão)
- **Button group (toolbar):** pode ser especificado como padrão de layout, não como componente próprio
- **Split button (botão com dropdown):** combina BC-05 + BC-10 — especificar se necessário
- **FAB (floating action button):** não identificado no inventário SISP — não entra
- **Temas por cliente:** os tokens semânticos já suportam theming (ex: PC usa dourado em `--color-primary`). O botão não precisa de lógica adicional

---

## Critérios de aceite

- [ ] 4 variantes (Primary, Secondary, Tertiary, Danger) existem no Figma com todos os estados
- [ ] 3 tamanhos (sm, md, lg) documentados no Figma
- [ ] Estado Loading com spinner especificado
- [ ] Variante icon-only com aria-label documentada
- [ ] Focus ring visível em todas as variantes
- [ ] Violações WCAG AA (cor aten · vis aten) resolvidas
- [ ] Violações Nielsen (H-1 aten · H-4 crit · H-5 aten) resolvidas
- [ ] Todos os tokens referenciados existem em `design-tokens/tokens.css`
- [ ] Regra de agrupamento documentada (Primary à direita, stack em mobile)
- [ ] Mapeamento de retrocompatibilidade com `SispLibStyleType` documentado
- [ ] Revisado e aprovado por Giuliana
