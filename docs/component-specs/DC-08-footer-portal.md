---
component-id: DC-08
component-name: Footer Portal
type: Doc
status: in-figma
sprint: 6
approved-by: Giuliana
approved-date: 2026-08-11
figma-node-id: 547:1550
---

# Component Spec — Footer Portal

> **Tipo:** Componente documental (DC) — exclusivo do portal DS, não vira Angular.
> **Referências:**
> - Session log: D130, D131, D149
> - Substituiu BC-12 Footer nos layouts do portal DS
> - Reestruturado D149 para paridade com HTML do portal

---

## O que é

Footer institucional do portal de documentação do DS SISP. Exibe branding do DS, links organizados em 3 colunas temáticas (Documentação, Recursos, Governança), separador e copyright CiASC. Diferencia-se do BC-12 Footer (genérico DV) e do SC-18 Footer DV (específico Delegacia Virtual).

---

## Audiência de uso

- **Devs CiASC / terceiros:** acessam links de documentação, recursos e governança do DS
- **POs (Sommer/Holiwod):** informações institucionais e contato

---

## Props / API

> Componente exclusivo do Figma — sem API Angular.

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `brandTitle` | `string` | Sim | `'DS SISP'` | Título do produto na coluna de branding |
| `brandDescription` | `string` | Sim | — | Descrição curta do DS |
| `docLinks` | `string[]` | Não | `['Sobre o DS', 'Fundação', 'Acessibilidade', 'Componentes Base', 'Componentes SISP']` | Links da coluna Documentação |
| `resourceLinks` | `string[]` | Não | `['Figma UI Kit', 'Tokens CSS', 'Dev Mode', 'Temas', 'Changelog']` | Links da coluna Recursos |
| `govLinks` | `string[]` | Não | `['Como contribuir', 'Guia SC v1.4', 'WCAG 2.1 AA', 'Contato']` | Links da coluna Governança |

---

## Estados e variantes

| Variante | Descrição visual | Dimensões |
|---|---|---|
| Layout=Desktop | 4 colunas: brand + Documentação + Recursos + Governança. Separator + copyright + versão | 1440×363px |
| Layout=Mobile | Colunas empilhadas verticalmente, centralizadas | 375×704px |

---

## Anatomia

```
Desktop:
┌──────────────────────────────────────────────────────────────┐
│                     dark/surface background                    │
│                                                                │
│  ┌──────────┐  ┌──────────────┐  ┌──────────┐  ┌──────────┐  │
│  │ 🔲 DS    │  │ DOCUMENTAÇÃO │  │ RECURSOS │  │GOVERNANÇA│  │
│  │ SISP     │  │  Sobre o DS  │  │ Figma Kit│  │ Contribuir│ │
│  │ descr... │  │  Fundação    │  │ Tokens   │  │ Guia SC  │  │
│  │          │  │  Acessib.    │  │ Dev Mode │  │ WCAG 2.1 │  │
│  │          │  │  Comp. Base  │  │ Temas    │  │ Contato  │  │
│  │          │  │  Comp. SISP  │  │ Changelog│  │          │  │
│  └──────────┘  └──────────────┘  └──────────┘  └──────────┘  │
│                                                                │
│  ───────────────────── separator ────────────────────────────  │
│  © 2026 CiASC — Centro de Informática...         v1.0.0       │
│                                                                │
└──────────────────────────────────────────────────────────────┘
  1440 × 363px (container 1200px centralizado com padding 120px)
```

| Elemento | Detalhe |
|---|---|
| Brand Column | Badge "DS" + "DS SISP" (Heading/MD) + descrição (Body/XS Regular) |
| Column Titles | Label/MD (Montserrat SemiBold), fill `dark/text` |
| Links | Body/SM Regular (Arial 14px), fill `dark/text` |
| Separator | Frame 1200×1px, fill `text/inverse` |
| Copyright (Left) | Body/XS Regular, "© 2026 CiASC — Centro de Informática..." |
| Version (Right) | Label/SM, "v1.0.0" |
| Background | `dark/surface` (VariableID:106:103) |

---

## Tokens e cores

| Elemento | Token | Variável Figma |
|---|---|---|
| Background | `dark/surface` | VariableID:106:32 (primitiva → 106:103 semântica) |
| Texto (todos) | `dark/text` | VariableID:106:104 |
| Separator | `text/inverse` | VariableID:106:86 |
| Padding vertical Desktop | 32px top (space/8), 16px bottom (space/4) | VariableID:106:62, 106:58 |
| Padding horizontal Desktop | 120px (centralização 1200px em 1440px) | Valor estrutural |
| Padding vertical Mobile | 24px top (space/6), 16px bottom (space/4) | VariableID:106:60, 106:58 |
| Padding horizontal Mobile | 16px (space/4) | VariableID:106:58 |
| Gap entre colunas | 48px (space/12) | VariableID:106:63 |
| Gap interno colunas | 8px (space/2) — links | VariableID:106:56 |
| Gap Brand | 12px (space/3) — badge+título+descrição | VariableID:106:57 |
| Gap seções (vertical) | 24px (space/6) | VariableID:106:60 |

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução proposta |
|---|---|---|
| Contraste (wcag) | N/A — componente novo | Texto claro sobre dark/surface: 12.8:1 ✅ |
| Uso de cor (cor) | N/A | Links sem underline em repouso — underline on hover |
| Tipografia (tip) | N/A | Column titles em Label/MD (semibold) — hierarquia clara vs links em Body/SM |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução proposta |
|---|---|---|
| H-4 Consistência | BC-12 Footer (DV) não refletia o sitemap do DS | Footer Portal com 3 colunas do sitemap do DS |
| H-6 Reconhecimento | Sem branding do DS no footer | Coluna brand com badge + título + descrição |
| H-10 Ajuda | Sem links de governança/contribuição | Coluna Governança com "Como contribuir", "Guia SC", "WCAG 2.1 AA", "Contato" |

---

## Regras de acessibilidade

- [x] Contraste mínimo 4.5:1 — texto claro sobre verde escuro: 12.8:1
- [x] Navegável por teclado — links tabuláveis
- [x] ARIA: `<footer role="contentinfo">`
- [x] Landmark semântico para screen readers
- [x] Links descritivos (não "clique aqui")
- [x] Label em português

---

## Comportamentos esperados

- Footer é estático — sem interação além de links
- Links abrem na mesma aba (páginas internas do portal)
- Versão exibida como texto estático (atualizada por release)
- Copyright com ano atualizado

---

## Comportamento responsivo

| Precisa de variante `Layout=Mobile`? | Sim — já implementado |
|---|---|
| **Desktop** | 4 colunas horizontais, container 1200px centralizado em 1440px |
| **Mobile** | Colunas empilhadas verticalmente (375px), padding 16px |
| **Tablet** | Segue Desktop |

---

## Composição atômica

Sem instâncias de BC — footer é composto por text nodes e frames simples. Badge "DS" é frame manual com texto.

---

## Diferença dos outros footers

| Aspecto | BC-12 Footer | SC-18 Footer DV | DC-08 Footer Portal |
|---|---|---|---|
| Contexto | Genérico — qualquer produto SISP | Delegacia Virtual | Portal DS |
| Colunas | Links genéricos | Sobre + Emergência + Ajuda | Documentação + Recursos + Governança |
| Branding | Sem | Logo PC + "Delegacia Virtual" | Badge DS + "DS SISP" |
| Vira Angular | Sim | Sim | Não |

---

## Casos excepcionais / bordas

- Links dinâmicos via CMS: não previsto — hardcoded no portal
- Badge "DS" customizável: não — fixo como representação do DS SISP
- Mais de 5 links por coluna: coluna cresce verticalmente (auto-layout)
- Copyright com ano dinâmico: atualizado manualmente por release

---

## O que está fora deste spec

- Newsletter/inscrição no footer
- Ícones de redes sociais
- Versão Angular — DC components são exclusivos do portal
- Links externos (todos são internos ao portal)

---

## Critérios de aceite

- [x] Component Set no Figma com 2 variantes (Desktop, Mobile)
- [x] Fill bound a `dark/surface`
- [x] Text styles aplicados: Heading/MD (título), Label/MD (column titles), Body/SM (links), Body/XS (descrição/copyright), Label/SM (versão)
- [x] Text fills bound a `dark/text`
- [x] Separator bound a `text/inverse`
- [x] 4 colunas Desktop com gap space/12
- [x] Container 1200px centralizado (padding 120px lateral)
- [x] Label em português
- [ ] Exemplo Angular documentado (N/A — DC component)
- [x] Revisado e aprovado por Giuliana (D130, D149)
