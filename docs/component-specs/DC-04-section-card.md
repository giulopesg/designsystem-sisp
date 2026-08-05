---
component-id: DC-04
component-name: Section Card
type: Doc
status: in-figma
sprint: 6
approved-by: [Giuliana]
approved-date: [2026-07-27]
figma-node-id: [519:227]
---

# Component Spec — Section Card

> **Tipo:** Componente documental (DC) — exclusivo do portal DS, não vira Angular.
> **Auditoria Regra 12:** nenhum dos 40 Component Sets existentes cobre card de navegação para seções do portal. BC-06 Cards tem padrão funcional diferente (card DV com status/ações).

---

## O que é

Section Card é um card de navegação que representa uma seção do portal DS (ex: "Sobre o DS", "Fundação", "Acessibilidade"). Usado na home (WF-01) para listar as 7 seções do Design System. Contém ícone, label de seção numerada, título e descrição curta.

---

## Audiência de uso

- **Devs CiASC / terceiros:** usam a home do portal para navegar até a seção relevante. Section Card funciona como atalho visual
- **POs (Sommer/Holiwod):** visão panorâmica das seções do DS ao abrir o portal

---

## Props / API

> Componente exclusivo do Figma — sem API Angular.

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `icon` | `Icon` | sim | — | Ícone representativo da seção (instância BC-15 Icons LG) |
| `sectionLabel` | `string` | sim | — | Label numerada (ex: "Seção 01") |
| `title` | `string` | sim | — | Nome da seção (ex: "Sobre o DS") |
| `description` | `string` | sim | — | Descrição curta da seção (1-2 linhas) |

---

## Anatomia do componente

```
┌─────────────────────────────────────────┐
│  🔲 (BC-15 Icons LG)                   │
│  Seção 01                               │
│  Sobre o DS                             │
│  O que é, quem mantém, como usar,       │
│  como contribuir, changelog             │
└─────────────────────────────────────────┘
```

- **Ícone:** instância BC-15 Icons LG (Regra 11 — composição atômica)
- **Section label:** Label/SM, fill=text/muted ("Seção 01")
- **Título:** Body/LG Bold, fill=text/primary ("Sobre o DS")
- **Descrição:** Body/SM Regular, fill=text/secondary (1-2 linhas)

---

## Estrutura no Figma

| Propriedade | Valor | Token / Variável |
|---|---|---|
| Tipo | Component Set | — |
| Auto-layout | VERTICAL | — |
| Padding | 16px | `space/4` (VariableID:106:58) |
| Gap | 12px | `space/3` (VariableID:106:57) |
| Border radius | 8px | `radius/lg` (VariableID:106:71) |
| Fill | #FFFFFF | `surface/bg` (VariableID:106:87) |
| Stroke | #E5E7EB, 1px, INSIDE | `border/base` (VariableID:106:92) |
| Width | FILL (herda do grid container) | — |
| Height | HUG | — |

### Elementos internos

| Elemento | Tipo | Text Style | Fill | Variável |
|---|---|---|---|---|
| Ícone | Instância BC-15 Icons LG | — | text/secondary | VariableID:106:84 |
| Section label | Text | Label/SM | text/muted | VariableID:106:85 |
| Título | Text | Body/LG Bold | text/primary | VariableID:106:83 |
| Descrição | Text | Body/SM Regular | text/secondary | VariableID:106:84 |

---

## Estados e variantes

| Variante | Descrição |
|---|---|
| **Default** | Card com ícone + label + título + descrição |

> 1 variante apenas. Conteúdo é override de texto/ícone na instância.

---

## Composição atômica

> **Regra 11:** "este elemento já existe como componente?"

| Elemento | Existe como componente? | Decisão |
|---|---|---|
| Ícone | Sim — BC-15 Icons (223:516) | **Instância** BC-15 Icons LG |
| Section label | Não — texto primitivo | Frame manual (text node) |
| Título | Não — texto primitivo | Frame manual (text node) |
| Descrição | Não — texto primitivo | Frame manual (text node) |

**Resultado:** DC-04 contém 1 instância de componente existente (BC-15 Icons LG).

---

## Regras de acessibilidade (proativas)

- [ ] Container com `role="link"` ou `<a>` — card inteiro é clicável
- [ ] `aria-label` descritivo: "Seção 01: Sobre o DS"
- [ ] Contraste verificado:
  - text/primary (#08060F) sobre surface/bg (#FFFFFF): ≥ 19:1 ✅ AAA
  - text/secondary (#4B5563) sobre surface/bg (#FFFFFF): ≥ 7.5:1 ✅ AAA
  - text/muted (#9CA3AF) sobre surface/bg (#FFFFFF): ≥ 2.8:1 — label "Seção XX" é complementar ao título, não informação única ✅
- [ ] Foco visível via ring `var(--color-border-focus)`
- [ ] Navegável por teclado (Tab + Enter)
- [ ] Labels em português

---

## Comportamento responsivo

> Regra 13 — Responsividade obrigatória.

| Precisa de variante `Layout=Mobile`? | **Não** — width FILL, auto-contido, se adapta ao container |
|---|---|
| **Desktop** | Grid 4 colunas (row 1) + 3 colunas (row 2) |
| **Mobile** | Stack 1 coluna — card ocupa 100% da largura |
| **Tablet** | Segue Desktop (grid se adapta) |

> A responsividade é tratada no grid container do layout, não no componente em si.

---

## Casos excepcionais / bordas

- **Descrição muito longa:** text wrap automático via auto-layout, card cresce verticalmente (HUG)
- **Sem ícone:** improvável — cada seção tem ícone representativo. Se necessário, ocultar o frame do ícone
- **7 seções → N seções:** grid acomoda qualquer quantidade via wrap

---

## O que está fora deste spec

- **Hover state (elevação/shadow):** componente Figma é estático. Hover pode ser documentado para implementação: shadow on hover
- **Versão Angular:** DC components são exclusivos do portal
- **Link/URL:** o destino da navegação é configurado no portal, não no componente Figma

---

## Critérios de aceite

- [ ] Component Set no Figma com 1 variante (Default)
- [ ] Auto-layout vertical, padding=space/4, gap=space/3, radius=radius/lg
- [ ] Fill bound a surface/bg, stroke bound a border/base
- [ ] BC-15 Icons LG como instância (Regra 11)
- [ ] Text styles aplicados: Label/SM, Body/LG Bold, Body/SM Regular
- [ ] Fills bound a variáveis: text/muted, text/primary, text/secondary
- [ ] Zero valores hardcoded (Regra 8)
- [ ] Revisado e aprovado por Giuliana
