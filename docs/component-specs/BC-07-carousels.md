---
component-id: BC-07
component-name: Carousels
type: Base
status: approved
sprint: 4.1
approved-by: [Giuliana]
approved-date: [2026-07-16]
figma-node-id: [315:820]
---

# Component Spec — Carousels

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → BC-07 (cor aten · **vis crit** — setas sem contraste sobre imagens, sem keyboard nav)
> - `docs/analyses/nielsen-analysis.md` → BC-07 (H-1 aten · **H-3 crit** · H-4 aten · H-5 aten · H-6 aten · **H-8 crit**)
> - `docs/analyses/inventory.md` → BC-07

---

## O que e

Carousel e o componente de apresentacao sequencial de slides do DS SISP. Exibe uma serie de conteudos (imagens, cards, banners) com navegacao por setas e indicadores de posicao. Na DV, usado para galerias de imagens de ocorrencias (fotos capturadas pelo policial) e banners informativos no painel. Componente com **multiplas violacoes criticas** de acessibilidade e usabilidade — este spec corrige todas.

> **Nota critica:** Carousels com autoplay sao um anti-padrao reconhecido de usabilidade (Nielsen H-8 crit). Este spec implementa autoplay como **desativado por padrao** com controle explicito de pausa, seguindo WCAG 2.2.2 (Pause, Stop, Hide).

---

## Audiencia de uso

- **Policial na DV:** navega entre fotos de ocorrencias capturadas, banners informativos no painel
- **Devs CiASC / terceiros:** usam carousel para galerias e banners. Precisam de controle sobre autoplay, navegacao por teclado, e indicadores de posicao
- **POs (Sommer/Holiwod):** precisam que galerias de imagens funcionem sem frustrar o usuario (sem movimento involuntario)

---

## Props / API

| Prop | Tipo | Obrigatorio | Padrao | Descricao |
|---|---|---|---|---|
| `items` | `CarouselItem[]` | sim | — | Lista de slides |
| `autoPlay` | `boolean` | nao | `false` | Rotacao automatica de slides. **Desativado por padrao** |
| `interval` | `number` | nao | `5000` | Intervalo entre slides em ms (quando autoPlay=true) |
| `pauseOnHover` | `boolean` | nao | `true` | Pausa autoplay ao hover (quando autoPlay=true) |
| `showControls` | `boolean` | nao | `true` | Exibe setas de navegacao |
| `showIndicators` | `boolean` | nao | `true` | Exibe indicadores de posicao (dots) |
| `wrap` | `boolean` | nao | `true` | Loop infinito (apos ultimo slide, volta ao primeiro) |
| `keyboard` | `boolean` | nao | `true` | Navegacao por teclado (←/→) |

**CarouselItem:**
```typescript
interface CarouselItem {
  content: string | TemplateRef;  // Conteudo do slide (imagem, template)
  alt?: string;                   // Texto alternativo (obrigatorio para imagens)
  caption?: string;               // Legenda opcional abaixo da imagem
}
```

**Convencao Angular:**
```html
<sisp-lib-carousel [sispLibCarouselConfig]="config"></sisp-lib-carousel>
```

**Exemplo de uso:**
```typescript
config: SispLibCarouselConfig = {
  autoPlay: false,
  showControls: true,
  showIndicators: true,
  items: [
    { content: fotoOcorrencia1, alt: 'Foto da ocorrencia - vista frontal', caption: 'Vista frontal' },
    { content: fotoOcorrencia2, alt: 'Foto da ocorrencia - vista lateral', caption: 'Vista lateral' },
    { content: fotoOcorrencia3, alt: 'Foto da ocorrencia - detalhe', caption: 'Detalhe do dano' }
  ]
};
```

---

## Anatomia do componente

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│    ◀    [        Conteudo do Slide (imagem/card)       ]  ▶  │  ← setas de navegacao
│                                                             │
│                      Vista frontal                          │  ← caption (opcional)
│                                                             │
│                       ● ○ ○                                 │  ← indicadores (dots)
│                                                             │
│                   ▶ / ❚❚                                    │  ← botao play/pause (quando autoPlay disponivel)
└─────────────────────────────────────────────────────────────┘
```

- **Container:** frame com overflow hidden, border-radius --radius-lg
- **Slide area:** conteudo do slide atual (imagem, card, ou template)
- **Setas:** botoes de navegacao esquerda/direita com fundo semitransparente
- **Indicadores:** dots que mostram posicao atual e total de slides
- **Caption:** texto descritivo abaixo do slide
- **Play/Pause:** botao de controle de autoplay (visivel apenas quando autoPlay=true)

---

## Estados e variantes

### Controles de navegacao (setas)

| Estado | Descricao visual | Tokens |
|---|---|---|
| **Default** | Seta com fundo semitransparente | `bg: rgba(255,255,255,0.8)` · `icon: --color-text-primary` · `border: 1px solid --color-border` |
| **Hover** | Fundo opaco | `bg: --color-surface` · `shadow: --shadow-sm` |
| **Focus** | Focus ring | `outline: 2px solid var(--color-border-focus)` · `outline-offset: 2px` |
| **Disabled** | Quando wrap=false e no primeiro/ultimo slide | `opacity: 0.3` · `cursor: not-allowed` |

### Indicadores (dots)

| Estado | Descricao visual | Tokens |
|---|---|---|
| **Inactive** | Dot pequeno e sutil | `bg: --color-border` · `size: 8px` · `border-radius: 50%` |
| **Active** | Dot expandido e contrastado | `bg: --color-primary` · `size: 8px width 24px` · `border-radius: 4px` |

### Cores

| Elemento | Token | Valor |
|---|---|---|
| Container fundo | `--color-surface-muted` | #F9FAFB |
| Container borda | `--color-border` | #E5E7EB |
| Seta fundo | — | rgba(255,255,255,0.8) |
| Seta fundo hover | `--color-surface` | #FFFFFF |
| Seta icone | `--color-text-primary` | #08060F |
| Seta borda | `--color-border` | #E5E7EB |
| Indicator ativo | `--color-primary` | #C4000B |
| Indicator inativo | `--color-border` | #E5E7EB |
| Caption texto | `--color-text-secondary` | #4B5563 |
| Play/Pause icone | `--color-text-secondary` | #4B5563 |
| Focus ring | `--color-border-focus` | #C4000B |

### Verificacao de contraste (WCAG AA)

| Elemento | Texto/Icone | Fundo | Ratio | Resultado |
|---|---|---|---|---|
| Seta icone (default) | #08060F | rgba(255,255,255,0.8) | >12:1 | ✅ AAA |
| Seta icone (hover) | #08060F | #FFFFFF | >15:1 | ✅ AAA |
| Indicator ativo | #C4000B | #F9FAFB | ~5.0:1 | ✅ AA (grafico 3:1) |
| Caption | #4B5563 | #F9FAFB | ~6.5:1 | ✅ AAA |
| Seta borda vs fundo | #E5E7EB | rgba(255,255,255,0.8) | ~1.2:1 | ✅ (reforçado por icone alto contraste) |

### Dimensoes

| Propriedade | Valor | Token |
|---|---|---|
| Container border-radius | 12px | `--radius-lg` |
| Container border | 1px solid | `--color-border` |
| Seta button size | 40×40px | — |
| Seta border-radius | 50% (circulo) | `--radius-full` |
| Seta icon size | 16px | — |
| Seta margin lateral | 12px | `--space-3` |
| Indicator dot height | 8px | — |
| Indicator dot width (inactive) | 8px | — |
| Indicator dot width (active) | 24px | — |
| Indicator gap | 8px | `--space-2` |
| Indicator margin bottom | 16px | `--space-4` |
| Caption font size | 14px | `--text-sm` |
| Caption font weight | 400 | `--font-regular` |
| Caption padding | 8px 16px | `--space-2 --space-4` |
| Play/Pause button size | 32×32px | — |
| Play/Pause margin | 8px | `--space-2` |

---

## Violacoes a resolver — WCAG AA

| Dimensao | Violacao atual | Solucao neste spec |
|---|---|---|
| Uso de cor (cor aten) | Setas sem contraste suficiente sobre imagens | Setas com fundo semitransparente branco (rgba 255,255,255,0.8) + borda 1px. Icone preto (#08060F) sobre fundo branco garante >12:1. Fundo semitransparente isola a seta do conteudo da imagem — contraste nao depende da imagem subjacente |
| **Visual (vis crit)** | **Setas sem contraste sobre imagens. Sem keyboard nav documentado** | **(1) Setas isoladas com fundo branco** — contraste independente da imagem. **(2) Navegacao por teclado completa:** ←/→ para navegar slides, Tab para acessar controles, Enter/Space para play/pause. **(3) Focus ring visivel** em todos os controles interativos. **(4) Botao play/pause** quando autoPlay ativo — WCAG 2.2.2 |

---

## Violacoes a resolver — Heuristicas Nielsen

| Heuristica | Violacao atual | Solucao neste spec |
|---|---|---|
| H-1 Visibilidade (aten) | Slide ativo nao claramente indicado | Indicadores de posicao (dots) mostram slide atual vs. total. Dot ativo expandido (24px) + cor primaria. Counter textual opcional ("2 de 5") para screen readers via aria-live |
| **H-3 Controle (crit)** | **Sem controles de pausa, sem indicacao de quantos slides** | **autoPlay desativado por padrao.** Quando ativado: (1) botao play/pause visivel e acessivel, (2) pausa ao hover, (3) pausa ao foco. Indicadores mostram quantidade e posicao. Setas permitem navegacao direta a qualquer momento |
| H-4 Consistencia (aten) | Sem padrao documentado | Carousel padronizado com config object. Setas e indicadores consistentes. Mesmo visual em todos os produtos |
| H-5 Prevencao de erros (aten) | Navegacao pode causar perda de contexto | wrap=true por padrao (nao ha "beco sem saida"). Transicao suave (300ms) da feedback visual. Indicadores mostram progresso. Caption mantem contexto textual |
| H-6 Reconhecimento (aten) | Setas e indicadores precisam ser descobertos | Setas sempre visiveis (nao aparecem apenas no hover). Indicadores sempre visiveis. Fundo semitransparente das setas indica interatividade. Cursor pointer nos controles |
| **H-8 Estetica (crit)** | **Movimento automatico sem controle = design intrusivo** | **autoPlay=false por padrao** — movimento apenas quando usuario solicita. Quando autoPlay ativado: botao play/pause proeminente, intervalo minimo 5s, pausa ao hover/foco. Respeita `prefers-reduced-motion` — desativa transicoes animadas |

---

## Regras de acessibilidade

- [ ] Container com `role="region"` e `aria-roledescription="carrossel"` e `aria-label` descritivo
- [ ] Cada slide com `role="group"` e `aria-roledescription="slide"` e `aria-label="Slide N de M"`
- [ ] `aria-live="polite"` no container de slides (anuncia mudanca de slide)
- [ ] Setas com `aria-label="Slide anterior"` e `aria-label="Proximo slide"`
- [ ] Indicadores com `aria-label="Ir para slide N"` e `aria-current="true"` no ativo
- [ ] **Botao play/pause** com `aria-label="Pausar rotacao automatica"` / `"Retomar rotacao automatica"`
- [ ] **WCAG 2.2.2 (Pause, Stop, Hide):** autoPlay desativado por padrao. Quando ativo, botao de pausa obrigatorio
- [ ] **Navegacao por teclado:**
  - `←` / `→` navega entre slides
  - `Tab` move entre controles (setas, indicators, play/pause)
  - `Enter` / `Space` ativa controle focado (play/pause, indicador)
- [ ] Focus ring visivel: `2px solid var(--color-border-focus)`
- [ ] `prefers-reduced-motion: reduce` → desativa transicoes animadas, desativa autoplay
- [ ] Imagens com `alt` descritivo obrigatorio
- [ ] Contraste minimo 4.5:1 — verificado (setas com fundo branco)
- [ ] Labels em portugues

---

## Comportamentos esperados

- Quando usuario clica na seta direita (▶) → proximo slide com transicao horizontal (300ms)
- Quando usuario clica na seta esquerda (◀) → slide anterior
- Quando `wrap = true` e usuario avanca apos ultimo slide → volta ao primeiro (loop)
- Quando `wrap = false` e usuario esta no primeiro/ultimo slide → seta correspondente fica disabled
- Quando usuario clica em indicador (dot) → navega diretamente para o slide correspondente
- Quando `autoPlay = true` → slides rotacionam automaticamente no intervalo definido
- Quando `autoPlay = true` e usuario faz hover → autoplay pausa
- Quando `autoPlay = true` e controle recebe foco → autoplay pausa
- Quando usuario clica no botao play/pause → alterna entre rotacao automatica e pausa
- Quando slide tem `caption` → legenda exibida abaixo do conteudo
- Quando `showControls = false` → setas ocultas (navegacao por indicadores, teclado ou touch)
- Quando `showIndicators = false` → dots ocultos
- Quando `prefers-reduced-motion: reduce` → transicoes instantaneas (sem animacao), autoplay desativado
- Quando viewport < 640px (mobile) → touch swipe horizontal navega entre slides. Setas menores (32px)

---

## Composicao com outros componentes

| Componente | Relacao | Composicao no Figma (Regra 11/12) |
|---|---|---|
| **BC-05 Buttons** | Setas de navegacao como botoes circulares | Padrao visual adaptado — nao instancia direta (botoes circulares 40px nao existem no DS como variante formal) |
| **BC-15 Icons** | Icones de seta (chevron-left/right) e play/pause | Font Awesome — `aria-hidden="true"` |

> **Regra 12 aplicada:** as setas de navegacao do Carousel sao botoes circulares especificos (40×40px, border-radius full). Nao sao instancias de BC-05 Button porque: (1) Button nao tem variante circular, (2) tamanho 40px nao corresponde a nenhum size do Button (SM=32, MD=40, LG=48 — MD e 40px mas nao circular). Candidato a micro-componente futuro (Icon Button circular) se padrao se repetir. No Figma, setas sao frames manuais.

---

## Mapeamento de retrocompatibilidade

| Prop atual | Mapeamento novo | Nota |
|---|---|---|
| `items` | `items: CarouselItem[]` | Extendido com `alt`, `caption` |
| `autoPlay` | `autoPlay` | Mantido. **Padrao alterado para false** |
| `interval` | `interval` | Mantido |
| — | `pauseOnHover` (novo) | Pausa autoplay ao hover |
| — | `showControls` (novo) | Controla visibilidade das setas |
| — | `showIndicators` (novo) | Controla visibilidade dos indicators |
| — | `wrap` (novo) | Loop infinito |
| — | `keyboard` (novo) | Navegacao por teclado |

**Breaking change:** `autoPlay` muda padrao de `true` para `false`. Produtos que dependiam de autoplay implicito precisam definir `autoPlay: true` explicitamente.

---

## Comportamento responsivo

> Regra 13 — Responsividade obrigatória. Breakpoints: Mobile (<768px), Tablet (768–1023px), Desktop (≥1024px).

| Precisa de variante `Layout=Mobile`? | **Sim** — múltiplos itens não cabem |
|---|---|
| **Desktop** | 480px, múltiplos itens visíveis com setas |
| **Mobile** | 343px, 1 item por vez. Setas mantidas |
| **Tablet** | Segue Desktop |

**Variantes no Figma:** 2 (1 estado × 2 layouts)

---

## Casos excepcionais / bordas

- **0 items:** componente nao renderiza
- **1 item:** renderiza o slide sem setas e sem indicadores. Sem autoplay
- **2 items:** renderiza com setas e 2 dots. wrap funciona normalmente
- **Imagem sem alt:** console.warn em dev mode. alt default "Slide N" (fallback — nao ideal)
- **Imagem muito grande:** container com overflow hidden e object-fit cover. Aspect ratio mantido
- **Conteudo nao-imagem (template):** slide renderiza o template Angular dentro do container
- **Mobile (< 640px):** touch swipe suportado. Setas 32px. Indicadores menores. Touch targets >= 44px
- **Muitos slides (> 10):** indicadores compactam (mostram subset com "..." ou scroll horizontal)
- **Transicao rapida (cliques rapidos):** debounce de 100ms entre navegacoes. Animacao anterior cancela

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-surface` | Fundo seta hover |
| `--color-surface-muted` | Fundo container |
| `--color-text-primary` | Icone setas |
| `--color-text-secondary` | Caption, play/pause |
| `--color-border` | Borda container, setas, indicators inativos |
| `--color-primary` | Indicator ativo |
| `--color-border-focus` | Focus ring |
| `--radius-lg` | Border radius container |
| `--radius-full` | Border radius setas (circulo) |
| `--shadow-sm` | Shadow seta hover |
| `--text-sm` | Font size caption |
| `--font-regular` | Peso caption |
| `--space-2` | Gap indicators, padding caption vertical |
| `--space-3` | Margin lateral setas |
| `--space-4` | Margin bottom indicators, padding caption horizontal |

---

## O que esta fora deste spec

- **Carousel com thumbnails (filmstrip):** extensao futura — seria um modo de preview com miniaturas abaixo
- **Carousel vertical:** nao e padrao SISP — scroll vertical natural da pagina e preferido
- **Carousel com zoom:** usar componente de image viewer dedicado para zoom/pan
- **Carousel com video:** slides de video sao template Angular — controles de video sao do browser
- **Carousel como slider de comparacao (before/after):** componente diferente
- **Lightbox (fullscreen gallery):** componente dedicado futuro

---

## Criterios de aceite

- [ ] 1 variante principal no Figma com setas, indicadores e caption
- [ ] Setas com fundo semitransparente branco — **contraste independente da imagem** (vis crit resolvido)
- [ ] Indicadores com dot ativo expandido (24px) + cor primaria
- [ ] **autoPlay desativado por padrao** (H-8 crit resolvido)
- [ ] **Botao play/pause** documentado quando autoPlay ativo (H-3 crit resolvido)
- [ ] Contraste verificado — >12:1 AAA para setas
- [ ] ARIA documentado: role="region", aria-roledescription, aria-live, aria-current
- [ ] Navegacao por teclado documentada (←/→, Tab, Enter)
- [ ] WCAG 2.2.2 (Pause, Stop, Hide) atendido
- [ ] prefers-reduced-motion documentado
- [ ] Violacoes WCAG (cor aten · vis crit) resolvidas
- [ ] Violacoes Nielsen (H-1 · H-3 crit · H-4 · H-5 · H-6 · H-8 crit) resolvidas
- [ ] Breaking change documentado (autoPlay default false)
- [ ] Mapeamento de retrocompatibilidade documentado
- [ ] Revisado e aprovado por Giuliana
