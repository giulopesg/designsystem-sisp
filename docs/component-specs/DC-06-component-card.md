---
component-id: DC-06
component-name: Component Card
type: Doc
status: in-figma
sprint: 6
approved-by: [Giuliana]
approved-date: [2026-07-27]
figma-node-id: [520:2154]
---

# Component Spec — Component Card

> **Tipo:** Componente documental (DC) — exclusivo do portal DS, não vira Angular.
> **Auditoria Regra 12:** nenhum dos 40 Component Sets existentes cobre card de preview de componente. BC-06 Cards é card DV com status/ações — padrão funcional diferente. DC-06 é um card de catálogo para listar componentes no portal.

---

## O que é

Component Card é um card de preview que representa um componente do DS. Usado na home (WF-01) na seção "Componentes mais usados" e reutilizável em páginas de listagem de componentes. Contém área de preview visual, nome do componente e badge indicando o tipo (Base Component / SISP Component).

---

## Audiência de uso

- **Devs CiASC / terceiros:** exploram o catálogo de componentes. Component Card funciona como thumbnail clicável que leva à página de documentação (WF-02) do componente
- **POs (Sommer/Holiwod):** visão panorâmica dos componentes disponíveis

---

## Props / API

> Componente exclusivo do Figma — sem API Angular.

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `preview` | `Frame` | sim | — | Área de preview com miniatura visual do componente |
| `name` | `string` | sim | — | Nome do componente (ex: "Button") |
| `typeBadge` | `Badge` | sim | — | Instância BC-04 Badge indicando tipo ("Base Component" ou "SISP Component") |

---

## Anatomia do componente

```
┌─────────────────────────────────────────┐
│  ┌─────────────────────────────────┐    │
│  │                                 │    │
│  │     (preview area — bg sutil)   │    │
│  │                                 │    │
│  └─────────────────────────────────┘    │
│  Button                                 │
│  ┌──────────────┐                       │
│  │Base Component│                       │
│  └──────────────┘                       │
└─────────────────────────────────────────┘
```

- **Preview area:** frame FILL×80, fill=surface/bg-subtle, border radius top-only (clip content), contém miniatura visual do componente
- **Nome:** Body/SM Bold, fill=text/primary ("Button")
- **Badge:** instância BC-04 Badge Neutral Subtle SM (Regra 11 — composição atômica)

---

## Estrutura no Figma

| Propriedade | Valor | Token / Variável |
|---|---|---|
| Tipo | Component Set | — |
| Auto-layout | VERTICAL | — |
| Gap | 8px | `space/2` (VariableID:106:56) |
| Border radius | 8px | `radius/lg` (VariableID:106:71) |
| Fill | #FFFFFF | `surface/bg` (VariableID:106:87) |
| Stroke | #E5E7EB, 1px, INSIDE | `border/base` (VariableID:106:92) |
| Width | FILL (herda do grid container) | — |
| Height | HUG | — |
| Clip content | true | — |

### Preview area

| Propriedade | Valor | Token / Variável |
|---|---|---|
| Width | FILL | — |
| Height | 80px FIXED | — |
| Fill | #F9FAFB | `surface/bg-subtle` (VariableID:106:88) |
| Border radius | Herda top corners do parent (clip content) | — |
| Alignment | CENTER (both axes) | — |

### Área de texto (abaixo do preview)

| Propriedade | Valor | Token / Variável |
|---|---|---|
| Auto-layout | VERTICAL | — |
| Padding | 0 top, 12px left/right/bottom | `space/3` (VariableID:106:57) |
| Gap | 4px | `space/1` (VariableID:106:55) |

### Elementos internos

| Elemento | Tipo | Text Style | Fill | Variável |
|---|---|---|---|---|
| Preview area | Frame | — | surface/bg-subtle | VariableID:106:88 |
| Nome | Text | Body/SM Bold | text/primary | VariableID:106:83 |
| Badge tipo | Instância BC-04 Badge | — | — | (herda do Badge) |

---

## Estados e variantes

| Variante | Descrição |
|---|---|
| **Default** | Card com preview + nome + badge |

> 1 variante apenas. Preview e texto são override na instância.

---

## Composição atômica

> **Regra 11:** "este elemento já existe como componente?"

| Elemento | Existe como componente? | Decisão |
|---|---|---|
| Badge de tipo | Sim — BC-04 Badge (124:2690) | **Instância** BC-04 Badge Neutral Subtle SM |
| Preview area | Não — container de layout | Frame manual |
| Nome | Não — texto primitivo | Frame manual (text node) |

**Resultado:** DC-06 contém 1 instância de componente existente (BC-04 Badge Neutral Subtle SM).

---

## Regras de acessibilidade (proativas)

- [ ] Container com `role="link"` ou `<a>` — card inteiro é clicável
- [ ] `aria-label` descritivo: "Componente Button — Base Component"
- [ ] Contraste verificado:
  - text/primary (#08060F) sobre surface/bg (#FFFFFF): ≥ 19:1 ✅ AAA
  - Badge herda contraste do BC-04 (já validado WCAG AA)
- [ ] Preview area é decorativa — não requer alt text (miniatura visual do componente)
- [ ] Foco visível via ring `var(--color-border-focus)`
- [ ] Navegável por teclado (Tab + Enter)
- [ ] Labels em português

---

## Comportamento responsivo

> Regra 13 — Responsividade obrigatória.

| Precisa de variante `Layout=Mobile`? | **Não** — width FILL, auto-contido, se adapta ao container |
|---|---|
| **Desktop** | Grid 6 colunas (6 cards lado a lado na home) |
| **Mobile** | Grid 2 colunas (2×3 — cards menores mas ainda legíveis) |
| **Tablet** | Segue Desktop |

> A responsividade é tratada no grid container do layout, não no componente em si.

---

## Casos excepcionais / bordas

- **Nome muito longo:** text wrap (ex: "Navigation Canvas" cabe em 1 linha nos cards desktop, pode quebrar em 2 no mobile grid 2×3)
- **Preview vazia:** improvável — cada componente tem representação visual. Se necessário, manter área com fill surface/bg-subtle
- **Badge SISP vs Base:** override de texto na instância do Badge ("SISP Component" para SC-XX)

---

## O que está fora deste spec

- **Hover state (elevação/shadow):** componente Figma é estático. Hover documentado para implementação: shadow + scale(1.02) on hover
- **Versão Angular:** DC components são exclusivos do portal
- **Preview interativa:** no Figma o preview é estático (frame com miniatura). No portal pode ser interativo
- **Contagem de variantes:** informação adicional que pode ser adicionada futuramente como label

---

## Critérios de aceite

- [ ] Component Set no Figma com 1 variante (Default)
- [ ] Auto-layout vertical, gap=space/2, radius=radius/lg, clip content=true
- [ ] Fill bound a surface/bg, stroke bound a border/base
- [ ] Preview area: FILL×80, fill=surface/bg-subtle
- [ ] BC-04 Badge Neutral Subtle SM como instância (Regra 11)
- [ ] Text styles aplicados: Body/SM Bold
- [ ] Fills bound a variáveis: text/primary
- [ ] Zero valores hardcoded (Regra 8)
- [ ] Revisado e aprovado por Giuliana
