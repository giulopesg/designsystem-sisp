---
component-id: SC-18
component-name: Footer DV
type: SISP
status: in-figma
sprint: 8
approved-by: Giuliana
approved-date: 2026-08-11
figma-node-id: 969:9388
---

# Component Spec — Footer DV

> Referências:
> - Session log: D172, D177
> - Componente Figma criado e atualizado manualmente por Giuliana (D177)
> - Padrão visual: DC-08 Footer Portal

---

## O que é

Footer institucional específico da Delegacia Virtual. Exibe informações de contato, links institucionais, branding da DV, e copyright do CiASC. Diferencia-se do DC-08 Footer Portal por ser mais compacto e conter colunas específicas do domínio policial (Emergência, Institucional, Ajuda).

---

## Audiência de uso

- Cidadão acessando a Delegacia Virtual (todas as páginas)
- Dev construindo layouts DV — instância obrigatória no rodapé de toda tela

---

## Props / API

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| type | `'institucional' \| 'simplificado'` | Sim | `'institucional'` | Tipo do footer — institucional (4 colunas) ou simplificado (3 colunas) |

**Convenção Angular:**
```html
<sisp-lib-footer-dv [sispLibFooterDvConfig]="config"></sisp-lib-footer-dv>
```

---

## Estados e variantes

| Variante | Descrição visual | Dimensões |
|---|---|---|
| Type=Institucional | 4 colunas: brand (logo + título + descrição) + Sobre + Emergência + Ajuda. Separator + copyright CiASC + versão. | 1440×296px |
| Type=Simplificado | 3 colunas: Emergência + Institucional + Ajuda. Sem coluna de brand. Separator + copyright. | 1440×226px |

---

## Anatomia

```
┌─────────────────────────────────────────────────────────────┐
│                    dark/surface background                   │
│                                                             │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐   │
│  │ Logo PC  │  │  SOBRE   │  │EMERGÊNCIA│  │  AJUDA   │   │
│  │ "DV"     │  │  link    │  │  link    │  │  link    │   │
│  │ descrição│  │  link    │  │  link    │  │  link    │   │
│  │          │  │  link    │  │          │  │  link    │   │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘   │
│                                                             │
│  ─────────────────── separator ──────────────────────────   │
│  © 2020-2026 · Todos os direitos reservados   Versão 1.0   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## Tokens e cores

| Elemento | Token | Variável Figma |
|---|---|---|
| Background | `dark/surface` | VariableID:106:103 |
| Títulos das colunas | Overline/XS (Montserrat SemiBold 12px uppercase) | Text Style |
| Links | Body/SM/Regular (Arial 14px) | Text Style |
| Descrição brand | Body/XS/Regular (Arial 12px) | Text Style |
| Cor do texto | `text/inverse` | VariableID:106:86 |
| Separator | `text/inverse` (1px) | VariableID:106:86 |
| Padding horizontal | 48px (space/12) | VariableID:106:63 |
| Padding vertical | 32px top, 48px bottom | space/8, space/12 |
| Gap entre colunas | 48px (space/12) | VariableID:106:63 |

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução proposta |
|---|---|---|
| Contraste (wcag) | N/A — componente novo | Texto branco (#FFF) sobre dark/surface (#192D22): contraste 12.8:1 ✅ |
| Uso de cor (cor) | N/A | Links underline on hover — não depende só de cor |
| Tipografia (tip) | N/A | Títulos uppercase com letter-spacing 0.1em para legibilidade |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução proposta |
|---|---|---|
| H-2 Mundo real | Footer anterior não tinha links de emergência | Type=Institucional inclui coluna Emergência (197, 181) |
| H-4 Consistência | Footer DV não seguia padrão do portal | Mesma estrutura visual do DC-08 Footer Portal |
| H-6 Reconhecimento | Sem branding institucional | Coluna brand com logo PC + "Delegacia Virtual" |

---

## Regras de acessibilidade

- [x] Contraste mínimo 4.5:1 — branco sobre verde escuro: 12.8:1
- [x] Navegável por teclado — links tabuláveis
- [x] ARIA: `<footer role="contentinfo">`
- [x] Landmark semântico para screen readers
- [x] Links descritivos (não "clique aqui")
- [x] Label em português

---

## Comportamentos esperados

- Footer é estático — sem interação além de links
- Links abrem na mesma aba (páginas internas) ou nova aba (links externos como telefones)
- Versão exibida dinamicamente via config
- Copyright com ano atualizado automaticamente

---

## Comportamento responsivo

| Precisa de variante `Layout=Mobile`? | Não neste sprint — footer compacto o suficiente para stack vertical via CSS |
|---|---|
| **Desktop** | 4 colunas horizontais, 1440px full-width |
| **Mobile** | Colunas empilham verticalmente, padding reduzido via token responsivo |
| **Tablet** | Segue Desktop |

---

## Composição atômica

Sem instâncias de BC — footer é composto por text nodes e frames simples. Logo PC é imagem (não componente).

---

## Casos excepcionais / bordas

- Se lista de links muda entre produtos SISP, usar config para definir colunas
- Se logo PC não carrega, exibir texto "Polícia Civil" como fallback
- Coluna brand é opcional no Type=Simplificado

---

## O que está fora deste spec

- Versão mobile dedicada (stack vertical) — implementar via CSS responsive
- Links dinâmicos via CMS — hardcoded neste sprint
- Ícones sociais — removidos na versão final (D177)

---

## Critérios de aceite

- [x] Todos os estados listados existem no Figma com tokens aplicados
- [x] Contraste WCAG AA verificado
- [x] Consistência visual com DC-08 Footer Portal
- [x] Label em português
- [ ] Exemplo Angular documentado
- [x] Revisado e aprovado por Giuliana (D177 — edição manual)
