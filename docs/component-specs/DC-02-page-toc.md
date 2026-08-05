---
component-id: DC-02
component-name: Page TOC
type: Doc
status: in-figma
sprint: 6
approved-by: [Giuliana]
approved-date: [2026-07-27]
figma-node-id: [499:17]
---

# Component Spec — Page TOC (Índice da Página)

> **Tipo:** Componente documental (DC) — exclusivo do portal DS, não vira Angular.
> **Auditoria Regra 12:** nenhum dos 37 Component Sets existentes cobre sidebar de âncoras de navegação intra-página. BC-26 Tabs é funcionalidade similar (navegação por seção), mas Tabs troca conteúdo visível — TOC rola a página para a seção. Padrão funcional diferente.

---

## O que é

Page TOC é o índice lateral fixo das páginas de documentação do portal DS. Exibe "NESTA PÁGINA" como heading e lista as seções da página como âncoras de navegação (scroll to section). O item ativo é destacado com borda esquerda vermelha. Aparece na sidebar esquerda de todas as páginas WF-02 (documentação de componente).

---

## Audiência de uso

- **Devs CiASC / terceiros:** usam o TOC para pular diretamente para a seção que precisam (ex: "Propriedades", "Exemplo Angular", "Acessibilidade") sem fazer scroll manual em páginas longas
- **Demilis (mantenedor):** valida estrutura da documentação — TOC reflete as seções obrigatórias de cada componente

---

## Props / API

> Componente exclusivo do Figma — sem API Angular.

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `items` | `string[]` | sim | — | Lista de seções da página (ex: ["Visão geral", "Propriedades", "Estados", ...]) |
| `activeIndex` | `number` | não | `0` | Índice do item ativo (destacado com borda) |

---

## Anatomia do componente

```
┌─────────────────────┐
│  NESTA PÁGINA       │
│                     │
│  ▌ Visão geral      │  ← ativo (borda esquerda vermelha)
│    Propriedades     │
│    Estados          │
│    Exemplo Angular  │
│    Acessibilidade   │
│    Tokens           │
└─────────────────────┘
```

- **Heading:** "NESTA PÁGINA" em Label/SM, fill=text/secondary. Uppercase é styling do text style (já definido em Label)
- **Item ativo:** Body/SM Bold, fill=text/primary. Borda esquerda 3px, fill=primary/base
- **Items inativos:** Body/SM Regular, fill=text/secondary

---

## Estrutura no Figma

| Propriedade | Valor | Token / Variável |
|---|---|---|
| Tipo | Component Set | — |
| Auto-layout | VERTICAL | — |
| Width | 200px FIXED | — |
| Gap (heading → items) | 16px | `space/4` (VariableID:106:56) |
| Gap (entre items) | 0px | — |
| Padding | 0 | — |
| Resize | FIXED × HUG | — |

### Heading

| Propriedade | Valor | Token / Variável |
|---|---|---|
| Text | "NESTA PÁGINA" | — |
| Text Style | Label/SM | — |
| Fill | text/secondary | VariableID:106:84 |

### Container de items

| Propriedade | Valor | Token / Variável |
|---|---|---|
| Auto-layout | VERTICAL | — |
| Gap | 0px | — |

### Item (ativo)

| Propriedade | Valor | Token / Variável |
|---|---|---|
| Auto-layout | HORIZONTAL | — |
| Padding left | 12px | `space/3` (VariableID:106:55) |
| Padding vertical | 8px | `space/2` (VariableID:106:54) |
| Text Style | Body/SM Bold | — |
| Text fill | text/primary | VariableID:106:83 |
| Borda esquerda | 3px, fill=primary/base | VariableID:106:76 |
| Width | FILL (200px) | — |

### Item (inativo)

| Propriedade | Valor | Token / Variável |
|---|---|---|
| Auto-layout | HORIZONTAL | — |
| Padding left | 12px | `space/3` (VariableID:106:55) |
| Padding vertical | 8px | `space/2` (VariableID:106:54) |
| Text Style | Body/SM Regular | — |
| Text fill | text/secondary | VariableID:106:84 |
| Borda esquerda | nenhuma | — |
| Width | FILL (200px) | — |

---

## Estados e variantes

| Variante | Descrição |
|---|---|
| **Default** | TOC com heading + N items (1 ativo, N-1 inativos) |

> 1 variante apenas. Item ativo e conteúdo dos items são overrides na instância.

---

## Composição atômica

> **Regra 11:** "este elemento já existe como componente?"

| Elemento | Existe como componente? | Decisão |
|---|---|---|
| Heading "NESTA PÁGINA" | Não — text node com Label/SM | Frame manual |
| Item de navegação | BC-26 Tab Item? | **Não usar.** Tab Item tem estados Hover/Active/Inactive visuais (underline/contained) que não se aplicam aqui. TOC item é texto com borda esquerda condicional — padrão visual diferente |
| Borda esquerda ativa | Não — stroke lateral 3px | Frame manual |

**Resultado:** DC-02 Page TOC é um componente folha. A semelhança com Tab Item é funcional (navegação), mas visual e estruturalmente são componentes distintos.

---

## Regras de acessibilidade (proativas)

- [ ] Container com `role="navigation"` e `aria-label="Índice da página"`
- [ ] Lista semântica: `<nav>` > `<ul>` > `<li>` > `<a>`
- [ ] Item ativo com `aria-current="true"`
- [ ] Links com foco visível: `2px solid var(--color-border-focus)`
- [ ] Contraste verificado:
  - text/secondary (#4B5563) sobre surface/bg (#FFFFFF): ≥ 7.2:1 ✅ AAA
  - text/primary (#08060F) sobre surface/bg (#FFFFFF): ≥ 19:1 ✅ AAA
  - primary/base (#C4000B) como borda: decorativo reforçando texto bold ✅
- [ ] Labels em português

---

## Comportamento responsivo

> Regra 13 — Responsividade obrigatória.

| Precisa de variante `Layout=Mobile`? | **Não** — sidebar inteira é oculta no mobile. Conteúdo principal vira full-width |
|---|---|
| **Desktop** | Sidebar fixa 200px à esquerda do conteúdo |
| **Mobile** | TOC não é renderizado. Conteúdo ocupa 100% da largura |
| **Tablet** | Segue Desktop |

> A decisão de ocultar o TOC no mobile é do layout (WF-02 Mobile), não do componente. O componente em si não precisa de variante responsiva.

---

## Casos excepcionais / bordas

- **Página com muitas seções (> 10):** TOC pode ficar mais alto que o viewport. Comportamento: sticky com scroll interno (`overflow-y: auto`, `max-height: calc(100vh - header)`)
- **Seção sem conteúdo:** item aparece normalmente — TOC reflete a estrutura da página, não o volume de conteúdo
- **Scroll-spy:** no portal implementado, o item ativo muda conforme o usuário rola a página. No Figma, apenas 1 item ativo (estático)

---

## O que está fora deste spec

- **Comportamento sticky:** responsabilidade do layout, não do componente
- **Scroll-spy (highlight automático):** lógica de scroll, fora do escopo Figma
- **Colapsável (toggle):** complexidade desnecessária — sidebar simplesmente não aparece no mobile
- **Versão Angular:** DC components são exclusivos do portal

---

## Critérios de aceite

- [ ] Component Set no Figma com 1 variante (Default)
- [ ] Width 200px FIXED, auto-layout vertical
- [ ] Heading "NESTA PÁGINA" com Label/SM, fill=text/secondary
- [ ] Item ativo com Body/SM Bold, fill=text/primary, borda esquerda 3px primary/base
- [ ] Items inativos com Body/SM Regular, fill=text/secondary
- [ ] Gaps e paddings bound a variáveis de spacing
- [ ] Fills bound a variáveis de Colors
- [ ] Zero valores hardcoded (Regra 8)
- [ ] 6 items de exemplo: "Visão geral", "Propriedades", "Estados", "Exemplo Angular", "Acessibilidade", "Tokens"
- [ ] Revisado e aprovado por Giuliana
