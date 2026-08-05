---
component-id: BC-04
component-name: Badges
type: Base
status: approved
sprint: 3
approved-by: [Giuliana]
approved-date: [2026-07-13]
figma-node-id: [124:2690]
---

# Component Spec — Badges

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → BC-04 (cor **crit** · wcag aten · tip aten)
> - `docs/analyses/nielsen-analysis.md` → BC-04 (H-1 aten · H-4 aten · H-6 aten · H-8 aten)
> - `docs/analyses/inventory.md` → BC-04

---

## O que é

Badge é o componente de indicação de status e categorização do DS SISP. Exibe rótulos curtos inline — status de registros, contadores, categorias. Na DV, badges aparecem em cabeçalhos de cards, colunas de tabelas e linhas de resumo. Atualmente 100% Bootstrap, com 8 variantes diferenciadas apenas por cor — sem guia semântico, sem escala de tamanhos.

---

## Audiência de uso

- **Policial na DV:** vê badges de status do BO ("Em andamento", "Finalizado", "Cancelado"), contadores de notificação, categorias de ocorrência
- **Devs CiASC / terceiros:** usam badges para indicar status em tabelas, cards e listagens
- **POs (Sommer/Holiwod):** precisam de diferenciação visual clara de status sem depender de memorização de cores

---

## Tipos semânticos — 5 níveis

> **Decisão de design:** substituir as 8 variantes Bootstrap por 5 tipos semânticos alinhados ao sistema de status do DS (mesmos tokens de Cards, Alerts, Toasts). O componente Angular mantém retrocompatibilidade com `SispLibStyleType`.

| Tipo | Quando usar | Exemplos na DV |
|---|---|---|
| **Neutral** | Categorias, rótulos sem carga semântica | "Furto", "Roubo", tipo de documento |
| **Success** | Status positivo, concluído, ativo | "Finalizado", "Ativo", "Validado" |
| **Warning** | Pendência, atenção necessária | "Em andamento", "Pendente", "Aguardando" |
| **Danger** | Erro, expirado, bloqueado | "Cancelado", "Expirado", "Bloqueado" |
| **Info** | Informativo, novo, atualizado | "Novo", "Atualizado", "2 anexos" |

---

## Props / API

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `type` | `'neutral' \| 'success' \| 'warning' \| 'danger' \| 'info'` | não | `'neutral'` | Tipo semântico do badge |
| `label` | `string` | sim | — | Texto do badge. Curto: 1–3 palavras |
| `style` | `'filled' \| 'subtle'` | não | `'subtle'` | Estilo visual — filled (alto contraste) ou subtle (baixa ênfase) |
| `size` | `'sm' \| 'md' \| 'lg'` | não | `'md'` | Tamanho do badge |
| `icon` | `string` | não | — | Classe Font Awesome (ex: `fa-solid fa-circle`). Posiciona à esquerda do label |
| `removable` | `boolean` | não | `false` | Exibe botão × para remover (ex: tags de filtro) |
| `onRemove` | `Function` | não | — | Callback ao clicar × |

**Convenção Angular:**
```html
<sisp-lib-badge [sispLibBadgeConfig]="config"></sisp-lib-badge>
```

**Exemplo de uso:**
```typescript
config: SispLibBadgeConfig = {
  type: 'success',
  label: 'Finalizado',
  style: 'subtle',
  size: 'md'
};
```

---

## Anatomia do badge

```
┌───────────────────────────┐
│  [icon]  Label       [×]  │
└───────────────────────────┘
```

- **Ícone:** opcional, à esquerda do label, mesmo tamanho do texto
- **Label:** texto curto, centralizado vertical e horizontalmente
- **Close (×):** só aparece se `removable = true`
- **Shape:** `border-radius: --radius-full` (pill shape)

---

## Estados e variantes

### Estilos por tipo

#### Subtle (padrão — baixa ênfase)

| Tipo | Fundo | Texto | Tokens |
|---|---|---|---|
| Neutral | Cinza sutil | Cinza escuro | `bg: --color-bg-muted` · `text: --color-text-secondary` |
| Success | Verde sutil | Verde escuro | `bg: --color-success-bg` · `text: --color-success` |
| Warning | Amarelo sutil | Marrom escuro | `bg: --color-warning-bg` · `text: --color-warning` |
| Danger | Vermelho sutil | Vermelho escuro | `bg: --color-danger-bg` · `text: --color-danger` |
| Info | Azul sutil | Azul escuro | `bg: --color-info-bg` · `text: --color-info` |

#### Filled (alto contraste — ênfase forte)

| Tipo | Fundo | Texto | Tokens |
|---|---|---|---|
| Neutral | Cinza escuro | Branco | `bg: --color-text-secondary` · `text: #FFFFFF` |
| Success | Verde escuro | Branco | `bg: --color-success` · `text: #FFFFFF` |
| Warning | Marrom escuro | Branco | `bg: --color-warning` · `text: #FFFFFF` |
| Danger | Vermelho escuro | Branco | `bg: --color-danger` · `text: #FFFFFF` |
| Info | Azul escuro | Branco | `bg: --color-info` · `text: #FFFFFF` |

### Verificação de contraste (WCAG AA)

| Tipo | Estilo | Texto | Fundo | Ratio | Resultado |
|---|---|---|---|---|---|
| Neutral subtle | #4B5563 | #F3F4F6 | ~8.2:1 | ✅ AAA |
| Success subtle | #166534 | #DCFCE7 | ~7.1:1 | ✅ AAA |
| Warning subtle | #92400E | #FEF3C7 | ~6.8:1 | ✅ AAA |
| Danger subtle | #991B1B | #FEE2E2 | ~6.5:1 | ✅ AAA |
| Info subtle | #1E3A8A | #DBEAFE | ~6.2:1 | ✅ AAA |
| Neutral filled | #FFFFFF | #4B5563 | ~6.4:1 | ✅ AAA |
| Success filled | #FFFFFF | #166534 | ~7.1:1 | ✅ AAA |
| Warning filled | #FFFFFF | #92400E | ~6.8:1 | ✅ AAA |
| Danger filled | #FFFFFF | #991B1B | ~6.5:1 | ✅ AAA |
| Info filled | #FFFFFF | #1E3A8A | ~9.3:1 | ✅ AAA |

### Tamanhos

| Tamanho | Altura | Padding horizontal | Font size | Ícone |
|---|---|---|---|---|
| `sm` | 20px | `--space-2` (8px) | `--text-xs` (12px) | 10px |
| `md` | 24px | `--space-3` (12px) | `--text-sm` (14px) | 12px |
| `lg` | 28px | `--space-3` (12px) | `--text-sm` (14px) | 14px |

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução neste spec |
|---|---|---|
| Contraste (wcag aten) | Algumas variantes com fundo colorido não verificadas | Todos os 10 pares (5 tipos × 2 estilos) verificados: mínimo 6.2:1 ✅ AAA |
| Uso de cor (cor **crit**) | 8 variantes diferenciadas exclusivamente por cor | 5 tipos semânticos com: **cor** + **rótulo textual** descritivo + **ícone** opcional. Badge depende do texto para comunicar status, não apenas da cor |
| Visual (vis) | ok | — |
| Tipografia (tip aten) | Tamanhos não documentados | 3 tamanhos definidos (sm/md/lg) com font size e padding documentados |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução neste spec |
|---|---|---|
| H-1 Visibilidade (aten) | Status sem diferenciação clara | 5 tipos com cor + forma consistente. Estilo subtle (padrão) e filled (ênfase). Ícone reforça o status |
| H-4 Consistência (aten) | 8 variantes sem guia semântico | 5 tipos alinhados ao sistema de status do DS inteiro (Cards, Alerts, Toasts). Guia "quando usar" documentado |
| H-6 Reconhecimento (aten) | Usuário precisa memorizar cores | Badge depende do **texto do label** ("Finalizado", "Cancelado") — cor é reforço visual, não canal primário |
| H-8 Estética (aten) | Visual Bootstrap genérico | Pill shape (`radius-full`), escala de tamanhos, dois estilos (subtle/filled), espaçamento por tokens |

---

## Regras de acessibilidade

- [ ] Badge **não é interativo** (não é botão) — exceto quando `removable = true`
- [ ] Texto do label é o canal primário de informação — cor é reforço, não dependência
- [ ] Quando `removable = true`, botão × com `aria-label="Remover [label]"`
- [ ] Focus ring visível no botão × (quando removable): `2px solid var(--color-border-focus)`
- [ ] Contraste mínimo 4.5:1 em todos os pares tipo/estilo — verificado (mínimo 6.2:1)
- [ ] Badge não deve ser o único indicador de uma informação crítica — sempre acompanhado de texto contextual
- [ ] Em tabelas, usar `<span>` com ARIA adequado, não `<div>` block-level

---

## Comportamentos esperados

- Quando `removable = true` e usuário clica × → dispara `onRemove` callback. Badge **não se remove do DOM sozinho**
- Quando `icon` definido → ícone aparece à esquerda do label com gap de `--space-1` (4px)
- Quando `label` é muito longo (> 20 caracteres) → trunca com `text-overflow: ellipsis` + `title` com texto completo. Máximo recomendado: 3 palavras
- Quando badge está dentro de tabela → usar tamanho `sm` para não dominar a célula
- Quando badge está em header de card → usar tamanho `md`

---

## Composição com outros componentes

| Componente | Relação |
|---|---|
| BC-06 Cards | Badge no header do card indicando status do conteúdo |
| BC-25 Tables | Badge em coluna de status — usar tamanho `sm` |
| BC-03 Alerts | Badge pode acompanhar alert como indicador visual inline |
| SC-02/03/04 Consultas | Badge de status em resultados de consulta |
| SC-13 Steppers | Badge de status em cada step ("Completo", "Pendente") |

---

## Mapeamento de retrocompatibilidade

| SispLibStyleType antigo | Mapeamento novo | Nota |
|---|---|---|
| `success` | **Success** | Direto |
| `warning` | **Warning** | Direto |
| `danger` | **Danger** | Direto |
| `info` | **Info** | Direto |
| `primary` | **Neutral** (filled) | Primary em badges não é semântico |
| `secondary` | **Neutral** (subtle) | Mapeado para neutro |
| `dark` | **Neutral** (filled) | Visual escuro vira neutro filled |
| `light` | **Neutral** (subtle) | Light vira neutro subtle |

---

## Casos excepcionais / bordas

- **Badge como contador:** usar tipo Neutral ou Danger (para notificações urgentes) com apenas número como label ("3", "12+")
- **Badge vazio (sem label):** componente não renderiza (validação no Angular)
- **Muitos badges inline:** gap de `--space-2` (8px) entre badges. Wrap em mobile
- **Mobile (< 640px):** manter tamanho `sm` ou `md`. Badge não precisa de target area de toque (não é interativo), exceto quando removable

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-text-secondary` | Texto Neutral subtle, fundo Neutral filled |
| `--color-bg-muted` | Fundo Neutral subtle |
| `--color-success / --color-success-bg` | Texto/fundo Success |
| `--color-warning / --color-warning-bg` | Texto/fundo Warning |
| `--color-danger / --color-danger-bg` | Texto/fundo Danger |
| `--color-info / --color-info-bg` | Texto/fundo Info |
| `--color-border-focus` | Ring de foco no botão × |
| `--radius-full` | Border radius pill (9999px) |
| `--font-body` | Família tipográfica |
| `--text-xs / --text-sm` | Font size por tamanho |
| `--font-semibold` | Peso do label (600) |
| `--space-1 / --space-2 / --space-3` | Gaps e paddings |

---

## O que está fora deste spec

- **Badge com tooltip:** componente separado se necessário (BC-XX Tooltip)
- **Badge com link:** badge não é interativo. Se precisa de clique, usar BC-05 Button (tertiary, sm)
- **Badge numérico com animação de incremento:** comportamento de produto, não do componente
- **Dot badge (sem label, apenas bolinha):** pode ser adicionado como variante futura se surgir necessidade

---

## Critérios de aceite

- [ ] 5 tipos semânticos (Neutral, Success, Warning, Danger, Info) no Figma
- [ ] 2 estilos (Subtle, Filled) para cada tipo
- [ ] 3 tamanhos (SM, MD, LG) documentados
- [ ] Contraste verificado para todos os 10 pares tipo/estilo — mínimo 4.5:1 AA
- [ ] Pill shape (`radius-full`)
- [ ] Violação WCAG AA (cor **crit** · wcag aten · tip aten) resolvida
- [ ] Violações Nielsen (H-1 · H-4 · H-6 · H-8) resolvidas
- [ ] Mapeamento de retrocompatibilidade com `SispLibStyleType` documentado
- [ ] Revisado e aprovado por Giuliana
