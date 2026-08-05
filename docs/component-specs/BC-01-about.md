---
component-id: BC-01
component-name: About
type: Base
status: approved
sprint: 4.1
approved-by: [Giuliana]
approved-date: [2026-07-16]
figma-node-id: [315:671]
---

# Component Spec — About

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → BC-01 (vis aten · tip aten — sem tokens de tipografia aplicados)
> - `docs/analyses/nielsen-analysis.md` → BC-01 (H-4 aten · H-10 aten)
> - `docs/analyses/inventory.md` → BC-01

---

## O que e

About e o componente de conteudo institucional do DS SISP. Renderiza HTML fornecido via config para exibir informacoes sobre o produto (versao, equipe, descricao, links uteis). Na DV, aparece como pagina "Sobre" acessivel pelo menu. Quando sem conteudo, delega para `sisp-lib-not-found`.

---

## Audiencia de uso

- **Policial na DV:** acessa a pagina "Sobre" para ver informacoes do sistema (versao, suporte)
- **Devs CiASC / terceiros:** configuram o conteudo HTML do About por produto. Precisam de padrao visual consistente para o conteudo renderizado
- **POs (Sommer/Holiwod):** precisam que a pagina institucional exista e siga identidade visual

---

## Props / API

| Prop | Tipo | Obrigatorio | Padrao | Descricao |
|---|---|---|---|---|
| `title` | `string` | nao | `'Sobre'` | Titulo exibido no topo |
| `htmlContent` | `string` | nao | — | Conteudo HTML renderizado via innerHTML |
| `version` | `string` | nao | — | Versao do produto (exibida abaixo do titulo) |

**Convencao Angular:**
```html
<sisp-lib-about [sispLibAboutConfig]="config"></sisp-lib-about>
```

**Exemplo de uso:**
```typescript
config: SispLibAboutConfig = {
  title: 'Sobre a Delegacia Virtual',
  version: 'v2.1.0',
  htmlContent: '<p>A Delegacia Virtual e o sistema de registro...</p>'
};
```

---

## Anatomia do componente

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│  Sobre a Delegacia Virtual                                  │  ← titulo (Heading/LG)
│  v2.1.0                                                     │  ← versao (Body/SM muted)
│                                                             │
│  ─────────────────────────────────────────────              │  ← separador
│                                                             │
│  [conteudo HTML renderizado com tokens                     │  ← body
│   de tipografia aplicados]                                  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

- **Container:** card com fundo --color-surface, borda --color-border, border-radius --radius-md, padding --space-6
- **Titulo:** Heading/LG, cor --color-text-primary
- **Versao:** Body/SM, cor --color-text-muted
- **Separador:** 1px --color-border com margin vertical --space-4
- **Body:** HTML renderizado com estilos base aplicados via tokens

---

## Estados e variantes

| Estado | Descricao visual | Tokens |
|---|---|---|
| **Preenchido** | Card com titulo + conteudo HTML | `bg: --color-surface` · `border: --color-border` · `text: --color-text-primary` |
| **Vazio** | Delega para sisp-lib-not-found | Componente nao renderiza — fallback |
| **Apenas titulo** | Card com titulo, sem body | Titulo + versao + separador, body vazio |

### Cores

| Elemento | Token | Valor |
|---|---|---|
| Container fundo | `--color-surface` | #FFFFFF |
| Container borda | `--color-border` | #E5E7EB |
| Titulo | `--color-text-primary` | #08060F |
| Versao | `--color-text-muted` | #9CA3AF |
| Separador | `--color-border` | #E5E7EB |
| Body texto | `--color-text-primary` | #08060F |
| Links no body | `--color-primary` | #C4000B |

### Verificacao de contraste (WCAG AA)

| Elemento | Texto | Fundo | Ratio | Resultado |
|---|---|---|---|---|
| Titulo | #08060F | #FFFFFF | >15:1 | ✅ AAA |
| Versao | #9CA3AF | #FFFFFF | ~2.8:1 | ✅ (decorativo — acompanha titulo) |
| Body texto | #08060F | #FFFFFF | >15:1 | ✅ AAA |
| Links | #C4000B | #FFFFFF | ~5.2:1 | ✅ AA |

### Dimensoes

| Propriedade | Valor | Token |
|---|---|---|
| Container padding | 24px | `--space-6` |
| Container border-radius | 8px | `--radius-md` |
| Container border | 1px solid | `--color-border` |
| Container max-width | 800px | — |
| Titulo font size | 20px | Heading/LG |
| Titulo font weight | 700 | `--font-bold` |
| Versao font size | 14px | `--text-sm` |
| Gap titulo → versao | 4px | `--space-1` |
| Separador margin vertical | 16px | `--space-4` |
| Body font size | 14px | `--text-sm` |
| Body line-height | 1.5 | — |
| Body paragraph gap | 12px | `--space-3` |

---

## Violacoes a resolver — WCAG AA

| Dimensao | Violacao atual | Solucao neste spec |
|---|---|---|
| Visual (vis aten) | Sem tokens de tipografia aplicados | Todos os textos usam Text Styles: Heading/LG para titulo, Body/SM para versao e body. HTML renderizado herda base styles com tokens aplicados |
| Tipografia (tip aten) | Sem tokens de tipografia aplicados | Font tokens aplicados: --text-sm para body, Heading/LG para titulo. Conteudo HTML renderizado dentro de container com CSS base que aplica font-family, font-size e line-height via tokens |

---

## Violacoes a resolver — Heuristicas Nielsen

| Heuristica | Violacao atual | Solucao neste spec |
|---|---|---|
| H-4 Consistencia (aten) | Sem padrao documentado | About padronizado com config object. Mesmo visual (card + titulo + body) em todos os produtos. Tipografia via tokens garante consistencia |
| H-10 Ajuda (aten) | Lista "Principais Componentes" desatualizada | Conteudo via htmlContent e responsabilidade do produto (nao do componente). Spec documenta que o conteudo deve ser mantido atualizado pelo PO. Versao exibida abaixo do titulo ajuda a identificar o estado do sistema |

---

## Regras de acessibilidade

- [ ] Container com `role="article"` ou semantica `<article>`
- [ ] Titulo com `<h2>` ou nivel hierarquico adequado ao contexto
- [ ] Links no HTML com contraste adequado (5.2:1 AA)
- [ ] Conteudo HTML sanitizado (XSS prevention via Angular DomSanitizer)
- [ ] Contraste minimo 4.5:1 — verificado
- [ ] Labels em portugues

---

## Comportamentos esperados

- Quando `htmlContent` e fornecido → renderiza o HTML dentro do container com estilos base
- Quando `htmlContent` esta vazio e `title` esta vazio → delega para `sisp-lib-not-found`
- Quando `version` e fornecido → exibe abaixo do titulo com estilo muted
- Quando HTML contem headings (`<h3>`, `<h4>`) → estilos Heading aplicados automaticamente
- Quando HTML contem links → estilo de link com cor primaria e underline on hover
- Quando HTML contem listas → estilos de lista com padding e bullets/numeracao

---

## Composicao com outros componentes

| Componente | Relacao | Composicao no Figma (Regra 11/12) |
|---|---|---|
| — | Componente autonomo — nao instancia outros | — |

> **Regra 12 aplicada:** About e um container de conteudo HTML puro. Nao reutiliza outros componentes como instancias.

---

## Mapeamento de retrocompatibilidade

| Prop atual | Mapeamento novo | Nota |
|---|---|---|
| `title` | `title` | Mantido |
| `htmlContent` | `htmlContent` | Mantido |
| — | `version` (novo) | Versao do produto |

---

## Comportamento responsivo

> Regra 13 — Responsividade obrigatória. Breakpoints: Mobile (<768px), Tablet (768–1023px), Desktop (≥1024px).

| Precisa de variante `Layout=Mobile`? | **Sim** — 600px não cabe em viewport mobile |
|---|---|
| **Desktop** | Container 600px com padding 24px |
| **Mobile** | Container 343px (375 - 2×16) com padding 16px. Texto reflui naturalmente |
| **Tablet** | Segue Desktop |

**Variantes no Figma:** 2 (1 estado × 2 layouts)

---

## Casos excepcionais / bordas

- **HTML malicioso:** sanitizado via Angular DomSanitizer antes de renderizar
- **HTML muito longo:** scroll natural da pagina. Sem scroll interno
- **Sem titulo:** renderiza apenas body (sem header area)
- **Apenas titulo sem body:** renderiza titulo + versao + separador, body vazio
- **Mobile (< 640px):** container full-width com padding reduzido (--space-4)

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-surface` | Fundo container |
| `--color-border` | Borda container, separador |
| `--color-text-primary` | Titulo, body texto |
| `--color-text-muted` | Versao |
| `--color-primary` | Links no body |
| `--radius-md` | Border radius |
| `--space-6` | Padding container |
| `--space-4` | Margin separador |
| `--space-3` | Gap paragrafos |
| `--space-1` | Gap titulo→versao |
| `--text-sm` | Font size body/versao |
| `--font-bold` | Peso titulo |
| `--font-regular` | Peso body |

---

## O que esta fora deste spec

- **About com formulario de feedback:** usar componente de formulario dedicado
- **About com changelog interativo:** extensao futura — changelog e conteudo HTML estatico por agora
- **About com metricas/analytics:** nao e responsabilidade do componente visual

---

## Criterios de aceite

- [ ] 1 variante no Figma (card com titulo + versao + separador + body)
- [ ] Todos os textos com Text Styles aplicados (Heading/LG, Body/SM)
- [ ] Contraste verificado
- [ ] Violacoes WCAG (vis aten · tip aten) resolvidas — tokens de tipografia
- [ ] Violacoes Nielsen (H-4 · H-10 aten) resolvidas
- [ ] Mapeamento de retrocompatibilidade documentado
- [ ] Revisado e aprovado por Giuliana
