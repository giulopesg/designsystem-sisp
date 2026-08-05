---
component-id: DC-03
component-name: CodeBlock
type: Doc
status: in-figma
sprint: 6
approved-by: [Giuliana]
approved-date: [2026-07-27]
figma-node-id: [500:9]
---

# Component Spec — CodeBlock

> **Tipo:** Componente documental (DC) — exclusivo do portal DS, não vira Angular.
> **Auditoria Regra 12:** nenhum dos 37 Component Sets existentes cobre bloco de código com syntax highlighting. BC-13 Textarea é campo de formulário editável — padrão funcional completamente diferente.

---

## O que é

CodeBlock é o bloco de exibição de código do portal DS. Mostra trechos de código Angular/HTML com fundo escuro, uma barra superior indicando a linguagem e um botão "Copiar" para clipboard. Aparece em todas as páginas de componente (WF-02) na seção "Exemplo Angular" e em páginas de documentação técnica.

---

## Audiência de uso

- **Devs CiASC / terceiros:** copiam trechos de código para integrar componentes nos projetos. CodeBlock é o principal mecanismo de transferência de conhecimento técnico do portal
- **Demilis (mantenedor):** valida que os exemplos Angular estão corretos e atualizados

---

## Props / API

> Componente exclusivo do Figma — sem API Angular.

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `language` | `string` | sim | `"HTML"` | Label da linguagem na barra superior (ex: "HTML", "TypeScript", "CSS") |
| `code` | `string` | sim | — | Conteúdo do código exibido |

---

## Anatomia do componente

```
┌─────────────────────────────────────────────────┐
│  HTML                                  Copiar   │  ← barra superior (escura)
├─────────────────────────────────────────────────┤
│                                                 │
│  <sisp-lib-button                               │  ← área de código (mais escura)
│    [sispLibButtonConfig]="config">              │
│  </sisp-lib-button>                             │
│                                                 │
└─────────────────────────────────────────────────┘
```

- **Barra superior:** fundo escuro (dark/surface), label da linguagem + botão "Copiar"
- **Área de código:** fundo mais escuro (text/primary como fill do frame), texto em Mono/SM, fill=text/inverse

---

## Estrutura no Figma

| Propriedade | Valor | Token / Variável |
|---|---|---|
| Tipo | Component Set | — |
| Auto-layout | VERTICAL | — |
| Border radius | 12px | `radius/lg` (VariableID:106:70) |
| Width | FILL (herda do container) | — |
| Clip content | true | — |

### Barra superior

| Propriedade | Valor | Token / Variável |
|---|---|---|
| Auto-layout | HORIZONTAL | — |
| Justify | SPACE_BETWEEN | — |
| Padding horizontal | 16px | `space/4` (VariableID:106:56) |
| Padding vertical | 8px | `space/2` (VariableID:106:54) |
| Fill (fundo) | dark/surface | VariableID:106:103 (#192D22) |
| Width | FILL | — |

#### Label da linguagem

| Propriedade | Valor | Token / Variável |
|---|---|---|
| Text | "HTML" (override) | — |
| Text Style | Label/SM | — |
| Fill | text/inverse | VariableID:106:86 (#FFFFFF) |

#### Botão "Copiar"

| Propriedade | Valor | Token / Variável |
|---|---|---|
| Text | "Copiar" | — |
| Text Style | Body/SM Regular | — |
| Fill | text/inverse | VariableID:106:86 (#FFFFFF) |
| Tipo | Frame manual (24×HUG) | — |

> **Nota — padrão Close Buttons:** "Copiar" é um frame manual, não uma instância de BC-05 Button. Mesmo padrão aceito para Close Buttons (×) em Alerts, Toasts e Modals — elementos interativos menores que Button SM (32px). Contexto dark com texto branco não combina com os estilos existentes de BC-05 (desenhados para fundos claros).

### Área de código

| Propriedade | Valor | Token / Variável |
|---|---|---|
| Auto-layout | VERTICAL | — |
| Padding | 16px | `space/4` (VariableID:106:56) |
| Fill (fundo) | text/primary | VariableID:106:83 (#08060F) |
| Width | FILL | — |
| Min height | 80px | — |

#### Texto do código

| Propriedade | Valor | Token / Variável |
|---|---|---|
| Text Style | Mono/SM | — |
| Fill | text/inverse | VariableID:106:86 (#FFFFFF) |
| Resize | FILL × HUG | — |

---

## Estados e variantes

| Variante | Descrição |
|---|---|
| **Default** | Bloco com barra superior + área de código |

> 1 variante apenas. Linguagem e código são overrides de texto na instância.

---

## Composição atômica

> **Regra 11:** "este elemento já existe como componente?"

| Elemento | Existe como componente? | Decisão |
|---|---|---|
| Barra superior | Não — container com layout específico | Frame manual |
| Label linguagem | Não — text node com Label/SM | Frame manual |
| Botão "Copiar" | BC-05 Button Tertiary SM? | **Não usar.** BC-05 foi desenhado para fundos claros (fills de texto usam text/primary, text/secondary). Em contexto dark, o button ficaria invisível ou precisaria de overrides que quebram a integridade do componente. Frame manual (padrão Close Buttons) |
| Texto do código | Não — text node com Mono/SM | Frame manual |

**Resultado:** DC-03 CodeBlock é um componente folha em contexto dark. A decisão de não instanciar BC-05 é deliberada — contexto de cor impede reuso sem quebrar o componente base.

---

## Decisão de tokens dark

O DS SISP não tem modo dark completo. Para o CodeBlock, usamos 2 variáveis existentes de forma criativa:

| Zona | Variável usada | Valor SC | Justificativa |
|---|---|---|---|
| Barra superior | `dark/surface` (106:103) | #192D22 | Variável semântica dark já existente |
| Área de código | `text/primary` (106:83) | #08060F | Quase preto — usado como fill de frame, não de texto |
| Texto (ambas zonas) | `text/inverse` (106:86) | #FFFFFF | Variável semântica para texto sobre fundo escuro |

**Contraste verificado:**
- text/inverse (#FFFFFF) sobre dark/surface (#192D22): ≥ 14:1 ✅ AAA
- text/inverse (#FFFFFF) sobre text/primary (#08060F): ≥ 19:1 ✅ AAA

> **Alternativa descartada:** usar `dark/surface` para ambas as zonas (sem diferenciação visual). A separação com dois tons distintos ajuda a distinguir barra de controle (ações) da área de conteúdo (código).

---

## Regras de acessibilidade (proativas)

- [ ] Container com `role="region"` e `aria-label="Exemplo de código HTML"` (dinâmico por linguagem)
- [ ] Código dentro de `<pre><code>` para semântica
- [ ] Botão "Copiar" com `aria-label="Copiar código para área de transferência"`
- [ ] Após copiar, feedback visual: texto muda para "Copiado!" por 2s
- [ ] Contraste verificado — ≥ 14:1 para todo texto (AAA)
- [ ] Texto do código selecionável (não é imagem)
- [ ] Font mono (Fira Code) para legibilidade de código
- [ ] Labels em português ("Copiar", não "Copy")

---

## Comportamento responsivo

> Regra 13 — Responsividade obrigatória.

| Precisa de variante `Layout=Mobile`? | **Não** — auto-contido, FILL horizontal (100% do container) |
|---|---|
| **Desktop** | Largura do container de conteúdo |
| **Mobile** | Mesma largura (100% do container mobile). Código longo faz scroll horizontal (`overflow-x: auto`) |
| **Tablet** | Segue Desktop |

---

## Casos excepcionais / bordas

- **Código muito longo (horizontal):** scroll horizontal dentro da área de código. Barra superior permanece fixa
- **Código muito longo (vertical):** sem limite de altura no Figma (HUG). Na implementação web, considerar `max-height` com scroll vertical para blocos > 20 linhas
- **Múltiplos CodeBlocks na mesma página:** cada um é instância independente. Sem interação entre eles
- **Linguagem não reconhecida:** label exibe o texto como-está (ex: "Shell", "JSON"). Sem validação

---

## O que está fora deste spec

- **Syntax highlighting (coloração de código):** no Figma, todo o código é branco (text/inverse). Coloração é responsabilidade do portal web (highlight.js ou similar)
- **Múltiplas abas de linguagem (HTML | TS | CSS):** versão futura. V1 tem 1 linguagem por bloco
- **Numeração de linhas:** complexidade visual desnecessária para snippets curtos (3-10 linhas)
- **Edição inline:** CodeBlock é read-only. Não é um editor de código
- **Versão Angular:** DC components são exclusivos do portal

---

## Critérios de aceite

- [ ] Component Set no Figma com 1 variante (Default)
- [ ] Auto-layout vertical, border-radius=radius/lg
- [ ] Barra superior: fill=dark/surface, "HTML" em Label/SM, "Copiar" em Body/SM Regular, ambos fill=text/inverse
- [ ] Área de código: fill=text/primary (como fundo), texto em Mono/SM, fill=text/inverse
- [ ] Clip content=true (border-radius aplicado)
- [ ] Width=FILL (herda do container)
- [ ] Padding bound a space/4, gap implícito (auto-layout vertical sem gap entre barra e código)
- [ ] Fills bound a variáveis de Colors — zero hex hardcoded (Regra 8)
- [ ] Contraste ≥ 14:1 para todo texto (AAA)
- [ ] Exemplo de conteúdo: trecho Angular do Button
- [ ] Revisado e aprovado por Giuliana
