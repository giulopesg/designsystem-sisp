---
component-id: SC-22
component-name: Barra de Acessibilidade
type: SISP
status: in-figma
sprint: 8
approved-by: Giuliana
approved-date: 2026-08-17
figma-node-id: 1031:10078
---

# Component Spec — Barra de Acessibilidade

> **Tipo:** Componente SISP — reutilizável em qualquer produto gov.br/SC.
> **Referências:**
> - Session log: D194
> - Formalizado de frame manual dentro do layout DV-Home-Desktop para Component Set na página SISP Components
> - Padrão de acessibilidade gov.br — barra de controles no topo de todas as páginas

---

## O que é

Barra horizontal fixa no topo de páginas de produtos governamentais SISP. Contém links de acessibilidade ("Acessibilidade", "Ir para o conteúdo") e controles de ajuste visual (tamanho de fonte A-/A/A+, alto contraste, Libras). Fundo escuro (`dark/surface`) com texto claro, alinhado ao padrão gov.br.

---

## Audiência de uso

- **Devs CiASC / terceiros:** implementam barra de acessibilidade obrigatória em todos os produtos SISP
- **Usuários finais:** acessam controles de acessibilidade no topo da página
- **POs:** compliance com padrões gov.br de acessibilidade

---

## Props / API

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `a11yLabel` | `string` | Não | `'Acessibilidade'` | Texto do link de acessibilidade |
| `skipToContentLabel` | `string` | Não | `'Ir para o conteúdo'` | Texto do link skip-to-content |
| `highContrastLabel` | `string` | Não | `'Alto contraste'` | Texto do controle de alto contraste |
| `librasLabel` | `string` | Não | `'Libras'` | Texto do controle de Libras |

---

## Estados e variantes

| Variante | Descrição visual | Dimensões |
|---|---|---|
| Layout=Desktop | Barra horizontal: links à esquerda + controles à direita | 1440×34px |

---

## Anatomia

```
┌──────────────────────────────────────────────────────────────┐
│  Acessibilidade  Ir para o conteúdo       A-  A  A+ | Alto contraste | Libras  │
└──────────────────────────────────────────────────────────────┘
  1440 × 34px, padding 8px vertical × 48px horizontal
```

| Elemento | Detalhe |
|---|---|
| A11y-Left | Frame horizontal, gap 12px. "Acessibilidade" + "Ir para o conteúdo" |
| A11y-Controls | Frame horizontal, gap 12px. "A-" + "A" + "A+" + "\|" + "Alto contraste" + "\|" + "Libras" |
| Todos os textos | Body/XS Regular (Arial 12px), fill `dark/text` |
| Background | `dark/surface` |

---

## Tokens e cores

| Elemento | Token | Variável Figma |
|---|---|---|
| Background | `dark/surface` | VariableID:106:103 |
| Texto (todos) | `dark/text` | VariableID:106:104 |
| Padding vertical | 8px (space/2) | VariableID:106:56 |
| Padding horizontal | 48px (space/12) | VariableID:106:63 |
| Gap interno (A11y-Left, A11y-Controls) | 12px (space/3) | VariableID:106:57 |

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução proposta |
|---|---|---|
| Contraste (wcag) | N/A — componente novo | Texto claro sobre dark/surface: 12.8:1 ✅ |
| Uso de cor (cor) | N/A | Links textuais — cor não é o único canal |
| Skip link (wcag) | "Ir para o conteúdo" é skip-to-content link obrigatório WCAG 2.4.1 | Implementado ✅ |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução proposta |
|---|---|---|
| H-4 Consistência | Barra de acessibilidade é padrão gov.br | Posição e controles seguem o padrão governamental |
| H-7 Flexibilidade | Usuários com necessidades visuais | Controles A-/A/A+, alto contraste e Libras |

---

## Regras de acessibilidade

- [x] Contraste mínimo 4.5:1 — texto claro sobre dark/surface: 12.8:1
- [x] Skip-to-content link presente (WCAG 2.4.1)
- [x] Navegável por teclado — links e controles tabuláveis
- [x] ARIA: `role="complementary"` ou `<aside aria-label="Controles de acessibilidade">`
- [x] Controles de fonte com `aria-label` descritivo
- [x] Label em português

---

## Comportamentos esperados

- "Ir para o conteúdo" faz scroll para o `<main>` da página (skip link)
- A-/A/A+ ajustam o `font-size` do `<body>` (via JavaScript)
- "Alto contraste" alterna classe CSS de alto contraste
- "Libras" abre widget de tradução em Libras (VLibras gov.br)
- Barra é fixa no topo da viewport, acima do Identity Bar

---

## Comportamento responsivo

| Precisa de variante `Layout=Mobile`? | Candidato futuro — barra é compacta (34px) mas pode precisar de reorganização em <375px |
|---|---|
| **Desktop** | Horizontal: links à esquerda, controles à direita (1440×34) |
| **Mobile** | Segue Desktop — 34px é viável em telas pequenas |
| **Tablet** | Segue Desktop |

---

## Composição atômica

Sem instâncias de BC — barra é composta por text nodes e frames simples.

---

## Casos excepcionais / bordas

- VLibras indisponível: link "Libras" permanece visível mas sem ação (graceful degradation)
- Alto contraste: troca de tema CSS, não de variáveis Figma
- A-/A/A+ limites: min 12px, max 24px (3 incrementos)

---

## O que está fora deste spec

- Lógica JavaScript dos controles de fonte
- Integração com VLibras
- Tema de alto contraste (é responsabilidade do sistema de temas)

---

## Critérios de aceite

- [x] Component Set no Figma com 1 variante (Layout=Desktop)
- [x] Fill bound a `dark/surface`
- [x] Text style aplicado: Body/XS Regular
- [x] Text fills bound a `dark/text`
- [x] Padding bound a variáveis (space/2 vertical, space/12 horizontal)
- [x] Gap bound a space/3
- [x] Label em português
- [x] Skip-to-content link presente
- [x] Revisado e aprovado por Giuliana
