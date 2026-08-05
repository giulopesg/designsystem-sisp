---
component-id: BC-06
component-name: Cards
type: Base
status: approved
sprint: 3
approved-by: [Giuliana]
approved-date: [2026-07-13]
figma-node-id: [118:2041]
---

# Component Spec — Cards

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → BC-06 (cor aten · vis aten)
> - `docs/analyses/nielsen-analysis.md` → BC-06 (H-1 aten · H-4 aten · H-6 aten · H-8 aten)
> - `docs/analyses/inventory.md` → BC-06

---

## O que é

Card é o container de conteúdo principal do DS SISP. Agrupa informações relacionadas em uma unidade visual — títulos, dados, formulários, ações. Praticamente toda tela da Delegacia Virtual é composta por cards. É o componente que mais define a identidade visual do sistema — atualmente 100% Bootstrap sem identidade SISP.

---

## Audiência de uso

- **Policial na DV:** vê cards em cada etapa do BO — dados da ocorrência, pessoas envolvidas, veículos, anexos
- **Devs CiASC / terceiros:** usam cards como container padrão para qualquer agrupamento de conteúdo
- **POs (Sommer/Holiwod):** identidade visual do card = identidade visual do sistema inteiro

---

## Props / API

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `title` | `string` | sim | — | Título exibido no header do card |
| `subtitle` | `string` | não | — | Subtítulo abaixo do título, texto secundário |
| `icon` | `string` | não | — | Classe Font Awesome exibida à esquerda do título |
| `headingLevel` | `'h2' \| 'h3' \| 'h4' \| 'h5'` | não | `'h3'` | Nível semântico do heading — adapta à hierarquia da página |
| `collapsible` | `boolean` | não | `false` | Permite expandir/recolher o body do card |
| `collapsed` | `boolean` | não | `false` | Estado inicial: recolhido. Só funciona se `collapsible = true` |
| `closeButton` | `boolean` | não | `false` | Exibe botão de fechar (×) no header |
| `onClose` | `Function` | não | — | Callback disparado ao clicar no botão de fechar |
| `footerButtons` | `SispLibButtonConfig[]` | não | `[]` | Botões renderizados no footer (usa BC-05 Buttons) |
| `status` | `'default' \| 'success' \| 'warning' \| 'danger' \| 'info'` | não | `'default'` | Aplica borda superior colorida indicando estado semântico |
| `padding` | `'none' \| 'sm' \| 'md' \| 'lg'` | não | `'md'` | Padding interno do body |

**Convenção Angular:**
```html
<sisp-lib-card [sispLibCardConfig]="config">
  <!-- conteúdo projetado via ng-content -->
</sisp-lib-card>
```

**Exemplo de uso:**
```typescript
config: SispLibCardConfig = {
  title: 'Dados da Ocorrência',
  icon: 'fa-solid fa-file-lines',
  headingLevel: 'h2',
  collapsible: true,
  footerButtons: [
    { type: 'tertiary', label: 'Cancelar' },
    { type: 'primary', label: 'Salvar' }
  ]
};
```

**Nota:** a prop `customClass` do componente atual **não será mantida**. Ela permite CSS arbitrário que quebra consistência (violação H-4). Qualquer variação visual deve ser resolvida via props documentadas ou tokens.

---

## Anatomia do card

```
┌─────────────────────────────────────────┐
│ HEADER                                  │
│  [icon]  Title                    [▼][×]│
│          Subtitle                       │
├─────────────────────────────────────────┤
│ BODY (ng-content)                       │
│                                         │
│  [conteúdo projetado pelo consumidor]   │
│                                         │
├─────────────────────────────────────────┤
│ FOOTER (opcional)                       │
│                    [Cancelar] [Salvar]   │
└─────────────────────────────────────────┘
```

- **Header:** sempre presente. Contém título (obrigatório), ícone (opcional), chevron de collapse (se `collapsible`), botão fechar (se `closeButton`)
- **Body:** conteúdo projetado via `ng-content`. Padding configurável
- **Footer:** só aparece se `footerButtons` tem itens. Botões alinhados à direita (segue regra de agrupamento do BC-05)
- **Status border:** borda superior de 3px colorida — aparece apenas quando `status ≠ 'default'`

---

## Estados e variantes

### Visuais

| Estado | Descrição visual | Tokens |
|---|---|---|
| Default | Fundo branco, borda neutra, sombra sm, radius-lg | `bg: --color-surface` · `border: --color-border` · `shadow: --shadow-sm` · `radius: --radius-lg` |
| Hover (quando interativo) | Sombra elevada | `shadow: --shadow-md` |
| Expanded | Body visível, chevron ▼ | — |
| Collapsed | Body oculto, chevron ▶, header mantém altura | — |
| With status | Borda superior 3px na cor do status | `border-top: 3px solid var(--color-[status])` |

### Status (borda superior)

| Status | Cor da borda | Quando usar |
|---|---|---|
| `default` | Sem borda colorida | Container neutro padrão |
| `success` | `--color-success` (#166534) | Dados validados, etapa concluída |
| `warning` | `--color-warning` (#92400E) | Atenção necessária, campos incompletos |
| `danger` | `--color-danger` (#991B1B) | Erro, dados críticos |
| `info` | `--color-info` (#1E3A8A) | Informação contextual |

### Header

| Elemento | Token |
|---|---|
| Título | `font: --font-body` · `size: --text-lg` · `weight: --font-semibold` · `color: --color-text-primary` |
| Subtítulo | `size: --text-sm` · `weight: --font-regular` · `color: --color-text-secondary` |
| Ícone | `color: --color-primary` · mesmo tamanho do título |
| Chevron (collapse) | `color: --color-text-muted` · `size: --text-base` |
| Botão fechar | `color: --color-text-muted` · hover `color: --color-text-primary` |

### Body

| Padding | Valor |
|---|---|
| `none` | 0 |
| `sm` | `--space-3` (12px) |
| `md` | `--space-4` (16px) |
| `lg` | `--space-6` (24px) |

### Footer

| Propriedade | Token |
|---|---|
| Fundo | `--color-bg-subtle` |
| Borda superior | `--color-border` |
| Padding | `--space-3` (12px) horizontal · `--space-3` vertical |
| Alinhamento botões | Direita — segue regra BC-05 (Primary por último) |

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução neste spec |
|---|---|---|
| Contraste (wcag) | ok | Texto `--color-text-primary` (#08060F) sobre `--color-surface` (#FFFFFF) = 19.5:1 ✅ |
| Uso de cor (cor) | Identidade Bootstrap pura | Card com identidade SISP: borda neutra + sombra sm + radius-lg. Status diferenciado por borda colorida + rótulo semântico (acessível a daltônicos) |
| Visual (vis) | Estados não diferenciados | Collapse com animação + chevron direcional. Hover com elevação de sombra. Status com borda superior colorida |
| Tipografia (tip) | ok | Heading level configurável (h2–h5) — resolve hierarquia de documento |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução neste spec |
|---|---|---|
| H-1 Visibilidade de estado | Card colapsado sem indicação clara de que tem conteúdo oculto | Chevron direcional ▶/▼ com animação de rotação 90°. `aria-expanded` no header |
| H-4 Consistência | Visual Bootstrap. Header sempre h3. `customClass` permitia CSS arbitrário | `headingLevel` configurável. `customClass` removido. Visual com identidade SISP (tokens aplicados) |
| H-6 Reconhecimento | Sem diferenciação visual entre cards neutros e cards com ação necessária | Prop `status` com borda colorida superior — reconhecimento imediato do estado do conteúdo |
| H-8 Estética | Bootstrap genérico | Sombra sutil (`--shadow-sm`), radius-lg (8px), espaçamento por tokens, footer com fundo sutil — design limpo com identidade própria |

---

## Regras de acessibilidade

- [ ] Heading level semântico configurável (`h2`–`h5`) — nunca hardcoded como `h3`
- [ ] `aria-expanded="true|false"` no botão de collapse
- [ ] Collapse ativável por `Enter` ou `Space` no header
- [ ] Botão fechar com `aria-label="Fechar [título do card]"`
- [ ] Status com borda colorida **e** sr-only text indicando o tipo ("Sucesso", "Atenção", "Erro", "Informação")
- [ ] Focus ring visível no botão de collapse e botão de fechar: `var(--color-border-focus)`
- [ ] Footer buttons seguem regras de acessibilidade do BC-05

---

## Comportamentos esperados

- Quando `collapsible = true` e usuário clica no header → body faz toggle com animação de altura (200ms ease)
- Quando `collapsed = true` no init → card inicia recolhido, chevron aponta ▶
- Quando `closeButton = true` e usuário clica × → dispara `onClose` callback. O card **não se remove do DOM sozinho** — o consumidor controla a remoção
- Quando `footerButtons` está vazio → footer não renderiza
- Quando `status` muda dinamicamente → borda superior anima cor com `--transition-normal` (200ms)
- Quando card está dentro de outro card (nesting) → nível de heading incrementa automaticamente (h2 → h3 → h4)

---

## Composição com outros componentes

| Componente | Relação |
|---|---|
| BC-05 Buttons | Footer buttons renderizados como `sisp-lib-button` |
| BC-13 Forms | Cards frequentemente contêm formulários — padding `none` recomendado para formulários full-width |
| BC-25 Tables | Cards contêm tabelas — padding `none` recomendado para tabelas full-width |
| SC-02/03/04 Consultas | Resultados de consulta exibidos em cards com status |
| SC-13 Steppers | Cada step pode conter um card |

---

## Comportamento responsivo

> Regra 13 — Responsividade obrigatória. Breakpoints: Mobile (<768px), Tablet (768–1023px), Desktop (≥1024px).

| Precisa de variante `Layout=Mobile`? | **Sim** — cards em grid reorganizam |
|---|---|
| **Desktop** | Card 400px. Grid: 2-3 colunas |
| **Mobile** | Card 343px (375 - 2×16). Grid: stack vertical 1 coluna |
| **Tablet** | Segue Desktop — grid 2 colunas |

**Variantes no Figma:** 20 (10 estados × 2 layouts)

---

## Casos excepcionais / bordas

- **Título muito longo:** trunca com `text-overflow: ellipsis` em tela pequena. Tooltip com título completo
- **Sem conteúdo no body:** card renderiza com header + footer (se houver). Body vazio é válido (cards de status, KPIs)
- **Cards aninhados:** nível de heading incrementa. Sombra do card interno removida para evitar poluição visual (usa apenas borda)
- **Mobile (< 640px):** card ocupa 100% da largura. Padding reduz para `sm` automaticamente. Footer buttons stack vertical

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-surface` | Fundo do card |
| `--color-bg-subtle` | Fundo do footer |
| `--color-border` | Borda do card e separador header/footer |
| `--color-text-primary` | Título |
| `--color-text-secondary` | Subtítulo |
| `--color-text-muted` | Chevron, botão fechar |
| `--color-primary` | Ícone do header |
| `--color-success / warning / danger / info` | Borda superior de status |
| `--color-border-focus` | Ring de foco |
| `--shadow-sm` | Sombra padrão |
| `--shadow-md` | Sombra hover |
| `--radius-lg` | Border radius (8px) |
| `--font-body` | Família tipográfica |
| `--text-lg / --text-sm` | Tamanhos de título e subtítulo |
| `--font-semibold / --font-regular` | Pesos |
| `--space-3 / --space-4 / --space-6` | Paddings |
| `--transition-normal` | Animação de collapse e status |

---

## O que está fora deste spec

- **Card como link (clicável inteiro):** não identificado no inventário. Se surgir necessidade, especificar separadamente
- **Card com imagem/thumbnail:** não existe no SISP atual. Não adicionar complexidade que não é usada
- **Card carousel (horizontal scroll de cards):** combina BC-06 + BC-07 — não entra neste spec
- **`customClass`:** removida intencionalmente. Se um consumidor precisa de estilo que as props não cobrem, o componente precisa de uma nova prop — não de escape hatch

---

## Critérios de aceite

- [ ] Card com header (title + subtitle + icon) renderizado no Figma com tokens
- [ ] 4 estados de status (success, warning, danger, info) com borda superior
- [ ] Collapse funcional com chevron ▶/▼ e `aria-expanded`
- [ ] Footer com botões alinhados à direita (regra BC-05)
- [ ] Heading level configurável (h2–h5) no Figma
- [ ] Violações WCAG AA (cor aten · vis aten) resolvidas
- [ ] Violações Nielsen (H-1 · H-4 · H-6 · H-8) resolvidas
- [ ] Composição documentada (com Forms, Tables, Consultas)
- [ ] `customClass` removida — sem escape hatch
- [ ] Revisado e aprovado por Giuliana
