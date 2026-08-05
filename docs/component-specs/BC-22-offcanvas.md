---
component-id: BC-22
component-name: Offcanvas
type: Base
status: approved
sprint: 4.1
approved-by: [Giuliana]
approved-date: [2026-07-16]
figma-node-id: [315:792]
---

# Component Spec — Offcanvas

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → BC-22 (**vis crit** — trap de foco nao documentado)
> - `docs/analyses/nielsen-analysis.md` → BC-22 (**H-3 crit** · H-4 aten · H-6 aten · H-8 aten)
> - `docs/analyses/inventory.md` → BC-22

---

## O que e

Offcanvas e o componente de painel lateral deslizante do DS SISP. Abre sobre o conteudo principal a partir de uma borda da tela (esquerda, direita, topo ou base) com backdrop semitransparente. Na DV, usado para paineis de filtros avancados, detalhes de registros sem sair do contexto, e menus contextuais em mobile. Funciona como overlay focado — similar a BC-19 Modals, mas com posicionamento lateral e largura parcial.

> **Regra 12 aplicada:** Offcanvas compartilha o padrao de overlay com BC-19 Modals (backdrop, focus trap, close button, ESC para fechar). A diferenca e posicional: Modals sao centralizados, Offcanvas sao ancorados a uma borda. Ambos usam o mesmo close button pattern (frame 24×24 aceito).

---

## Audiencia de uso

- **Policial na DV:** usa offcanvas para acessar filtros avancados em telas de consulta, ver detalhes de um registro sem perder a lista, ou navegar menus em mobile
- **Devs CiASC / terceiros:** usam offcanvas para paineis contextuais que nao justificam uma pagina propria. Precisam configurar posicao, titulo, conteudo e comportamento de backdrop
- **POs (Sommer/Holiwod):** precisam que informacoes complementares sejam acessiveis sem perder o contexto principal

---

## Props / API

| Prop | Tipo | Obrigatorio | Padrao | Descricao |
|---|---|---|---|---|
| `position` | `'start' \| 'end' \| 'top' \| 'bottom'` | nao | `'end'` | Borda de ancoragem do painel |
| `title` | `string` | nao | — | Titulo exibido no header do painel |
| `backdrop` | `boolean` | nao | `true` | Exibe backdrop semitransparente |
| `backdropClose` | `boolean` | nao | `true` | Fechar ao clicar no backdrop |
| `keyboard` | `boolean` | nao | `true` | Fechar com ESC |
| `width` | `string` | nao | `'300px'` | Largura do painel (position start/end) |
| `height` | `string` | nao | `'auto'` | Altura do painel (position top/bottom) |
| `showClose` | `boolean` | nao | `true` | Exibe botao de fechar |

**Convencao Angular:**
```html
<sisp-lib-offcanvas [sispLibOffcanvasConfig]="config"></sisp-lib-offcanvas>
```

**Exemplo de uso:**
```typescript
config: SispLibOffcanvasConfig = {
  position: 'end',
  title: 'Filtros Avancados',
  backdrop: true,
  backdropClose: true,
  keyboard: true,
  width: '360px'
};
```

---

## Anatomia do componente

### Position: end (direita — padrao)
```
┌───────────────────────────┬────────────────────┐
│                           │  Filtros Avancados ×│  ← header (titulo + close)
│      Conteudo principal   ├────────────────────┤
│      (backdrop escuro)    │                    │
│                           │  [conteudo do      │  ← body
│                           │   painel]          │
│                           │                    │
│                           │                    │
└───────────────────────────┴────────────────────┘
         backdrop                offcanvas
```

### Position: start (esquerda)
```
┌────────────────────┬───────────────────────────┐
│ × Menu             │                           │
├────────────────────┤      Conteudo principal   │
│                    │      (backdrop escuro)    │
│  [conteudo do      │                           │
│   painel]          │                           │
│                    │                           │
└────────────────────┴───────────────────────────┘
```

- **Backdrop:** overlay semitransparente sobre o conteudo principal
- **Panel:** painel branco ancorado a uma borda, com shadow
- **Header:** titulo (opcional) + close button (×) alinhado a direita
- **Body:** area de conteudo com scroll interno quando necessario
- **Close button:** frame 24×24 (padrao aceito — mesmo de Modals e Alerts)

---

## Estados e variantes

### Posicoes

| Posicao | Descricao | Ancoragem |
|---|---|---|
| **Start** | Painel a esquerda | Borda esquerda, altura 100vh |
| **End** | Painel a direita (padrao) | Borda direita, altura 100vh |
| **Top** | Painel no topo | Borda superior, largura 100% |
| **Bottom** | Painel na base | Borda inferior, largura 100% |

### Estados

| Estado | Descricao visual | Tokens |
|---|---|---|
| **Closed** | Painel oculto, sem backdrop | Nenhum elemento visivel |
| **Open** | Painel visivel com backdrop | `panel-bg: --color-surface` · `backdrop: rgba(0,0,0,0.5)` · `shadow: --shadow-xl` |
| **Header hover (close)** | Close button com hover | `close-bg: --color-surface-hover` |
| **Header focus (close)** | Focus ring no close button | `outline: 2px solid var(--color-border-focus)` |

### Cores

| Elemento | Token | Valor |
|---|---|---|
| Panel fundo | `--color-surface` | #FFFFFF |
| Panel shadow | `--shadow-xl` | box-shadow do token |
| Backdrop | — | rgba(0,0,0,0.5) |
| Header titulo | `--color-text-primary` | #08060F |
| Header borda inferior | `--color-border` | #E5E7EB |
| Close button | `--color-text-secondary` | #4B5563 |
| Close hover fundo | `--color-surface-hover` | #F3F4F6 |
| Focus ring | `--color-border-focus` | #C4000B |
| Body texto | `--color-text-primary` | #08060F |

### Verificacao de contraste (WCAG AA)

| Elemento | Texto | Fundo | Ratio | Resultado |
|---|---|---|---|---|
| Header titulo | #08060F | #FFFFFF | >15:1 | ✅ AAA |
| Close button | #4B5563 | #FFFFFF | ~7.2:1 | ✅ AAA |
| Body texto | #08060F | #FFFFFF | >15:1 | ✅ AAA |

### Dimensoes

| Propriedade | Valor | Token |
|---|---|---|
| Panel width (start/end) | 300px default, configuravel | — |
| Panel max-width | calc(100vw - 48px) | — |
| Header height | 56px | — |
| Header padding | 16px | `--space-4` |
| Body padding | 16px | `--space-4` |
| Close button size | 24×24px | — |
| Close icon size | 14px | — |
| Header font size | 16px | `--text-base` |
| Header font weight | 600 | `--font-semibold` |
| Header border bottom | 1px solid | `--color-border` |
| Panel border-radius | 0 | — (ancorado a borda) |
| Gap titulo → close | auto (space-between) | — |
| Backdrop z-index | 1040 | — |
| Panel z-index | 1045 | — |

---

## Violacoes a resolver — WCAG AA

| Dimensao | Violacao atual | Solucao neste spec |
|---|---|---|
| **Visual (vis crit)** | **Trap de foco nao documentado** | **Focus trap obrigatorio.** Quando aberto: (1) foco move para o painel (primeiro elemento focavel ou close button), (2) Tab/Shift+Tab cicla apenas dentro do painel, (3) foco nunca escapa para o conteudo por tras do backdrop. Ao fechar: foco retorna ao elemento que abriu o offcanvas. Identico ao padrao de BC-19 Modals |

---

## Violacoes a resolver — Heuristicas Nielsen

| Heuristica | Violacao atual | Solucao neste spec |
|---|---|---|
| **H-3 Controle (crit)** | **Trap de foco nao documentado** | 3 formas de fechar: (1) close button (×), (2) ESC, (3) clique no backdrop (quando backdropClose=true). Focus trap garante que o usuario nao fica preso — sempre ha caminho de saida claro. Mesmo padrao de BC-19 Modals |
| H-4 Consistencia (aten) | Sem padrao documentado entre produtos | Offcanvas padronizado com config object. Mesmo visual e comportamento em todos os produtos. Padrao de overlay consistente com Modals (backdrop + focus trap + ESC) |
| H-6 Reconhecimento (aten) | Funcionalidade do backdrop nao obvia | Backdrop escuro indica claramente que o conteudo principal esta bloqueado. Close button (×) visivel e padrao. Titulo no header identifica o contexto do painel |
| H-8 Estetica (aten) | Visual generico | Shadow-xl no painel cria profundidade. Header com borda sutil separa titulo do conteudo. Backdrop semitransparente mantem referencia visual ao conteudo principal |

---

## Regras de acessibilidade

- [ ] Panel com `role="dialog"` e `aria-modal="true"`
- [ ] `aria-labelledby` apontando para o titulo (quando presente)
- [ ] `aria-label` como fallback quando sem titulo
- [ ] **Focus trap obrigatorio (vis crit resolvido):**
  - Ao abrir: foco move para primeiro elemento focavel do painel
  - Tab/Shift+Tab cicla dentro do painel
  - Ao fechar: foco retorna ao trigger
- [ ] ESC fecha o painel (quando keyboard=true)
- [ ] Close button com `aria-label="Fechar"`
- [ ] Backdrop com `aria-hidden="true"`
- [ ] Conteudo por tras do painel com `aria-hidden="true"` e `inert` quando aberto
- [ ] Focus ring visivel: `2px solid var(--color-border-focus)`
- [ ] Contraste minimo 4.5:1 — verificado
- [ ] Labels em portugues

---

## Comportamentos esperados

- Quando offcanvas abre → painel desliza da borda configurada, backdrop aparece, foco move para o painel
- Quando usuario clica no close button (×) → painel fecha, foco retorna ao trigger
- Quando usuario pressiona ESC (keyboard=true) → painel fecha
- Quando usuario clica no backdrop (backdropClose=true) → painel fecha
- Quando `backdrop = false` → sem overlay, conteudo principal interativo (modo painel nao-modal)
- Quando `position = 'start'` → painel desliza da esquerda
- Quando `position = 'end'` → painel desliza da direita (padrao)
- Quando `position = 'top'` → painel desliza do topo, largura 100%
- Quando `position = 'bottom'` → painel desliza da base, largura 100%
- Quando conteudo do body excede a altura → scroll interno no body (header fixo)
- Quando `showClose = false` → close button nao aparece (fechar via backdrop ou ESC apenas)
- Quando viewport < 640px (mobile) → painel usa largura total (100vw) independente do width configurado

---

## Composicao com outros componentes

| Componente | Relacao | Composicao no Figma (Regra 11/12) |
|---|---|---|
| **BC-19 Modals** | Compartilha padrao de overlay: backdrop, focus trap, close button, ESC | Padrao de comportamento — nao instancia visual |
| **BC-20 Navigation Canvas** | Nav Canvas pode ser renderizado dentro de Offcanvas em mobile | Composicao de uso |

> **Regra 12 aplicada:** Offcanvas e visualmente distinto de Modals (lateral vs. central, altura 100vh vs. auto), mas compartilha o padrao comportamental de overlay (backdrop + focus trap + ESC). Close button segue o padrao aceito de frame 24×24. Nao usa instancias de BC-05 Button (close e menor que Button SM 32px).

---

## Mapeamento de retrocompatibilidade

| Prop atual | Mapeamento novo | Nota |
|---|---|---|
| `position` | `position` | Mantido. Valores: start/end/top/bottom |
| `title` | `title` | Mantido |
| `backdrop` | `backdrop` | Mantido |
| — | `backdropClose` (novo) | Controla se clique no backdrop fecha |
| — | `keyboard` (novo) | Controla se ESC fecha |
| — | `width` (novo) | Largura configuravel |
| — | `height` (novo) | Altura configuravel (top/bottom) |
| — | `showClose` (novo) | Controla visibilidade do close button |

---

## Comportamento responsivo

> Regra 13 — Responsividade obrigatória. Breakpoints: Mobile (<768px), Tablet (768–1023px), Desktop (≥1024px).

| Precisa de variante `Layout=Mobile`? | **Sim** — painel lateral não cabe em viewport mobile |
|---|---|
| **Desktop** | Painel lateral 320px (dentro do frame 800px com conteúdo). Desliza da esquerda (Start) ou direita (End) |
| **Mobile** | Tela cheia (375px / 100% viewport). Painel ocupa toda a largura |
| **Tablet** | Segue Desktop |

**Variantes no Figma:** 4 (2 posições × 2 layouts)

---

## Casos excepcionais / bordas

- **Sem titulo e sem close:** painel renderiza apenas body. Usuario fecha via backdrop ou ESC. Nao recomendado — sempre fornecer pelo menos uma forma visual de fechar
- **Conteudo vazio:** painel renderiza header + body vazio com padding
- **Width > viewport:** limitado a calc(100vw - 48px) para sempre permitir clique no backdrop
- **Multiple offcanvas:** apenas um offcanvas visivel por vez. Abrir segundo fecha o primeiro
- **Mobile (< 640px):** painel usa largura 100vw para start/end. Touch swipe horizontal para fechar (opcional — melhoria futura)
- **Backdrop desabilitado (backdrop=false):** painel abre sem overlay — conteudo principal continua interativo. Nao ha focus trap neste modo
- **Transicao:** slide-in 200ms ease. Backdrop fade 150ms

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-surface` | Fundo do painel |
| `--color-text-primary` | Texto header e body |
| `--color-text-secondary` | Close button |
| `--color-border` | Borda inferior header |
| `--color-surface-hover` | Close button hover |
| `--color-border-focus` | Focus ring |
| `--shadow-xl` | Shadow do painel |
| `--text-base` | Font size header (16px) |
| `--text-sm` | Font size body (14px) |
| `--font-semibold` | Peso header (600) |
| `--font-regular` | Peso body (400) |
| `--space-4` | Padding header e body |

---

## O que esta fora deste spec

- **Offcanvas com tabs internas:** usar BC-26 Tabs dentro do body — composicao de uso
- **Offcanvas com formulario completo:** usar dentro do body — responsabilidade do conteudo
- **Offcanvas persistente (sem fechar):** usar BC-20 Navigation Canvas para navegacao lateral permanente
- **Offcanvas com resize (drag):** nao e padrao SISP
- **Offcanvas nested (offcanvas dentro de offcanvas):** nao suportado
- **Animacoes de transicao customizadas:** implementacao Angular define

---

## Criterios de aceite

- [ ] 4 variantes no Figma por posicao: Start, End, Top, Bottom (minimo End como padrao)
- [ ] Header com titulo + close button (frame 24×24)
- [ ] Backdrop semitransparente (rgba(0,0,0,0.5))
- [ ] Shadow-xl no painel
- [ ] **Focus trap documentado (vis crit resolvido)**
- [ ] **3 formas de fechar (H-3 crit resolvido):** close, ESC, backdrop
- [ ] Contraste verificado — >15:1 AAA para texto
- [ ] ARIA documentado: role="dialog", aria-modal, aria-labelledby
- [ ] Navegacao por teclado documentada (Tab cycle, ESC)
- [ ] Violacoes WCAG (vis crit) resolvidas — focus trap
- [ ] Violacoes Nielsen (H-3 crit · H-4 · H-6 · H-8 aten) resolvidas
- [ ] Relacao com BC-19 Modals documentada (padrao overlay compartilhado)
- [ ] Mapeamento de retrocompatibilidade documentado
- [ ] Revisado e aprovado por Giuliana
