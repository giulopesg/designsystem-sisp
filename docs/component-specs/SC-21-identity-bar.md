---
component-id: SC-21
component-name: Identity Bar
type: SISP
status: in-figma
sprint: 8
approved-by: Giuliana
approved-date: 2026-08-11
figma-node-id: 974:9679
---

# Component Spec — Identity Bar

> Referências:
> - Session log: D176
> - Componente criado manualmente por Giuliana no Figma
> - Substitui BC-14 Headers nos layouts DV

---

## O que é

Barra de identidade institucional da Delegacia Virtual. Combina branding da Polícia Civil (logo + nome), navegação principal (tabs), e logo do Governo de Santa Catarina. Substitui o BC-14 Headers genérico nos layouts DV porque o header padrão não comporta a identidade institucional da PC.

---

## Audiência de uso

- Cidadão navegando na DV — identifica o produto e a instituição
- Policial homologador — reconhece o sistema institucional
- Dev construindo layouts DV — instância obrigatória no topo de toda tela DV

---

## Props / API

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| activeTab | `string` | Sim | `'Início'` | Tab ativa na navegação |
| tabs | `string[]` | Não | `['Início', 'Serviços', 'Sobre o DV', 'Ajuda']` | Lista de tabs de navegação |
| logo | `string` | Não | — | URL do logo institucional (Polícia Civil) |
| orgName | `string` | Não | `'Polícia Civil'` | Nome da organização |
| productName | `string` | Não | `'Delegacia Virtual'` | Nome do produto |

**Convenção Angular:**
```html
<sisp-lib-identity-bar [sispLibIdentityBarConfig]="config"></sisp-lib-identity-bar>
```

---

## Estados e variantes

| Estado | Descrição visual | Dimensões |
|---|---|---|
| Default | Barra full-width com Logo Zone à esquerda e Nav Bar à direita | 1440×97px |

> Nota: componente criado inicialmente apenas como Desktop. Variante Mobile a definir em sprint futuro (hamburger menu, como BC-14 Headers Mobile).

---

## Anatomia

```
┌──────────────────────────────────────────────────────────────┐
│                      dark/surface background                  │
│                                                              │
│  ┌─────────────────────┐         ┌─────────────────────────┐ │
│  │ 🛡️ Polícia Civil     │         │ Início  Serviços  Sobre │ │
│  │    Delegacia Virtual │         │                   🏛️ SC │ │
│  └─────────────────────┘         └─────────────────────────┘ │
│       Logo Zone (166px)                Nav Bar (495px)        │
│                                                              │
└──────────────────────────────────────────────────────────────┘
  1440 × 97px
```

| Elemento | Detalhe |
|---|---|
| Logo Zone | Badge PC 48×61px + "Polícia Civil" (Montserrat SemiBold 16px) + "Delegacia Virtual" (Arial 14px), ambos `text/inverse` |
| Nav Bar | BC-26 Tabs Underline instance (4 Tab Items) + Logo Governo SC 110×28px |
| Background | `dark/surface` (#192D22) |

---

## Tokens e cores

| Elemento | Token | Variável Figma |
|---|---|---|
| Background | `dark/surface` | VariableID:106:103 |
| Texto org/produto | `text/inverse` | VariableID:106:86 |
| Tabs | Herdam tokens do BC-26 Tabs | — |
| Padding horizontal | 48px (space/12) | VariableID:106:63 |
| Padding vertical | centrado verticalmente | — |
| Gap Logo Zone interno | 8px (space/2) | VariableID:106:56 |

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução proposta |
|---|---|---|
| Contraste (wcag) | N/A — componente novo | Texto branco sobre dark/surface: 12.8:1 ✅ |
| Uso de cor (cor) | Tab ativa diferenciada por underline + cor | Underline 3px garante 2 canais visuais |
| Visual (vis) | N/A | Logos com alt text descritivo |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução proposta |
|---|---|---|
| H-1 Visibilidade | BC-14 Headers não mostrava contexto institucional | Logo PC + nome do produto visíveis permanentemente |
| H-2 Mundo real | Header genérico não reflete linguagem institucional | "Polícia Civil" + "Delegacia Virtual" = linguagem do mundo real |
| H-4 Consistência | DV usava header genérico diferente de outros sistemas PC | Identity Bar padroniza a identidade visual da PC em todos os produtos |
| H-6 Reconhecimento | Sem logo institucional, usuário não reconhece o sistema | Badge PC + Logo Governo SC = reconhecimento imediato |

---

## Regras de acessibilidade

- [x] Contraste mínimo 4.5:1 — branco sobre verde escuro: 12.8:1
- [x] Navegável por teclado — tabs via BC-26 (já acessível)
- [x] ARIA: `<nav role="navigation" aria-label="Navegação principal">`
- [x] Logo PC: `<img alt="Brasão da Polícia Civil de Santa Catarina">`
- [x] Logo Governo SC: `<img alt="Governo de Santa Catarina">`
- [x] Tab ativa com `aria-current="page"`
- [x] Label em português

---

## Comportamentos esperados

- Quando o usuário clica em uma tab → navega para a seção correspondente
- Quando hover em tab → underline preview (via BC-26 Tab Item hover)
- Tab ativa exibe underline 3px (`primary/base`)
- Logo PC é link para a home da DV
- Logo Governo SC é link externo (abre nova aba)
- Barra é fixa no topo da viewport (sticky)

---

## Comportamento responsivo

| Precisa de variante `Layout=Mobile`? | Sim — em sprint futuro |
|---|---|
| **Desktop** | Layout horizontal: Logo Zone + Nav Bar + Logo Governo SC |
| **Mobile** | Logo Zone + hamburger ≡ (drawer com tabs verticais) |
| **Tablet** | Segue Desktop |

> Variante Mobile não criada neste sprint. Layouts DV Mobile cobertos pelos protótipos HTML com hamburger menu implementado em CSS.

---

## Composição atômica

| Instância interna | Componente DS | Uso |
|---|---|---|
| Tabs · Nav | BC-26 Tabs (Style=Underline) | Navegação principal com 4 tabs |
| Tab Items (4×) | BC-26 Tab Item | Início, Serviços, Sobre o DV, Ajuda |

---

## Diferença do BC-14 Headers

| Aspecto | BC-14 Headers | SC-21 Identity Bar |
|---|---|---|
| Contexto | Genérico — qualquer produto SISP | Específico — Delegacia Virtual |
| Branding | Sem logo institucional | Logo PC + "Polícia Civil" + "Delegacia Virtual" |
| Governo | Sem referência | Logo Governo de SC |
| Background | `dark/surface` | `dark/surface` (mesmo) |
| Tabs | BC-26 Tabs (instância) | BC-26 Tabs (instância) — mesmo |

> SC-21 é uma especialização do BC-14 para produtos da Polícia Civil. O BC-14 continua existindo para produtos SISP genéricos.

---

## Casos excepcionais / bordas

- Nome da organização longo: truncar com ellipsis (max 200px)
- Mais de 5 tabs: scroll horizontal ou dropdown "Mais"
- Logo não carrega: exibir texto "PC" como fallback no badge
- Sem tabs configuradas: exibir apenas Logo Zone

---

## O que está fora deste spec

- Variante Mobile (hamburger + drawer) — sprint futuro
- Submenu/dropdown nas tabs
- Search integrado na barra (usar BC-29 Search separado)
- Notificações/badge de alertas

---

## Critérios de aceite

- [x] Componente existe no Figma com tokens aplicados
- [x] Composição atômica: BC-26 Tabs como instância (5 instâncias)
- [x] Branding PC visível (logo + nome + governo)
- [x] Contraste WCAG AA verificado
- [x] Label em português
- [ ] Exemplo Angular documentado
- [x] Revisado e aprovado por Giuliana (D176 — criação manual)
