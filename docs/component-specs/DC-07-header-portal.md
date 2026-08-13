---
component-id: DC-07
component-name: Header Portal
type: Doc
status: in-figma
sprint: 6
approved-by: Giuliana
approved-date: 2026-08-11
figma-node-id: 547:1305
---

# Component Spec — Header Portal

> **Tipo:** Componente documental (DC) — exclusivo do portal DS, não vira Angular.
> **Referências:**
> - Session log: D130, D131
> - Substituiu BC-14 Headers nos layouts do portal DS
> - Auditoria Regra 12: BC-14 Headers é específico da DV (tabs "Dados Gerais/Pessoas/Objetos/Anexos"). O portal DS precisa de header próprio com navegação do sitemap do DS.

---

## O que é

Header de navegação do portal de documentação do Design System SISP. Barra fixa no topo com título "DS SISP", 6 links de navegação correspondentes às seções do portal, e campo de busca. Diferencia-se do BC-14 Headers (produto DV) e do SC-21 Identity Bar (produto PC).

---

## Audiência de uso

- **Devs CiASC / terceiros:** navegam entre as seções do portal DS (Sobre, Fundação, Acessibilidade, Componentes, Temas, Figma)
- **POs (Sommer/Holiwod):** acessam seções específicas do DS para revisão

---

## Props / API

> Componente exclusivo do Figma — sem API Angular.

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `title` | `string` | Sim | `'DS SISP'` | Título do portal exibido à esquerda |
| `navLinks` | `string[]` | Não | `['Sobre', 'Fundação', 'Acessibilidade', 'Componentes', 'Temas', 'Figma']` | Links de navegação |
| `searchPlaceholder` | `string` | Não | `'Buscar componente...'` | Placeholder do campo de busca |

---

## Estados e variantes

| Variante | Descrição visual | Dimensões |
|---|---|---|
| State=Default (Desktop) | Barra horizontal: título + 6 nav links + campo de busca | 1440×48px |
| State=Mobile | Barra horizontal: título + hamburger ≡ | 375×48px |

---

## Anatomia

```
Desktop:
┌────────────────────────────────────────────────────────────┐
│  DS SISP   Sobre  Fundação  Acessib.  Comp.  Temas  Figma  🔍  │
└────────────────────────────────────────────────────────────┘
  1440 × 48px

Mobile:
┌──────────────────────┐
│  DS SISP           ≡ │
└──────────────────────┘
  375 × 48px
```

| Elemento | Detalhe |
|---|---|
| Title | "DS SISP", Heading/MD (Montserrat Bold), fill `dark/text` (VariableID:106:104) |
| Nav Links (6×) | Body/SM Regular (Arial 14px), fill `dark/text` (VariableID:106:104) |
| Search | Frame 141×32, fill `surface/bg-subtle` (VariableID:106:88), radius `radius/sm` (4px), placeholder Body/XS |
| Background | `dark/surface` (VariableID:106:103) |
| Hamburger (Mobile) | Texto "≡", fill `dark/text` |

---

## Tokens e cores

| Elemento | Token | Variável Figma |
|---|---|---|
| Background | `dark/surface` | VariableID:106:103 |
| Título | `dark/text` | VariableID:106:104 |
| Nav links | `dark/text` | VariableID:106:104 |
| Search fill | `surface/bg-subtle` | VariableID:106:88 |
| Search radius | `radius/sm` (4px) | VariableID:106:68 |
| Padding horizontal Desktop | 24px (space/6) | VariableID:106:60 |
| Padding horizontal Mobile | 16px (space/4) | VariableID:106:58 |
| Gap entre elementos | 24px (space/6) | VariableID:106:60 |

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução proposta |
|---|---|---|
| Contraste (wcag) | N/A — componente novo | Texto claro sobre dark/surface: 12.8:1 ✅ |
| Uso de cor (cor) | N/A | Links sem diferenciação de cor vs título — aceito (mesma hierarquia) |
| Tipografia (tip) | N/A | Heading/MD para título, Body/SM para links — hierarquia clara |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução proposta |
|---|---|---|
| H-4 Consistência | BC-14 Headers usava tabs DV no portal DS | Header Portal com nav links do sitemap do DS |
| H-6 Reconhecimento | "Delegacia Virtual" no portal do DS confundia contexto | "DS SISP" identifica o produto correto |
| H-7 Flexibilidade | Desktop: todos os links visíveis. Mobile: hamburger para espaço limitado | 2 variantes (Desktop/Mobile) |

---

## Regras de acessibilidade

- [x] Contraste mínimo 4.5:1 — texto claro sobre verde escuro: 12.8:1
- [x] Navegável por teclado — links tabuláveis
- [x] ARIA: `<header role="banner">` + `<nav role="navigation" aria-label="Navegação do portal">`
- [x] Search: `<input role="search" aria-label="Buscar componente">`
- [x] Mobile: hamburger com `aria-expanded` + `aria-label="Menu"`
- [x] Label em português

---

## Comportamentos esperados

- Links navegam para seções do portal (mesma página ou páginas internas)
- Search filtra componentes por nome (autocomplete futuro)
- Mobile: hamburger abre drawer/menu com links empilhados verticalmente
- Header é sticky no topo da viewport

---

## Comportamento responsivo

| Precisa de variante `Layout=Mobile`? | Sim — já implementado como State=Mobile |
|---|---|
| **Desktop** | Barra horizontal com título + 6 links + search (1440×48) |
| **Mobile** | Barra horizontal com título + hamburger (375×48) |
| **Tablet** | Segue Desktop |

> Nota: a propriedade usa `State` em vez de `Layout` — convenção do Sprint 6, anterior à padronização `Layout=Desktop/Mobile`.

---

## Composição atômica

Sem instâncias de BC — header é composto por text nodes e frames simples. Campo de busca é frame manual (anterior ao BC-29 Search).

> Candidato a refatoração futura: substituir campo de busca manual por instância de BC-29 Search.

---

## Diferença do BC-14 Headers

| Aspecto | BC-14 Headers | DC-07 Header Portal |
|---|---|---|
| Contexto | Produto DV — navegação por abas | Portal DS — navegação por seções do sitemap |
| Navegação | BC-26 Tabs (Dados Gerais, Pessoas, etc.) | 6 text links (Sobre, Fundação, etc.) |
| Busca | Sem campo de busca | Campo de busca integrado |
| Mobile | Hamburger + tabs em drawer | Hamburger + links em drawer |

---

## Casos excepcionais / bordas

- Mais de 6 links: overflow horizontal ou dropdown "Mais" (não previsto atualmente)
- Search desabilitado: ocultar frame (não há estado disabled no componente)
- Título longo: truncar com ellipsis (max ~120px)

---

## O que está fora deste spec

- Dropdown/megamenu nas seções
- Search com autocomplete/sugestões
- Versão Angular — DC components são exclusivos do portal
- Active state nos links (indicar seção atual)

---

## Critérios de aceite

- [x] Component Set no Figma com 2 variantes (Default Desktop, Mobile)
- [x] Fill bound a `dark/surface`
- [x] Text styles aplicados: Heading/MD (título), Body/SM (links)
- [x] Text fills bound a `dark/text`
- [x] Search fill bound a `surface/bg-subtle`, radius bound a `radius/sm`
- [x] Label em português
- [ ] Exemplo Angular documentado (N/A — DC component)
- [x] Revisado e aprovado por Giuliana (D130)
