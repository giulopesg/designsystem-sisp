---
component-id: DC-10
component-name: Callout Card
type: Doc
status: in-figma
sprint: 6
approved-by: Giuliana
approved-date: 2026-08-11
figma-node-id: 584:5450
---

# Component Spec — Callout Card

> **Tipo:** Componente documental (DC) — exclusivo do portal DS, não vira Angular.
> **Referências:**
> - Session log: D148, D149
> - Criado para destaques de informação no portal (ex: "Saiba mais sobre theming", "Componente em beta")
> - Auditoria Regra 12: BC-03 Alert é para feedback de sistema (sucesso/erro/warning). Callout Card é para destaque editorial de conteúdo — padrão funcional diferente.

---

## O que é

Card de destaque editorial para informações importantes dentro de páginas do portal DS. Diferente do BC-03 Alert (feedback de sistema), o Callout Card é um componente editorial — destaca conteúdo relevante sem urgência semântica. Usa borda lateral esquerda colorida (4px primary/base) como indicador visual e fundo sutil.

---

## Audiência de uso

- **Devs CiASC / terceiros:** identificam rapidamente informações complementares ou contextuais nas páginas de documentação
- **POs:** chamadas de atenção para decisões de design ou regras de governança

---

## Props / API

> Componente exclusivo do Figma — sem API Angular.

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `title` | `string` | Sim | `'Callout Title'` | Título do destaque |
| `description` | `string` | Sim | `'Callout description text.'` | Texto descritivo |
| `icon` | `Icon` | Não | — | Instância BC-15 Icons LG dentro de container 40×40 |

---

## Estados e variantes

| Variante | Descrição visual | Dimensões |
|---|---|---|
| Layout=Desktop | Horizontal: ícone à esquerda + texto à direita | 600×93px |
| Layout=Mobile | Vertical: ícone acima + texto abaixo | 343×129px |

---

## Anatomia

```
Desktop:
┌─┬──────────────────────────────────────────────────┐
│▌│  🔲  Callout Title                               │
│▌│      Callout description text.                    │
└─┴──────────────────────────────────────────────────┘
 4px  600 × 93px
 borda

Mobile:
┌─┬──────────────────────────┐
│▌│  🔲                      │
│▌│  Callout Title           │
│▌│  Callout description.    │
└─┴──────────────────────────┘
 4px  343 × 129px
```

| Elemento | Detalhe |
|---|---|
| Icon Container | Frame 40×40px, fill `primary/base` (VariableID:106:76), radius `radius/md` (6px) |
| Icon | Instância BC-15 Icons LG (28×28px) dentro do container |
| Title | Heading/MD (Montserrat Bold), fill `text/primary` (VariableID:106:83) |
| Description | Body/SM Regular (Arial 14px), fill `text/secondary` (VariableID:106:84) |
| Background | `primary/muted` (VariableID:106:78) |
| Borda esquerda | 4px, `primary/base` — via strokeLeftWeight=4, strokeTopWeight/Right/Bottom=1 |

---

## Tokens e cores

| Elemento | Token | Variável Figma |
|---|---|---|
| Background | `primary/muted` | VariableID:106:78 |
| Icon container fill | `primary/base` | VariableID:106:76 |
| Icon fill | `text/inverse` (dentro do BC-15) | VariableID:106:86 |
| Título | `text/primary` | VariableID:106:83 |
| Descrição | `text/secondary` | VariableID:106:84 |
| Borda esquerda | `primary/base` (4px) | VariableID:106:76 |
| Border radius | `radius/lg` (8px) | VariableID:106:71 |
| Icon container radius | `radius/md` (6px) | VariableID:106:70 |
| Padding Desktop | 24px (space/6) | VariableID:106:60 |
| Padding Mobile | 16px (space/4) | VariableID:106:58 |
| Gap Desktop | 20px (space/5) | VariableID:106:59 |
| Gap Mobile | 12px (space/3) | VariableID:106:57 |

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução proposta |
|---|---|---|
| Contraste (wcag) | N/A — componente novo | text/primary (#08060F) sobre primary/muted (#FEF2F2): ≥ 17:1 ✅ |
| Uso de cor (cor) | Borda colorida diferencia do corpo da página | Acompanhada de ícone + fundo sutil — cor não é o único canal |
| Visual (vis) | N/A | Borda 4px + fundo + ícone = 3 canais visuais |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução proposta |
|---|---|---|
| H-6 Reconhecimento | Sem componente para destaques editoriais | Callout Card com ícone + borda colorida = reconhecimento imediato |
| H-8 Estética | Texto corrido sem destaque visual | Card com fundo sutil e borda lateral quebra a monotonia |

---

## Regras de acessibilidade

- [x] Contraste mínimo 4.5:1 — texto escuro sobre fundo sutil: ≥ 17:1
- [x] Callout é informacional — `role="complementary"` ou `<aside>`
- [x] Ícone é decorativo — `aria-hidden="true"`
- [x] Borda colorida acompanhada de fundo sutil + ícone (não depende só de cor)
- [x] Label em português

---

## Comportamentos esperados

- Callout é estático — sem interação
- Conteúdo via override de texto na instância
- Ícone via override de instância BC-15 Icons LG
- Pode ser usado em qualquer página do portal para destacar informação

---

## Comportamento responsivo

| Precisa de variante `Layout=Mobile`? | Sim — já implementado |
|---|---|
| **Desktop** | Horizontal: ícone à esquerda, texto à direita (600×93px) |
| **Mobile** | Vertical: ícone acima, texto abaixo (343×129px) |
| **Tablet** | Segue Desktop |

---

## Composição atômica

| Instância interna | Componente DS | Uso |
|---|---|---|
| Icon | BC-15 Icons LG | Ícone decorativo dentro de container 40×40 primary/base |

**Resultado:** DC-10 contém 1 instância de BC-15 Icons LG.

---

## Casos excepcionais / bordas

- Sem título: description sozinha — ocultar title node
- Description longa (>3 linhas): card cresce verticalmente (HUG)
- Sem ícone: ocultar container — apenas borda lateral + texto
- Theming: borda e fundo usam primary/* — adaptam automaticamente por tema (SC/PC)

---

## O que está fora deste spec

- Variante de tipo (info, warning, success) — usar BC-03 Alert para feedback semântico
- Callout dismissível — componente é editorial, não de sistema
- Versão Angular — DC components são exclusivos do portal

---

## Critérios de aceite

- [x] Component Set no Figma com 2 variantes (Desktop, Mobile)
- [x] Fill bound a `primary/muted`
- [x] Stroke esquerda 4px bound a `primary/base`
- [x] Icon container fill bound a `primary/base`, radius bound a `radius/md`
- [x] BC-15 Icons LG como instância (Regra 11)
- [x] Text styles aplicados: Heading/MD (título), Body/SM Regular (descrição)
- [x] Text fills bound a variáveis: `text/primary`, `text/secondary`
- [x] Radius bound a `radius/lg`
- [x] Desktop padding space/6, gap space/5. Mobile padding space/4, gap space/3
- [x] 100% variable bindings, 100% text styles
- [x] Label em português
- [ ] Exemplo Angular documentado (N/A — DC component)
- [x] Revisado e aprovado por Giuliana
