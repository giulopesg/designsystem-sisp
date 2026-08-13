---
component-id: BC-29
component-name: Search
type: Base
status: in-figma
sprint: 8
approved-by: [Giuliana]
approved-date: [2026-08-10]
figma-node-id: [969:9072]
---

# Component Spec — Search

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → BC-13 (cor **crit** · vis **crit** · wcag aten · tip aten)
> - `docs/analyses/nielsen-analysis.md` → BC-13 (H-1 **crit** · H-4 **crit** · H-5 **crit** · H-9 **crit** · H-2 aten · H-6 aten · H-8 aten)
> - `docs/analyses/inventory.md` → BC-13
> - Contexto: BC-13 Input não tem affordance de busca. SC-06 Pesquisa Textual é específico (BFF policial). Falta componente genérico de search no DS.

---

## O que é

Search é o campo de busca reutilizável do DS SISP. Combina input de texto com ícone de lupa, botão de limpar e feedback de loading. Usado em headers, barras de filtro, páginas de listagem e qualquer contexto que exija busca textual. Diferente do SC-06 Pesquisa Textual (componente SISP auto-suficiente com BFF policial, checkbox fonetizada e filtros de data), BC-29 é o campo de busca genérico que pode ser usado dentro de SC-06 ou em qualquer outro contexto.

---

## Audiência de uso

- **Policial na DV:** busca de categorias de BO, filtro de listagens, busca geral na interface
- **Devs CiASC / terceiros:** usam search em headers, telas de listagem, painéis de filtro
- **POs (Sommer/Holiwod):** esperam padrão visual consistente de busca em todas as telas do SISP
- **Portal DS:** barra de busca no Header Portal (DC-07) para navegar documentação

---

## Props / API

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `placeholder` | `string` | não | `'Buscar...'` | Texto placeholder do campo |
| `value` | `string` | não | `''` | Valor atual do campo |
| `disabled` | `boolean` | não | `false` | Desabilita o campo |
| `loading` | `boolean` | não | `false` | Exibe spinner no lugar da lupa |
| `showButton` | `boolean` | não | `false` | Exibe botão "Buscar" à direita do input |
| `buttonLabel` | `string` | não | `'Buscar'` | Label do botão (quando showButton=true) |
| `ariaLabel` | `string` | não | `'Buscar'` | Aria-label do container `role="search"` |
| `debounceMs` | `number` | não | `300` | Debounce em ms antes de emitir `onSearch` |
| `onSearch` | `EventEmitter<string>` | sim | — | Emitido quando o usuário submete (Enter) ou após debounce |
| `onClear` | `EventEmitter<void>` | não | — | Emitido quando o usuário limpa o campo |

**Convenção Angular:**
```html
<sisp-lib-search [sispLibSearchConfig]="config"></sisp-lib-search>
```

**Exemplo de uso:**
```typescript
config: SispLibSearchConfig = {
  placeholder: 'Buscar ocorrência...',
  debounceMs: 300,
  onSearch: (term) => this.filterResults(term)
};
```

---

## Anatomia do componente

```
┌─────────────────────────────────────────────────────┐
│  🔍  │  Placeholder ou texto digitado       │  ✕   │
└─────────────────────────────────────────────────────┘
   ↑              ↑                              ↑
 BC-15         BC-13 Input                    BC-15
 Icons SM      (sem borda própria —           Icons SM
 (lupa)        borda no container)            (clear, só
                                              quando Filled)
```

- **Container:** frame com borda, radius e background — encapsula todos os elementos
- **Ícone lupa:** BC-15 Icons SM à esquerda, sempre visível (substituído por BC-16 Loader Spinner SM quando loading=true)
- **Input:** BC-13 Input (campo de texto) — herda todos os estados do input base
- **Ícone limpar (×):** BC-15 Icons SM à direita, visível apenas quando há texto digitado
- **Botão buscar (opcional):** BC-05 Button Primary SM à direita, fora do container do input

---

## Composição atômica (Regra 11)

| Instância interna | Componente DS | Node ID | Uso |
|---|---|---|---|
| Campo de texto | **BC-13 Input** (State=Default) | 118:2232 | Input base — herda todos os estados |
| Ícone de busca | **BC-15 Icons** (Size=SM) | 223:516 | Lupa à esquerda do input (leading icon) |
| Ícone limpar | **BC-15 Icons** (Size=SM) | 223:516 | "×" à direita, visível quando há texto |
| Loader | **BC-16 Loader** (Spinner SM) | 177:484 | Substitui ícone de lupa durante loading |
| Botão buscar | **BC-05 Button** (Primary SM) | 116:1862 | CTA explícito — opcional, para buscas com submit |

---

## Estados e variantes

| Estado | Ícone esquerdo | Input | Ícone direito | Tokens |
|---|---|---|---|---|
| **Default** | 🔍 lupa (`text/muted`) | Placeholder visível | — oculto | border: `border/default`, bg: `surface/bg`, radius: `radius/md` |
| **Focus** | 🔍 lupa (`text/primary`) | Cursor ativo, focus ring | — oculto | border: `border/focus` (2px), bg: `surface/bg` |
| **Filled** | 🔍 lupa (`text/primary`) | Texto digitado | ✕ clear (`text/muted`) | border: `border/default`, bg: `surface/bg` |
| **Loading** | ⟳ spinner SM (`primary/base`) | Texto digitado (readonly) | — oculto | spinner substitui lupa |
| **Disabled** | 🔍 lupa (`text/disabled`) | Placeholder (`text/disabled`) | — oculto | bg: `surface/bg-subtle`, border: `border/default` |

**Total: 5 variantes** no Figma (propriedade `State` com valores Default, Focus, Filled, Loading, Disabled)

### Tokens por estado

| Token | Valor | Variável Figma |
|---|---|---|
| Padding interno vertical | 10px | `space/2.5` |
| Padding interno horizontal | 12px | `space/3` |
| Gap (ícone ↔ input) | 8px | `space/2` |
| Border radius | 8px | `radius/md` |
| Borda default | 1px solid | `border/default` |
| Borda focus | 2px solid | `border/focus` |
| Background | — | `surface/bg` |
| Background disabled | — | `surface/bg-subtle` |
| Cor placeholder | — | `text/muted` |
| Cor texto | — | `text/primary` |
| Cor ícone default | — | `text/muted` |
| Cor ícone focus/filled | — | `text/primary` |
| Cor ícone disabled | — | `text/disabled` |
| Text style placeholder | Body/SM/Regular | text style existente |
| Text style valor | Body/SM/Regular | text style existente |

---

## Violações a resolver — WCAG AA

> Herdadas de BC-13 Forms + específicas do contexto de busca.

| Dimensão | Violação atual | Solução neste spec |
|---|---|---|
| Uso de cor (cor **crit** — BC-13) | Erro/estado indicado apenas por cor vermelha — sem ícone, sem texto | Estados diferenciados por **ícone** (lupa → spinner → ×) + **borda** (default → focus → default) + **cor**. Três canais visuais, não apenas cor |
| Visual (vis **crit** — BC-13) | Estados de foco não mapeados. Campos sem label visível | Focus ring `border/focus` 2px obrigatório. Ícone lupa funciona como affordance visual permanente. `aria-label` documenta o propósito |
| Contraste (wcag aten — BC-13) | Estados de erro e foco precisam verificação | Todos os tokens de cor usados passam AA: `text/primary` sobre `surface/bg` ≥ 4.5:1, `text/muted` sobre `surface/bg` ≥ 4.5:1 |
| Tipografia (tip aten — BC-13) | Hierarquia visual entre label, placeholder e valor não estabelecida | Placeholder em `text/muted` Body/SM/Regular; valor digitado em `text/primary` Body/SM/Regular — hierarquia por cor, mesmo tamanho |
| ARIA (específico — search) | Nenhum componente do DS usa `role="search"` | Container com `role="search"` + `aria-label="Buscar"` (customizável via prop) |
| Keyboard (específico — search) | Sem atalhos documentados para busca | `Escape` limpa o campo, `Enter` submete, foco via `Tab` |

---

## Violações a resolver — Heurísticas Nielsen

> Herdadas de BC-13 Forms + específicas do contexto de busca.

| Heurística | Violação atual | Solução neste spec |
|---|---|---|
| H-1 Visibilidade (**crit** — BC-13) | Estados de validação (erro, enviando, sucesso) não documentados | Estado Loading com spinner SM substitui lupa — feedback visual imediato. Estados Default/Focus/Filled/Loading/Disabled todos mapeados |
| H-3 Controle (específico — search) | Não dá pra limpar facilmente o campo | Botão clear (✕) visível quando há texto. `Escape` como atalho de teclado |
| H-4 Consistência (**crit** — BC-13) | Inconsistência entre campos | Search herda padrões do BC-13 Input (borda, radius, cores). Tokens compartilhados = consistência automática |
| H-5 Prevenção de erros (**crit** — BC-13) | Sem validação progressiva documentada | Debounce configurável (`debounceMs`) previne submissões acidentais. Search não tem validação — emite o que o usuário digita |
| H-6 Reconhecimento (aten — BC-13, específico — search) | Input genérico sem affordance de busca. Campos sem hint | Ícone lupa sempre visível = padrão universal de busca. Placeholder descritivo ("Buscar...") |
| H-7 Flexibilidade (específico — search) | Sem atalho de teclado para busca | `Ctrl+K` / `Cmd+K` como atalho opcional (via prop futura). `Enter` para submit, `Escape` para limpar |
| H-8 Estética (aten — BC-13) | Densidade de campos não auditada | Componente compacto — um único campo com ícones integrados. Padding e gap via tokens |
| H-9 Recuperação (**crit** — BC-13) | Mensagens de erro não especificadas | Search não exibe erro próprio — o componente pai (ex: SC-06) é responsável por feedback de "nenhum resultado" ou erros de API |

---

## Regras de acessibilidade

- [ ] Container com `role="search"` e `aria-label` customizável (padrão: "Buscar")
- [ ] Input com `type="search"` (habilita clear nativo em alguns browsers)
- [ ] Focus ring visível: `2px solid var(--color-border-focus)` no container
- [ ] Navegável por teclado: `Tab` para foco, `Enter` para submit, `Escape` para limpar
- [ ] Botão clear (×) com `aria-label="Limpar busca"` — só visível quando há texto
- [ ] Contraste mínimo 4.5:1 em todos os estados — verificado via tokens semânticos
- [ ] Não depende apenas de cor: ícone muda (lupa → spinner → ×), borda muda (default → focus)
- [ ] Placeholder em português por padrão ("Buscar...")
- [ ] Quando `loading=true`, `aria-busy="true"` no container + spinner com `aria-hidden="true"` (decorativo)
- [ ] Quando `disabled=true`, `aria-disabled="true"` no input

---

## Comportamentos esperados

- Quando usuário foca no campo → borda muda para `border/focus` (2px), ícone lupa muda para `text/primary`
- Quando usuário digita → após `debounceMs` (padrão 300ms), emite `onSearch` com o termo atual
- Quando usuário pressiona `Enter` → emite `onSearch` imediatamente (ignora debounce)
- Quando usuário pressiona `Escape` → limpa o campo, emite `onClear`, campo mantém foco
- Quando campo tem texto → ícone clear (×) aparece à direita
- Quando usuário clica no clear (×) → limpa o campo, emite `onClear`, campo mantém foco
- Quando `loading=true` → spinner SM substitui ícone de lupa. Input fica readonly (não editável durante loading)
- Quando `disabled=true` → campo não aceita foco nem interação. Background muda para `surface/bg-subtle`
- Quando `showButton=true` → BC-05 Button Primary SM aparece à direita do container do input. Click no botão emite `onSearch`
- Quando `value` é muito longo → texto faz scroll horizontal dentro do input (padrão nativo)

---

## Comportamento responsivo

> Regra 13 — Responsividade obrigatória. Breakpoints: Mobile (<768px), Tablet (768–1023px), Desktop (≥1024px).

| Precisa de variante `Layout=Mobile`? | **Não** |
|---|---|
| **Justificativa** | Largura 100% do container (como BC-13 Input). Adapta-se automaticamente. Ícones e padding internos não mudam. Componente auto-contido, menor que 320px de altura. |
| **Desktop** | Comportamento padrão — largura definida pelo container pai |
| **Mobile** | Mesmo comportamento — 100% do container, touch targets dos ícones já ≥ 44×44 CSS pixels (ícone SM 16px + padding interno) |
| **Tablet** | Segue Desktop |

---

## Casos excepcionais / bordas

- **Sem texto digitado:** ícone clear (×) fica oculto. Apenas lupa visível
- **Texto muito longo:** scroll horizontal nativo do input. Sem truncamento
- **Search vazio + Enter:** emite `onSearch('')`. Componente pai decide se busca vazia é válida
- **Loading + usuário tenta digitar:** input readonly durante loading. Texto anterior permanece
- **showButton + loading:** botão fica disabled durante loading. Spinner aparece no ícone, não no botão
- **Focus + Escape sem texto:** nada acontece (campo já está vazio). `onClear` não é emitido

---

## Composição com outros componentes

| Componente | Relação |
|---|---|
| BC-14 Headers | Search pode ser embutido no header para busca global |
| DC-07 Header Portal | Search no header do portal DS para navegar documentação |
| SC-06 Pesquisa Textual | BC-29 pode substituir o input manual dentro do SC-06 |
| BC-25 Tables | Search como filtro acima da tabela |
| SC-01 Atualizações Recentes | Search para filtrar registros recentes |

---

## O que está fora deste spec

- **Autocomplete / sugestões:** comportamento de produto — componente futuro (BC-XX Autocomplete) que comporia com BC-29
- **Search com filtros avançados:** SC-06 já cobre busca policial com filtros. BC-29 é o input simples
- **Search com resultados inline (dropdown de sugestões):** usar BC-10 Dropdown composto com BC-29 se necessário
- **Atalho global `Ctrl+K`:** comportamento de aplicação, não do componente. O componente apenas recebe foco
- **Validação de input:** Search não valida — emite o que o usuário digita. Validação é responsabilidade do componente pai

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `border/default` | Borda do container (Default, Filled, Disabled) |
| `border/focus` | Borda do container (Focus) — 2px |
| `surface/bg` | Background do container (Default, Focus, Filled, Loading) |
| `surface/bg-subtle` | Background do container (Disabled) |
| `text/primary` | Cor do texto digitado, ícone lupa em Focus/Filled |
| `text/muted` | Cor do placeholder, ícone lupa em Default, ícone clear |
| `text/disabled` | Cor do texto e ícone em Disabled |
| `primary/base` | Cor do spinner SM (Loading) |
| `radius/md` | Border radius do container (8px) |
| `space/2` | Gap entre ícone e input (8px) |
| `space/2.5` | Padding vertical (10px) |
| `space/3` | Padding horizontal (12px) |
| Body/SM/Regular | Text style do placeholder e valor digitado |

---

## Critérios de aceite

- [ ] 5 estados (Default, Focus, Filled, Loading, Disabled) no Figma com tokens aplicados
- [ ] Composição atômica: BC-13 Input + BC-15 Icons SM + BC-16 Loader Spinner SM como instâncias
- [ ] 100% variable bindings (spacing, colors, radius, text styles)
- [ ] Nenhum text style novo — usa Body/SM/Regular existente
- [ ] Nenhuma variável nova — usa apenas os 108 tokens existentes
- [ ] Violações WCAG AA (cor **crit** · vis **crit** · wcag aten · tip aten) resolvidas
- [ ] Violações Nielsen (H-1 · H-3 · H-4 · H-5 · H-6 · H-7 · H-8 · H-9) resolvidas
- [ ] `role="search"` + `aria-label` documentados
- [ ] Keyboard navigation documentada (Tab, Enter, Escape)
- [ ] Revisado e aprovado por Giuliana
