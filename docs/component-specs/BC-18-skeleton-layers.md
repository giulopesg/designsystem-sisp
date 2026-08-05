---
component-id: BC-18
component-name: Skeleton Layers
type: Base
status: approved
sprint: 4.1
approved-by: [Giuliana]
approved-date: [2026-07-16]
figma-node-id: [323:829]
---

# Component Spec — Skeleton Layers

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → BC-18 (N/A — inacessivel durante inventario)
> - `docs/analyses/nielsen-analysis.md` → BC-18 (N/A — nao avaliado)
> - `docs/analyses/inventory.md` → BC-18 (A confirmar — "estado, nao componente" per Demilis)

> **Nota:** Este spec tem gaps marcados como **[A CONFIRMAR COM DEMILIS]**. Demilis confirmou que Skeleton Layers e um estado, nao um componente standalone. Este spec formaliza o padrao de skeleton como componente utilitario de placeholders, usado dentro de outros componentes para representar estados de loading.

---

## O que e

Skeleton Layers e o componente utilitario de placeholders de carregamento do DS SISP. Fornece formas geometricas animadas (barras retangulares, circulos, blocos) que imitam a estrutura do conteudo enquanto os dados carregam. Na DV, usado dentro de Cards, Tables, listas e areas de conteudo para dar feedback visual de que a informacao esta sendo carregada — alternativa ao spinner quando a estrutura do conteudo e previsivel.

> **Diferenca de BC-16 Loaders:** Loaders (spinner, bar) sao indicadores genericos de loading sem relacao com a estrutura do conteudo. Skeleton Layers imitam o layout do conteudo real — o usuario "ve" onde o titulo, texto e imagem vao aparecer. Ambos coexistem: Loader para acoes pontuais (submit, upload), Skeleton para carregamento de pagina/secao.

---

## Audiencia de uso

- **Policial na DV:** ve skeletons ao abrir telas de consulta, detalhes de BO, listagens. Entende que o conteudo esta carregando sem confundir com tela vazia ou erro
- **Devs CiASC / terceiros:** usam skeletons dentro de componentes existentes (Cards, Tables, listas) para indicar estados de loading. Precisam de formas reutilizaveis e padrao de animacao consistente
- **POs (Sommer/Holiwod):** precisam que a experiencia de loading seja profissional e nao cause ansiedade no usuario

---

## Props / API

| Prop | Tipo | Obrigatorio | Padrao | Descricao |
|---|---|---|---|---|
| `type` | `'text' \| 'circle' \| 'rect' \| 'card'` | nao | `'text'` | Forma do skeleton |
| `width` | `string` | nao | `'100%'` | Largura do skeleton |
| `height` | `string` | nao | `'16px'` | Altura do skeleton |
| `lines` | `number` | nao | `1` | Numero de linhas (quando type='text') |
| `animated` | `boolean` | nao | `true` | Animacao shimmer |

**[A CONFIRMAR COM DEMILIS]:** Verificar se sisp-lib-skeleton-layers existe no repositorio Angular e quais props reais aceita. As props acima sao inferidas do padrao de skeleton loaders.

**Convencao Angular:**
```html
<sisp-lib-skeleton-layers [sispLibSkeletonLayersConfig]="config"></sisp-lib-skeleton-layers>
```

**Exemplo de uso:**
```typescript
// Skeleton de texto (3 linhas simulando paragrafo)
configText: SispLibSkeletonLayersConfig = {
  type: 'text',
  lines: 3,
  width: '100%'
};

// Skeleton circular (avatar)
configAvatar: SispLibSkeletonLayersConfig = {
  type: 'circle',
  width: '40px',
  height: '40px'
};
```

---

## Anatomia do componente

### Formas disponiveis

```
TEXT (default):
┌────────────────────────────────────────┐  ← barra 100% width
├──────────────────────────────┐          ← barra ~75% width
├─────────────────────┐                   ← barra ~50% width (ultima linha menor)

CIRCLE:
  ┌──┐
  │  │  ← circulo (avatar, icone)
  └──┘

RECT:
┌──────────────────────┐
│                      │  ← retangulo (imagem, bloco)
│                      │
└──────────────────────┘

CARD (composicao):
┌──────────────────────────────────────┐
│  ○  ████████████████                 │  ← circle + text line
│     ██████████████████████           │  ← text line
│     ████████████████                 │  ← text line (menor)
│  ┌──────────────────────────────┐    │  ← rect (imagem)
│  │                              │    │
│  └──────────────────────────────┘    │
└──────────────────────────────────────┘
```

- **Barra (text):** retangulo com border-radius --radius-sm, altura 16px padrao
- **Circulo:** ellipse com dimensoes iguais
- **Retangulo:** bloco com border-radius --radius-md
- **Card:** composicao pre-definida de circle + text + rect (atalho para casos comuns)
- **Animacao shimmer:** gradiente linear que desliza horizontalmente (pulsacao sutil)

---

## Estados e variantes

| Estado | Descricao visual | Tokens |
|---|---|---|
| **Loading (default)** | Formas cinza com animacao shimmer | `bg: --color-surface-bg-muted` |
| **Static** | Formas cinza sem animacao (prefers-reduced-motion) | `bg: --color-surface-bg-muted` |

### Cores

| Elemento | Token | Valor |
|---|---|---|
| Fundo da forma | `--color-surface-bg-muted` | #F3F4F6 |
| Shimmer highlight | — | rgba(255,255,255,0.6) |
| Fundo do container | transparente | — |

### Verificacao de contraste (WCAG AA)

| Elemento | Foreground | Background | Ratio | Resultado |
|---|---|---|---|---|
| Skeleton vs fundo branco | #F3F4F6 | #FFFFFF | ~1.1:1 | ✅ (decorativo — placeholder, nao informacao) |

> Skeletons sao elementos decorativos de placeholder — nao transmitem informacao. Contraste baixo e intencional e padrao consolidado (Material Design, Ant Design, Carbon).

### Dimensoes

| Propriedade | Valor | Token |
|---|---|---|
| Text height | 16px | `--text-base` (referencia) |
| Text border-radius | 4px | `--radius-sm` |
| Text gap entre linhas | 8px | `--space-2` |
| Text ultima linha width | 60-75% | — |
| Circle size | configuravel | — |
| Rect border-radius | 8px | `--radius-md` |
| Shimmer duration | 1.5s | — |
| Shimmer delay | 0s | — |

---

## Violacoes a resolver — WCAG AA

| Dimensao | Violacao atual | Solucao neste spec |
|---|---|---|
| (N/A) | Nao avaliado — componente inacessivel durante inventario | Spec define acessibilidade from scratch: `aria-hidden="true"` nos skeletons (decorativos), `aria-busy="true"` no container pai, `aria-label` descritivo no container. Respeita `prefers-reduced-motion` |

---

## Violacoes a resolver — Heuristicas Nielsen

| Heuristica | Violacao atual | Solucao neste spec |
|---|---|---|
| (N/A) | Nao avaliado | Spec resolve preventivamente: H-1 (visibilidade — skeletons imitam layout real, usuario entende que conteudo esta carregando), H-4 (consistencia — padrao unico de skeleton para todos os componentes), H-8 (estetica — animacao shimmer sutil, nao intrusiva) |

---

## Regras de acessibilidade

- [ ] Skeletons com `aria-hidden="true"` (decorativos)
- [ ] Container pai com `aria-busy="true"` durante loading
- [ ] Container pai com `aria-label` descritivo (ex: "Carregando dados da ocorrencia")
- [ ] `prefers-reduced-motion: reduce` → desativa animacao shimmer (formas estaticas)
- [ ] Nao depende de cor para comunicar estado — forma geometrica e posicao comunicam
- [ ] Labels em portugues

---

## Comportamentos esperados

- Quando conteudo esta carregando → skeletons visiveis com animacao shimmer
- Quando conteudo carrega → skeletons substituidos pelo conteudo real (transicao instantanea)
- Quando `animated = false` → formas estaticas sem shimmer
- Quando `type = 'text'` e `lines > 1` → multiplas barras com ultima linha menor (60-75%)
- Quando `type = 'circle'` → ellipse com width=height
- Quando `type = 'card'` → composicao pre-definida (circle + text + rect)
- Quando `prefers-reduced-motion: reduce` → shimmer desativado automaticamente
- Quando loading demora > 10s → considerar adicionar BC-16 Loader com mensagem textual (responsabilidade do produto, nao do componente)

---

## Composicao com outros componentes

| Componente | Relacao | Composicao no Figma (Regra 11/12) |
|---|---|---|
| **BC-25 Tables** | Tables Loading ja usa skeleton rows | Padrao existente — formalizar como instancias de Skeleton |
| **BC-06 Cards** | Cards podem ter estado loading com skeleton | Composicao de uso |
| **BC-16 Loaders** | Complementar — Loader para acoes, Skeleton para conteudo | Coexistencia documentada |

> **Regra 12 aplicada:** BC-25 Tables Loading variant ja usa um padrao de skeleton (barras placeholder). Este spec formaliza esse padrao como componente reutilizavel. **[A CONFIRMAR COM DEMILIS]:** verificar se Tables Loading deve migrar para instancias de Skeleton Layers.

---

## Mapeamento de retrocompatibilidade

| Prop atual | Mapeamento novo | Nota |
|---|---|---|
| **[A CONFIRMAR COM DEMILIS]** | `type` | Verificar props reais no repositorio |
| **[A CONFIRMAR COM DEMILIS]** | `width` / `height` | Verificar como dimensoes sao configuradas |
| **[A CONFIRMAR COM DEMILIS]** | `lines` | Verificar se existe conceito de linhas |
| **[A CONFIRMAR COM DEMILIS]** | `animated` | Verificar se animacao e controlavel |

---

## Casos excepcionais / bordas

- **Conteudo ja disponivel (cache):** skeleton nao aparece — conteudo renderiza direto
- **Erro de carregamento:** skeleton e removido, exibe estado de erro (responsabilidade do componente pai)
- **Loading infinito:** skeleton continua animando — sem timeout interno. Recomendacao: produto define timeout e fallback
- **Mobile:** layout identico — skeleton adapta ao container responsivo
- **Dark mode:** **[A CONFIRMAR COM DEMILIS]** — verificar se ha necessidade de cores invertidas

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-surface-bg-muted` | Fundo das formas (#F3F4F6) |
| `--radius-sm` | Border radius barras de texto |
| `--radius-md` | Border radius retangulos |
| `--space-2` | Gap entre linhas de texto |

---

## O que esta fora deste spec

- **Skeleton com conteudo parcial (progressive loading):** extensao futura
- **Skeleton com erro:** estado de erro e responsabilidade do componente pai
- **Skeleton com placeholder de imagem (blur-up):** tecnica de imagem, nao skeleton
- **Skeleton customizado por componente:** a composicao e feita pelo dev — Skeleton fornece os blocos basicos

---

## Gaps a confirmar com Demilis

| # | Gap | Impacto |
|---|---|---|
| 1 | Skeleton Layers existe como componente Angular (`sisp-lib-skeleton-layers`)? Ou e um padrao CSS aplicado manualmente? | Define se este spec descreve um componente real ou um padrao de design |
| 2 | Quais props aceita o componente atual (se existir)? | Tabela de props pode estar incompleta |
| 3 | Demilis disse "estado, nao componente" — confirmar: skeleton e uma variante de estado em cada componente (como Tables Loading), ou e um componente utilitario separado? | Muda a abordagem no Figma — estado interno vs. componente reutilizavel |
| 4 | Ha uso de skeleton na DV atualmente? Em quais telas? | Prioridade DV (Regra 10) |
| 5 | Tables Loading deve migrar para instancias de Skeleton Layers? | Regra 11 — composicao atomica |

---

## Criterios de aceite

- [ ] Formas basicas no Figma: text (barra), circle, rect
- [ ] Animacao shimmer documentada (CSS/Angular)
- [ ] Tokens aplicados (bg-muted, radius-sm, radius-md, space-2)
- [ ] Acessibilidade documentada (aria-busy, aria-hidden, prefers-reduced-motion)
- [ ] Relacao com BC-25 Tables Loading documentada
- [ ] Relacao com BC-16 Loaders documentada (complementar, nao substituto)
- [ ] **Gaps confirmados com Demilis**
- [ ] Revisado e aprovado por Giuliana
