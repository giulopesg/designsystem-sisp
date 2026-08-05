---
component-id: DC-01
component-name: Breadcrumb
type: Doc
status: in-figma
sprint: 6
approved-by: [Giuliana]
approved-date: [2026-07-27]
figma-node-id: [498:6]
---

# Component Spec — Breadcrumb

> **Tipo:** Componente documental (DC) — exclusivo do portal DS, não vira Angular.
> **Auditoria Regra 12:** nenhum dos 37 Component Sets existentes cobre trilha de navegação hierárquica.

---

## O que é

Breadcrumb é a trilha de navegação hierárquica do portal DS. Mostra a posição do usuário na estrutura do site ("Componentes Base > Button"). Aparece acima do título em todas as páginas de componente (WF-02) e outras páginas internas do portal.

---

## Audiência de uso

- **Devs CiASC / terceiros:** navegam no portal para consultar documentação de componentes. Breadcrumb indica onde estão e permite voltar para seções superiores
- **POs (Sommer/Holiwod):** usam o portal para validar componentes — breadcrumb orienta na navegação

---

## Props / API

> Componente exclusivo do Figma — sem API Angular. Props descrevem as propriedades visuais configuráveis.

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `items` | `string[]` | sim | — | Lista de itens na trilha (ex: ["Componentes Base", "Button"]) |

---

## Anatomia do componente

```
┌──────────────────────────────────────────────┐
│  Componentes Base  >  Button                 │
│  (link)         (sep)  (atual)               │
└──────────────────────────────────────────────┘
```

- **Link items:** Body/SM Regular, fill=text/secondary. Clicáveis — navegam para a seção
- **Separador:** ">" em Body/SM Regular, fill=text/muted. Decorativo (não clicável)
- **Item atual (último):** Body/SM Bold, fill=text/primary. Não clicável (já estamos aqui)

---

## Estrutura no Figma

| Propriedade | Valor | Token / Variável |
|---|---|---|
| Tipo | Component Set | — |
| Auto-layout | HORIZONTAL | — |
| Gap | 8px | `space/2` (VariableID:106:54) |
| Padding | 0 | — |
| Resize | HUG × HUG | — |

### Elementos internos

| Elemento | Tipo | Text Style | Fill | Variável |
|---|---|---|---|---|
| Link item | Text | Body/SM Regular | text/secondary | VariableID:106:84 |
| Separador ">" | Text | Body/SM Regular | text/muted | VariableID:106:85 |
| Item atual | Text | Body/SM Bold | text/primary | VariableID:106:83 |

---

## Estados e variantes

| Variante | Descrição |
|---|---|
| **Default** | Trilha com N links + separadores + item atual |

> 1 variante apenas. Conteúdo dos items é override de texto na instância.

---

## Composição atômica

> **Regra 11:** "este elemento já existe como componente?"

| Elemento | Existe como componente? | Decisão |
|---|---|---|
| Texto (link) | Não — elemento primitivo (text node) | Frame manual |
| Texto (separador) | Não — caractere decorativo | Frame manual |
| Texto (atual) | Não — elemento primitivo (text node) | Frame manual |

**Resultado:** DC-01 Breadcrumb é um componente folha — não contém instâncias de outros componentes. Composto apenas de text nodes com text styles e fills do DS.

---

## Regras de acessibilidade (proativas)

- [ ] Container com `role="navigation"` e `aria-label="Breadcrumb"`
- [ ] Lista semântica: `<ol>` com `<li>` para cada item
- [ ] Item atual com `aria-current="page"`
- [ ] Separadores são decorativos (`aria-hidden="true"`)
- [ ] Contraste verificado:
  - text/secondary (#4B5563) sobre surface/bg-subtle (#F9FAFB): ≥ 5.9:1 ✅ AA
  - text/primary (#08060F) sobre surface/bg-subtle (#F9FAFB): ≥ 18:1 ✅ AAA
  - text/muted (#9CA3AF) sobre surface/bg-subtle (#F9FAFB): ≥ 2.8:1 — separador decorativo, não informacional ✅
- [ ] Labels em português

---

## Comportamento responsivo

> Regra 13 — Responsividade obrigatória.

| Precisa de variante `Layout=Mobile`? | **Não** — auto-contido, HUG horizontal, sempre < 320px |
|---|---|
| **Desktop** | Exibido inline acima do título |
| **Mobile** | Mesmo comportamento — texto pode quebrar linha naturalmente |
| **Tablet** | Segue Desktop |

---

## Casos excepcionais / bordas

- **Trilha muito longa (> 3 níveis):** improvável no portal DS (máximo 3: "Componentes Base > Formulários > Input"). Se necessário, truncar níveis intermediários com "..."
- **Item único:** apenas o item atual (sem links nem separadores) — caso da Home

---

## O que está fora deste spec

- **Dropdown em breadcrumb (padrão Shopify):** o portal DS tem hierarquia rasa (máximo 3 níveis). Dropdown é over-engineering
- **Versão Angular:** DC components são exclusivos do portal. Se necessário no futuro, criar BC-XX
- **Micro-interações (hover underline):** componente Figma é estático. Hover documentado para referência: underline on hover nos links

---

## Critérios de aceite

- [ ] Component Set no Figma com 1 variante (Default)
- [ ] Auto-layout horizontal, gap=space/2
- [ ] Text styles aplicados: Body/SM Regular (links, separador), Body/SM Bold (atual)
- [ ] Fills bound a variáveis: text/secondary, text/muted, text/primary
- [ ] Zero valores hardcoded (Regra 8)
- [ ] Exemplo de conteúdo: "Componentes Base > Button"
- [ ] Revisado e aprovado por Giuliana
