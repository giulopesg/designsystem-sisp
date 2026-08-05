---
component-id: DC-05
component-name: Persona Card
type: Doc
status: in-figma
sprint: 6
approved-by: [Giuliana]
approved-date: [2026-07-27]
figma-node-id: [520:229]
---

# Component Spec — Persona Card

> **Tipo:** Componente documental (DC) — exclusivo do portal DS, não vira Angular.
> **Auditoria Regra 12:** nenhum dos 40 Component Sets existentes cobre card de pathway por persona. BC-06 Cards é card DV com status/ações — padrão funcional diferente.

---

## O que é

Persona Card é um card de pathway que guia o usuário conforme seu perfil. Usado na home (WF-01) na seção "Por onde começar?" para direcionar cada tipo de persona ao caminho mais relevante dentro do portal DS. Contém ícone circular, nome da persona, descrição do contexto e caminho sugerido.

---

## Audiência de uso

- **Devs CiASC:** "Sou dev CiASC" → caminho otimizado para quem já conhece o UI Kit
- **Terceiros contratados:** "Sou terceiro" → caminho para quem não tem contexto histórico
- **POs / gestores:** "Quero visibilidade" → caminho para validação e governança

---

## Props / API

> Componente exclusivo do Figma — sem API Angular.

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `icon` | `Icon` | sim | — | Ícone representativo da persona (instância BC-15 Icons LG) |
| `title` | `string` | sim | — | Nome da persona (ex: "Sou dev CiASC") |
| `description` | `string` | sim | — | Contexto da persona (ex: "Já uso o UI Kit e preciso de referência rápida") |
| `path` | `string` | sim | — | Caminho sugerido (ex: "Componentes Base → Componentes SISP → Fundação") |

---

## Anatomia do componente

```
┌─────────────────────────────────────────┐
│  ⬤ (ícone circular 40×40)              │
│  Sou dev CiASC                          │
│  Já uso o UI Kit e preciso de           │
│  referência rápida para props,          │
│  estados e tokens.                      │
│  Componentes Base → SISP → Fundação     │
└─────────────────────────────────────────┘
```

- **Ícone:** frame 40×40 com instância BC-15 Icons LG centralizada + ellipse de fundo (Regra 11 — composição atômica)
- **Título:** Body/LG Bold, fill=text/primary ("Sou dev CiASC")
- **Descrição:** Body/SM Regular, fill=text/secondary (2-3 linhas)
- **Caminho:** Label/SM, fill=text/muted ("Componentes Base → Componentes SISP → Fundação")

---

## Estrutura no Figma

| Propriedade | Valor | Token / Variável |
|---|---|---|
| Tipo | Component Set | — |
| Auto-layout | VERTICAL | — |
| Padding | 24px | `space/6` (VariableID:106:60) |
| Gap | 12px | `space/3` (VariableID:106:57) |
| Border radius | 8px | `radius/lg` (VariableID:106:71) |
| Fill | #FFFFFF | `surface/bg` (VariableID:106:87) |
| Stroke | #E5E7EB, 1px, INSIDE | `border/base` (VariableID:106:92) |
| Width | FILL (herda do grid container) | — |
| Height | HUG | — |

### Frame do ícone

| Propriedade | Valor | Token / Variável |
|---|---|---|
| Tamanho | 40×40 FIXED | — |
| Border radius | 20px (circular) | `radius/full` (VariableID:106:72) |
| Fill | #F9FAFB | `surface/bg-subtle` (VariableID:106:88) |
| Conteúdo | Instância BC-15 Icons LG centralizada | — |

### Elementos internos

| Elemento | Tipo | Text Style | Fill | Variável |
|---|---|---|---|---|
| Frame ícone | Frame 40×40 | — | surface/bg-subtle | VariableID:106:88 |
| Ícone dentro | Instância BC-15 Icons LG | — | text/secondary | VariableID:106:84 |
| Título | Text | Body/LG Bold | text/primary | VariableID:106:83 |
| Descrição | Text | Body/SM Regular | text/secondary | VariableID:106:84 |
| Caminho | Text | Label/SM | text/muted | VariableID:106:85 |

---

## Estados e variantes

| Variante | Descrição |
|---|---|
| **Default** | Card com ícone + título + descrição + caminho |

> 1 variante apenas. Conteúdo é override de texto/ícone na instância.

---

## Composição atômica

> **Regra 11:** "este elemento já existe como componente?"

| Elemento | Existe como componente? | Decisão |
|---|---|---|
| Ícone | Sim — BC-15 Icons (223:516) | **Instância** BC-15 Icons LG |
| Frame circular | Não — container de layout | Frame manual 40×40 |
| Título | Não — texto primitivo | Frame manual (text node) |
| Descrição | Não — texto primitivo | Frame manual (text node) |
| Caminho | Não — texto primitivo | Frame manual (text node) |

**Resultado:** DC-05 contém 1 instância de componente existente (BC-15 Icons LG dentro do frame circular).

---

## Regras de acessibilidade (proativas)

- [ ] Container com `role="link"` ou `<a>` — card inteiro é clicável
- [ ] `aria-label` descritivo: "Sou dev CiASC: Componentes Base, Componentes SISP, Fundação"
- [ ] Contraste verificado:
  - text/primary (#08060F) sobre surface/bg (#FFFFFF): ≥ 19:1 ✅ AAA
  - text/secondary (#4B5563) sobre surface/bg (#FFFFFF): ≥ 7.5:1 ✅ AAA
  - text/muted (#9CA3AF) sobre surface/bg (#FFFFFF): ≥ 2.8:1 — caminho é complementar, não informação única ✅
- [ ] Foco visível via ring `var(--color-border-focus)`
- [ ] Navegável por teclado (Tab + Enter)
- [ ] Labels em português

---

## Comportamento responsivo

> Regra 13 — Responsividade obrigatória.

| Precisa de variante `Layout=Mobile`? | **Não** — width FILL, auto-contido, se adapta ao container |
|---|---|
| **Desktop** | Grid 3 colunas (3 persona cards lado a lado) |
| **Mobile** | Stack 1 coluna — card ocupa 100% da largura |
| **Tablet** | Segue Desktop |

> A responsividade é tratada no grid container do layout, não no componente em si.

---

## Casos excepcionais / bordas

- **Descrição muito longa:** text wrap automático via auto-layout, card cresce verticalmente (HUG)
- **Caminho muito longo:** text wrap, mas improvável (máximo 3-4 itens separados por →)
- **Mais de 3 personas:** grid acomoda — mas no portal DS são 3 personas definidas no PRD

---

## O que está fora deste spec

- **Hover state (elevação/shadow):** componente Figma é estático. Hover documentado para implementação: shadow + translateY(-2px) on hover
- **Versão Angular:** DC components são exclusivos do portal
- **Avatar/foto da persona:** ícone genérico é suficiente — personas são perfis funcionais, não indivíduos

---

## Critérios de aceite

- [ ] Component Set no Figma com 1 variante (Default)
- [ ] Auto-layout vertical, padding=space/6, gap=space/3, radius=radius/lg
- [ ] Fill bound a surface/bg, stroke bound a border/base
- [ ] Frame ícone 40×40 com radius/full, fill=surface/bg-subtle
- [ ] BC-15 Icons LG como instância dentro do frame ícone (Regra 11)
- [ ] Text styles aplicados: Body/LG Bold, Body/SM Regular, Label/SM
- [ ] Fills bound a variáveis: text/primary, text/secondary, text/muted
- [ ] Zero valores hardcoded (Regra 8)
- [ ] Revisado e aprovado por Giuliana
