---
component-id: BC-23
component-name: Popovers
type: Base
status: approved
sprint: 4.1
approved-by: [Giuliana]
approved-date: [2026-07-16]
figma-node-id: [266:536]
---

# Component Spec — Popovers

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → BC-23 (vis aten · tip aten — tamanho minimo de texto nao garantido)
> - `docs/analyses/nielsen-analysis.md` → BC-23 (H-3 aten · H-4 aten · H-6 aten · H-8 aten)
> - `docs/analyses/inventory.md` → BC-23

---

## O que e

Popover e o componente de informacao contextual flutuante do DS SISP. Exibe conteudo adicional (texto explicativo, dica, detalhes) ancorado a um elemento trigger — tipicamente um icone (i) ou link de ajuda. Na DV, popovers aparecem em campos de formulario com instrucoes (ex: "formato CPF"), tooltips informativos em tabelas, e detalhes de status. Diferente do Tooltip (texto simples, hover only): Popover suporta conteudo rico (titulo + corpo + links) e pode ser acionado por click.

> **Regra 12 aplicada:** popover e componente autonomo (floating panel). Nao reutiliza outros componentes como instancias — conteudo e texto/HTML nativo. Trigger e definido pelo contexto de uso (icone (i), link, botao).

---

## Audiencia de uso

- **Policial na DV:** ve popovers ao interagir com campos de formulario (instrucoes de preenchimento), icones informativos em tabelas, e detalhes de status de BOs
- **Devs CiASC / terceiros:** usam popovers para contextualizar elementos da interface sem poluir o layout. Precisam de API com conteudo, trigger type e posicionamento
- **POs (Sommer/Holiwod):** precisam que informacoes de ajuda estejam acessiveis sem sobrecarregar a interface

---

## Props / API

| Prop | Tipo | Obrigatorio | Padrao | Descricao |
|---|---|---|---|---|
| `content` | `string` | sim | — | Texto do corpo do popover |
| `title` | `string` | nao | — | Titulo do popover (exibido em bold acima do conteudo) |
| `trigger` | `'hover' \| 'click' \| 'focus'` | nao | `'click'` | Evento que abre o popover |
| `placement` | `'top' \| 'bottom' \| 'left' \| 'right'` | nao | `'top'` | Posicao em relacao ao elemento trigger |
| `dismissible` | `boolean` | nao | `true` | Exibe botao de fechar (x) quando trigger = 'click' |
| `maxWidth` | `number` | nao | `280` | Largura maxima do popover em pixels |

**Convencao Angular:**
```html
<sisp-lib-popover [sispLibPopoverConfig]="config"></sisp-lib-popover>
```

**Exemplo de uso:**
```typescript
// Popover informativo em campo de formulario
helpConfig: SispLibPopoverConfig = {
  title: 'Formato do CPF',
  content: 'Digite os 11 digitos sem pontos ou tracos. O sistema aplica a mascara automaticamente.',
  trigger: 'click',
  placement: 'right'
};

// Tooltip simples (hover)
tooltipConfig: SispLibPopoverConfig = {
  content: 'Numero do boletim de ocorrencia',
  trigger: 'hover',
  placement: 'top',
  dismissible: false
};
```

---

## Anatomia do componente

### Com titulo
```
        ▼ (seta apontando para trigger)
┌──────────────────────────────┐
│  Formato do CPF          [×] │  ← titulo (bold) + close button
│  ──────────────────────────  │  ← divider
│  Digite os 11 digitos sem    │
│  pontos ou tracos.           │  ← corpo (texto regular)
└──────────────────────────────┘
```

### Sem titulo
```
        ▼
┌──────────────────────────────┐
│  Numero do boletim de        │
│  ocorrencia                  │  ← apenas corpo
└──────────────────────────────┘
```

- **Container:** fundo branco, sombra, borda, border-radius, seta direcional
- **Titulo (opcional):** texto bold no topo
- **Divider (quando titulo presente):** linha separadora entre titulo e corpo
- **Corpo:** texto regular, pode conter multiplas linhas
- **Close button (quando click trigger):** botao (x) no canto superior direito

---

## Estados e variantes

| Estado / Variante | Descricao visual | Tokens usados |
|---|---|---|
| **Hidden** | Popover invisivel, apenas trigger visivel | — |
| **Visible** | Popover aberto com conteudo | `bg: --color-surface` · `text: --color-text-primary` |
| **With title** | Popover com titulo + divider + corpo | `titulo: --font-semibold` · `--text-sm` |
| **Without title** | Popover apenas com corpo | `corpo: --font-regular` · `--text-sm` |

### Placements

| Placement | Seta | Posicao |
|---|---|---|
| `top` | ▼ (seta na base, aponta para trigger abaixo) | Acima do trigger |
| `bottom` | ▲ (seta no topo, aponta para trigger acima) | Abaixo do trigger |
| `left` | ► (seta na direita, aponta para trigger a direita) | A esquerda do trigger |
| `right` | ◄ (seta na esquerda, aponta para trigger a esquerda) | A direita do trigger |

### Cores

| Elemento | Token | Valor |
|---|---|---|
| Fundo | `--color-surface` | #FFFFFF |
| Borda | `--color-border` | #E5E7EB |
| Sombra | `--shadow-md` | Effect Style |
| Titulo | `--color-text-primary` | #08060F |
| Corpo | `--color-text-secondary` | #4B5563 |
| Divider | `--color-border` | #E5E7EB |
| Seta | `--color-surface` (fill) + `--color-border` (stroke) | #FFFFFF / #E5E7EB |
| Close button | `--color-text-muted` | #9CA3AF |
| Close hover | `--color-text-primary` | #08060F |

### Verificacao de contraste (WCAG AA)

| Elemento | Texto | Fundo | Ratio | Resultado |
|---|---|---|---|---|
| Titulo | #08060F | #FFFFFF | >15:1 | ✅ AAA |
| Corpo | #4B5563 | #FFFFFF | ~7.2:1 | ✅ AAA |
| Close button | #9CA3AF | #FFFFFF | ~2.9:1 | Decorativo (icone com aria-label) |
| Close hover | #08060F | #FFFFFF | >15:1 | ✅ AAA |

### Dimensoes

| Propriedade | Valor | Token |
|---|---|---|
| Min width | 160px | — |
| Max width | 280px (default, configuravel) | — |
| Padding | 12px | `--space-3` |
| Gap titulo → corpo | 8px | `--space-2` |
| Titulo font size | 14px | `--text-sm` |
| Titulo font weight | 600 | `--font-semibold` |
| Corpo font size | 14px | `--text-sm` |
| Corpo font weight | 400 | `--font-regular` |
| Corpo line height | 1.5 | `--leading-normal` |
| Border radius | 8px | `--radius-lg` |
| Seta tamanho | 8px | — |
| Close button | 24×24px | — |
| Offset trigger → popover | 8px | `--space-2` |
| Divider height | 1px | — |

---

## Violacoes a resolver — WCAG AA

| Dimensao | Violacao atual | Solucao neste spec |
|---|---|---|
| Visual (vis aten) | Sem refinamento visual | Container com sombra --shadow-md, border-radius --radius-lg, padding e dimensoes por tokens. Seta direcional indica relacao com trigger. Close button para popover click |
| Tipografia (tip aten) | Tamanho minimo de texto nao garantido | Font size minimo --text-sm (14px) para titulo e corpo. Line height --leading-normal (1.5) para legibilidade. Contraste corpo 7.2:1 (AAA) |

---

## Violacoes a resolver — Heuristicas Nielsen

| Heuristica | Violacao atual | Solucao neste spec |
|---|---|---|
| H-3 Controle (aten) | Sem documentacao de como fechar | Click trigger → abre/fecha (toggle). Click fora → fecha. Escape → fecha. Close button (x) → fecha. Hover trigger → delay 300ms para abrir, 200ms para fechar (evita flicker). Multiplos canais de saida |
| H-4 Consistencia (aten) | Sem padrao documentado | 3 triggers padronizados (click, hover, focus). Posicionamento configuravel. Mesmo visual em todo o DS. Segue padrao Bootstrap/Popper familiar |
| H-6 Reconhecimento (aten) | Conteudo pode ser dificil de encontrar | Seta direcional indica de onde vem a informacao. Titulo opcional contextualiza. Popover aparece proximo ao trigger — relacao visual clara |
| H-8 Estetica (aten) | Visual generico | Sombra --shadow-md, borda sutil, border-radius --radius-lg, espacamento consistente por tokens. Tipografia com hierarquia (titulo bold + corpo regular) |

---

## Regras de acessibilidade

- [ ] Trigger com `aria-describedby` apontando para o ID do popover
- [ ] Popover com `role="tooltip"` (quando trigger = hover/focus) ou `role="dialog"` (quando trigger = click com close button)
- [ ] Quando `role="dialog"`: close button com `aria-label="Fechar"`
- [ ] Quando `role="dialog"`: foco move para o popover ao abrir, retorna ao trigger ao fechar
- [ ] Quando `role="tooltip"`: sem focus trap — foco permanece no trigger
- [ ] `Escape` fecha o popover em todos os modos
- [ ] Conteudo acessivel por screen reader via `aria-describedby`
- [ ] Focus ring visivel no trigger: `2px solid var(--color-border-focus)`
- [ ] Contraste minimo 4.5:1 em todos os textos — verificado (titulo >15:1, corpo 7.2:1)
- [ ] Font size minimo 14px (--text-sm) — resolve tip aten
- [ ] `prefers-reduced-motion`: sem animacao de entrada/saida

---

## Comportamentos esperados

- Quando `trigger = 'click'` e usuario clica no trigger → popover abre. Click novamente → fecha (toggle)
- Quando `trigger = 'click'` e usuario clica fora → popover fecha
- Quando `trigger = 'hover'` e mouse entra no trigger → popover abre apos 300ms delay (evita flicker acidental)
- Quando `trigger = 'hover'` e mouse sai do trigger e do popover → fecha apos 200ms delay
- Quando `trigger = 'hover'` e mouse move de trigger para popover → popover permanece aberto (gap bridge)
- Quando `trigger = 'focus'` e trigger recebe foco (Tab) → popover abre. Foco sai → fecha
- Quando `Escape` pressionado → popover fecha, foco retorna ao trigger
- Quando `dismissible = true` e trigger = 'click' → close button (x) exibido
- Quando `dismissible = false` → sem close button (tipico para hover/focus)
- Quando popover nao cabe na viewport (placement = top mas nao ha espaco) → reposiciona para o lado oposto (flip automatico)
- Quando `title` e fornecido → titulo bold + divider + corpo
- Quando `title` nao e fornecido → apenas corpo

---

## Composicao com outros componentes

| Componente | Relacao | Composicao no Figma (Regra 11/12) |
|---|---|---|
| BC-13 Forms | Popovers como dicas em campos de formulario (icone (i)) | Composicao de uso |
| BC-25 Tables | Popovers para detalhes de celula truncada ou tooltip de coluna | Composicao de uso |
| BC-15 Icons | Icone (i) como trigger visual do popover | Font Awesome — trigger externo ao componente |

> **Regra 12 aplicada:** popover e autonomo. Close button e frame manual 24×24 (padrao aceito — menor que Button SM). Conteudo e texto nativo.

---

## Mapeamento de retrocompatibilidade

| Prop atual | Mapeamento novo | Nota |
|---|---|---|
| `content` | `content` | Mantido |
| `trigger` | `trigger` | Mantido — valores padronizados (hover, click, focus) |
| `placement` | `placement` | Mantido |
| — | `title` (novo) | Titulo opcional para popover com hierarquia |
| — | `dismissible` (novo) | Close button configuravel |
| — | `maxWidth` (novo) | Largura maxima configuravel |

---

## Casos excepcionais / bordas

- **Conteudo longo (> 3 linhas):** popover cresce verticalmente. Sem scroll — conteudo deve ser conciso
- **Conteudo vazio:** componente nao renderiza
- **Multiplos popovers simultaneos:** apenas 1 popover visivel por vez (click trigger). Abrir um fecha o anterior. Hover popovers podem coexistir
- **Popover dentro de modal:** z-index adequado (--z-toast > --z-modal). Focus trap do modal inclui o popover
- **Mobile (< 640px):** hover trigger e ineficaz em touch. Automaticamente converte para click trigger em dispositivos touch
- **Trigger desabilitado:** popover nao abre se trigger estiver com `disabled`
- **Popover com link interno:** corpo pode conter links — links navegaveis por Tab dentro do popover (role="dialog")

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-surface` | Fundo do popover |
| `--color-border` | Borda e divider |
| `--color-text-primary` | Titulo, close hover |
| `--color-text-secondary` | Corpo |
| `--color-text-muted` | Close button default |
| `--color-border-focus` | Focus ring do trigger |
| `--shadow-md` | Sombra do container |
| `--radius-lg` | Border radius (8px) |
| `--font-body` | Familia tipografica |
| `--text-sm` | Font size titulo e corpo (14px) |
| `--font-semibold` | Peso titulo (600) |
| `--font-regular` | Peso corpo (400) |
| `--leading-normal` | Line height corpo (1.5) |
| `--space-3` | Padding interno |
| `--space-2` | Gap titulo→corpo, offset trigger→popover |
| `--z-toast` | Z-index (acima de overlays) |

---

## O que esta fora deste spec

- **Popover com formulario interativo:** composicao de uso — popover como container de BC-13 Forms. Nao e responsabilidade do popover
- **Popover com imagem/media:** nao e padrao SISP. Manter texto simples
- **Popover controlado programaticamente (show/hide via API):** pode ser adicionado como extensao futura
- **Popover com confirmacao (tipo "Tem certeza?"):** usar BC-19 Modal Confirmation em vez de popover
- **Popover posicionado em coordenadas absolutas:** sempre ancorado a um trigger element

---

## Criterios de aceite

- [ ] 4 placements (top, bottom, left, right) documentados no Figma
- [ ] Variantes com titulo e sem titulo
- [ ] Close button (x) para trigger click
- [ ] Seta direcional apontando para trigger
- [ ] Contraste verificado — titulo >15:1, corpo 7.2:1 (ambos AAA)
- [ ] Font size minimo --text-sm (14px) — resolve tip aten
- [ ] ARIA documentado: `aria-describedby`, `role="tooltip"` / `role="dialog"`
- [ ] 3 trigger types documentados (click, hover, focus) com comportamento de fechar
- [ ] Violacoes WCAG (vis aten · tip aten) resolvidas
- [ ] Violacoes Nielsen (H-3 · H-4 · H-6 · H-8 aten) resolvidas
- [ ] Mapeamento de retrocompatibilidade documentado
- [ ] Revisado e aprovado por Giuliana
