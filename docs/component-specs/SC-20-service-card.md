---
component-id: SC-20
component-name: Service Card
type: SISP
status: in-figma
sprint: 8
approved-by: Giuliana
approved-date: 2026-08-11
figma-node-id: 968:8784
---

# Component Spec — Service Card

> Referências:
> - Session log: D175
> - Padrão visual: DC-04 Section Card (portal DS)
> - Composição: BC-15 Icons MD

---

## O que é

Card de serviço institucional da Delegacia Virtual. Exibe um serviço disponível (Registrar Ocorrência, Imprimir BO, Emitir Atestado, Comunicar) com ícone, título e descrição curta. Padrão visual idêntico ao DC-04 Section Card do portal DS.

---

## Audiência de uso

- Cidadão na Home da DV — identifica e acessa serviços disponíveis
- Dev construindo layouts DV — instância na seção "Nossos Serviços"

---

## Props / API

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| title | `string` | Sim | `'Título do Serviço'` | Nome do serviço exibido no card |
| description | `string` | Sim | `'Descrição do serviço'` | Descrição curta do serviço |
| icon | `string` | Não | `'fa-file-lines'` | Classe Font Awesome do ícone |
| link | `string` | Sim | — | URL de destino ao clicar |

**Convenção Angular:**
```html
<sisp-lib-service-card [sispLibServiceCardConfig]="config"></sisp-lib-service-card>
```

**Figma text properties:**
- `Title#974:11` — override do título
- `Description#974:13` — override da descrição

---

## Estados e variantes

| Estado | Descrição visual | Tokens usados |
|---|---|---|
| Default | Card branco com ícone circular, título bold, descrição, seta → | surface/base, primary/base, primary/muted |
| Hover | Elevação sutil (shadow) + cursor pointer | — (comportamento CSS) |

---

## Anatomia

```
┌─────────────────────────────────┐
│  ┌────┐                    →   │
│  │ 🔴 │  Título do Serviço      │
│  └────┘  Descrição do serviço   │
│                                 │
└─────────────────────────────────┘
  282 × 156px
```

| Elemento | Detalhe |
|---|---|
| Icon Circle | 40×40px, fill `primary/muted`, radius/full |
| Icon | BC-15 Icons MD, fill `primary/base`, Font Awesome |
| Title | Heading/SM (Montserrat SemiBold) |
| Description | Body/SM/Regular (Arial 14px), `text/secondary` |
| Arrow | → chevron, `text/muted` |

---

## Tokens e cores

| Elemento | Token | Variável Figma |
|---|---|---|
| Background card | `surface/base` | VariableID:106:90 |
| Icon container | `primary/muted` | VariableID:106:78 |
| Icon fill | `primary/base` | VariableID:106:76 |
| Título | `text/primary` | VariableID:106:83 |
| Descrição | `text/secondary` | VariableID:106:84 |
| Arrow | `text/muted` | VariableID:106:85 |
| Border radius card | `radius/lg` | VariableID:106:72 |
| Padding interno | 16px (space/4) | VariableID:106:58 |
| Gap entre ícone e texto | 12px (space/3) | VariableID:106:57 |

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução proposta |
|---|---|---|
| Contraste (wcag) | N/A — componente novo | text/primary sobre surface/base: 17.4:1 ✅ |
| Uso de cor (cor) | Ícone usa cor como diferenciador | Acompanhado por título textual — cor é decorativa |
| Visual (vis) | N/A | Card com borda ou shadow para delimitação visual |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução proposta |
|---|---|---|
| H-6 Reconhecimento | Cards sem ícone dificultam scanning | Ícone Font Awesome por serviço para reconhecimento rápido |
| H-8 Estética | Cards genéricos sem diferenciação | Ícone + título + descrição = hierarquia visual clara |

---

## Regras de acessibilidade

- [x] Contraste mínimo 4.5:1 — todos os textos verificados
- [x] Card inteiro é clicável — `<a>` semântico ou `role="link"`
- [x] Focus ring visível no card (não no link interno)
- [x] Ícone é decorativo — `aria-hidden="true"`
- [x] Arrow é decorativa — não duplica a ação de click
- [x] Label em português

---

## Comportamentos esperados

- Quando o usuário clica no card → navega para o serviço correspondente
- Quando hover → elevação sutil indica interatividade
- Quando focus (teclado) → focus ring visível no contorno do card
- Card é link único — toda a superfície é clicável

---

## Comportamento responsivo

| Precisa de variante `Layout=Mobile`? | Não — card de 282px é auto-contido, menor que 320px |
|---|---|
| **Desktop** | Cards em grid 4 colunas na seção "Nossos Serviços" |
| **Mobile** | Cards empilham em 1 coluna, largura 100% do container |
| **Tablet** | Grid 2 colunas |

---

## Composição atômica

| Instância interna | Componente DS | Uso |
|---|---|---|
| Icon | BC-15 Icons MD | Ícone do serviço dentro do circle container |

---

## Casos excepcionais / bordas

- Título longo (>30 chars): truncar com ellipsis ou quebrar em 2 linhas (max 2)
- Descrição longa: truncar em 2 linhas com ellipsis
- Sem ícone: circle container exibe fallback genérico (fa-circle)

---

## O que está fora deste spec

- Variante com imagem em vez de ícone
- Estado disabled (serviço indisponível) — não existe na DV atual
- Badge de status no card

---

## Critérios de aceite

- [x] Todos os estados listados existem no Figma com tokens aplicados
- [x] Composição atômica: BC-15 Icons MD como instância
- [x] Text properties configuradas (Title, Description)
- [x] Contraste WCAG AA verificado
- [x] Label em português
- [ ] Exemplo Angular documentado
- [x] Revisado e aprovado por Giuliana (D175)
