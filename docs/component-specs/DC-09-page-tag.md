---
component-id: DC-09
component-name: Page Tag
type: Doc
status: in-figma
sprint: 6
approved-by: Giuliana
approved-date: 2026-08-11
figma-node-id: 579:4526
---

# Component Spec — Page Tag

> **Tipo:** Componente documental (DC) — exclusivo do portal DS, não vira Angular.
> **Referências:**
> - Criado durante Sprint 6 para uso em DC-04 Section Card e DC-05 Persona Card
> - Auditoria Regra 12: BC-04 Badge tem estilo visual diferente (cores semânticas, rounded). Page Tag é um chip neutro para categorização de subpages/pathways.

---

## O que é

Tag de categorização usada em cards do portal DS. Indica subpages (em Section Cards) ou pathways (em Persona Cards). Formato compacto tipo chip, com fundo sutil e texto secundário. Variante com ícone para Persona Cards.

---

## Audiência de uso

- **Devs CiASC / terceiros:** identificam rapidamente as subpages dentro de cada seção do portal ou os pathways de cada persona
- **POs:** entendem a granularidade do conteúdo sem precisar navegar

---

## Props / API

> Componente exclusivo do Figma — sem API Angular.

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `label` | `string` | Sim | `'Label'` | Texto da tag |
| `icon` | `Icon` | Não | — | Instância BC-15 Icons XS (apenas na variante Icon=Yes) |

---

## Estados e variantes

| Variante | Descrição visual | Dimensões |
|---|---|---|
| Icon=No | Chip retangular sem ícone | ~46×22px (HUG) |
| Icon=Yes | Chip pill com ícone à esquerda | ~66×26px (HUG) |

---

## Anatomia

```
Icon=No:
┌────────────┐
│  Label     │
└────────────┘
  radius/sm (4px)

Icon=Yes:
┌─────────────────┐
│  🔲  Label      │
└─────────────────┘
  radius/full (pill)
```

| Elemento | Detalhe |
|---|---|
| Label | Body/XS Regular (Arial 12px), fill `text/secondary` (VariableID:106:84) |
| Icon (Icon=Yes) | Instância BC-15 Icons XS (16×16px) |
| Background | `surface/bg-subtle` (VariableID:106:88) |

---

## Tokens e cores

| Elemento | Token | Variável Figma |
|---|---|---|
| Background | `surface/bg-subtle` | VariableID:106:88 |
| Label text | `text/secondary` | VariableID:106:84 |
| Border radius (Icon=No) | `radius/sm` (4px) | VariableID:106:68 |
| Border radius (Icon=Yes) | `radius/full` (9999px) | VariableID:106:72 |
| Padding (Icon=No) | 2px vertical, 8px horizontal | space/0-5 (VariableID:106:54), space/2 (VariableID:106:56) |
| Padding (Icon=Yes) | 4px vertical, 8px horizontal | space/1 (VariableID:106:55), space/2 (VariableID:106:56) |
| Gap ícone-label (Icon=Yes) | 4px (space/1) | VariableID:106:55 |

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução proposta |
|---|---|---|
| Contraste (wcag) | text/secondary (#4B5563) sobre surface/bg-subtle (#F9FAFB): ~7.2:1 ✅ | Contraste suficiente |
| Uso de cor (cor) | N/A | Tag é complementar ao título — cor não é o único canal |
| Visual (vis) | N/A | Fundo sutil + radius diferencia visualmente do texto ao redor |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução proposta |
|---|---|---|
| H-6 Reconhecimento | Tags sem ícone são genéricas | Variante Icon=Yes adiciona ícone para reconhecimento rápido em Persona Cards |
| H-8 Estética | Chip compacto reduz ruído visual em cards | Tamanho mínimo, cor sutil — não compete com título/descrição |

---

## Regras de acessibilidade

- [x] Contraste mínimo 4.5:1 — text/secondary sobre surface/bg-subtle: ~7.2:1
- [x] Tag é informação complementar — não crítica para compreensão
- [x] Ícone (Icon=Yes) é decorativo — `aria-hidden="true"`
- [x] Label em português

---

## Comportamentos esperados

- Tag é estática — sem interação (não clicável)
- Conteúdo via override de texto na instância
- Ícone via override de instância BC-15 Icons XS

---

## Comportamento responsivo

| Precisa de variante `Layout=Mobile`? | Não — componente inline, menor que 100px |
|---|---|
| **Desktop** | Inline dentro de cards (DC-04, DC-05) |
| **Mobile** | Mesmo tamanho — auto-contido |
| **Tablet** | Segue Desktop |

---

## Composição atômica

| Instância interna | Componente DS | Uso |
|---|---|---|
| Icon (Icon=Yes) | BC-15 Icons XS | Ícone decorativo à esquerda do label |

**Resultado:** DC-09 (Icon=Yes) contém 1 instância de BC-15 Icons XS. DC-09 (Icon=No) não contém instâncias.

---

## Casos excepcionais / bordas

- Label longo (>15 chars): text wrap não esperado — truncar se necessário, mas labels devem ser curtos (1-3 palavras)
- Sem label: tag não faz sentido vazia — ocultar instância

---

## O que está fora deste spec

- Estado interativo (hover, click) — tag é estática
- Variante com cor semântica (success, danger) — usar BC-04 Badge para esse caso
- Versão Angular — DC components são exclusivos do portal

---

## Critérios de aceite

- [x] Component Set no Figma com 2 variantes (Icon=No, Icon=Yes)
- [x] Fill bound a `surface/bg-subtle`
- [x] Text style aplicado: Body/XS Regular
- [x] Text fill bound a `text/secondary`
- [x] Icon=No: radius bound a `radius/sm`
- [x] Icon=Yes: radius bound a `radius/full`, instância BC-15 Icons XS (Regra 11)
- [x] Label em português
- [ ] Exemplo Angular documentado (N/A — DC component)
- [x] Revisado e aprovado por Giuliana
