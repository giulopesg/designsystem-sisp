---
component-id: SC-14
component-name: Timelines
type: SISP
status: approved
sprint: 7
approved-by: [Giuliana]
approved-date: [2026-08-04]
figma-node-id: [804:7204]
---

# Component Spec — Timelines

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → SC-14 (cor aten — date-pills 1º grupo vermelho, demais azuis, lógica não documentada · tip aten)
> - `docs/analyses/nielsen-analysis.md` → SC-14 (H-1 aten · H-4 aten · H-6 aten · H-8 aten — sem críticos)
> - `docs/analyses/inventory.md` → SC-14
> - Screenshot de produção: `uikit-screencapture/...timelines...2026-06-03-09_43_53.png`

---

## O que é

Timelines é o componente que exibe uma linha do tempo de eventos no SISP. Usado para visualizar o histórico cronológico de um registro — ex: ciclo de vida de um B.O. (abertura, aprovações, finalizações), movimentações de processos, ou ações em sequência. Os eventos são agrupados por data com "pills" coloridas e cada evento é um card expansível com título, descrição e timestamp.

---

## Audiência de uso

- **Policial na DV:** visualiza histórico do B.O. — quando foi aberto, aprovado, quem fez cada ação. Formato cronológico facilita reconstruir a sequência de fatos
- **Devs CiASC / terceiros:** integram via config object com array de `TimelineEvent[]` e propriedades opcionais de orientação e cor
- **Demilis (mantenedor):** documentar lógica de cores dos date-pills (1º vermelho, demais verdes/amarelos) e valores válidos de `orientation`

---

## Props / API

> **Padrão de API:** Config object — `[sispLibTimelineConfig]="config"`

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `events` | `TimelineEvent[]` | sim | — | Array de eventos para exibir na timeline |
| `orientation` | `string` | não | `'vertical'` | Orientação da timeline. Valores: `'vertical'` (padrão), `'horizontal'` (se implementado) |
| `color` | `string` | não | — | Cor temática da timeline (sem escopo definido — documentar) |

**TimelineEvent (interface inferida):**
```typescript
interface TimelineEvent {
  label: string;         // Tipo do evento (ex: "Início", "Aprovação", "Finalização")
  date: string;          // Data no formato ISO ou dd/mm/yyyy
  description: string;   // Descrição do evento
  icon?: string;         // Ícone Font Awesome (opcional — muda por tipo de evento)
  time?: string;         // Horário (HH:mm:ss)
}
```

> ⚠️ **Inventário:** `orientation` sem valores válidos documentados. `color` sem escopo definido. Confirmar com Demilis.

**Convenção Angular:**
```html
<sisp-lib-timeline
  [sispLibTimelineConfig]="{
    events: [
      { label: 'Início', date: '2023-01-01', description: 'Processo iniciado' },
      { label: 'Aprovação', date: '2023-01-05', description: 'Aprovado pelo gestor' },
      { label: 'Finalização', date: '2023-01-10', description: 'Processo finalizado' }
    ]
  }">
</sisp-lib-timeline>
```

---

## Anatomia do componente

### Timeline vertical (default)
```
  ┌──────────────┐
  │  02/06/2026  │  ← Date pill (vermelho = mais recente)
  └──────────────┘
         │
    ┌────┤
    │ ⚙ │  Card Title 1                            ⏱ 09:43:48  [—]
    └────┤  Card Body 1
         │
    ┌────┤
    │ ⚙ │  Card Title 5                            ⏱ 09:43:48  [—]
    └────┤  Card Body 5
         │
  ┌──────────────┐
  │  03/06/2026  │  ← Date pill (verde = anterior)
  └──────────────┘
         │
    ┌────┤
    │ 📄 │  Card Title 2                            ⏱ 09:43:48  [—]
    └────┤  Card Body 2
         │
    ┌────┤
    │ 🔵 │  Card Title 4                            ⏱ 09:43:48  [—]
    └────┤  Card Body 4
         │
  ┌──────────────┐
  │  04/06/2026  │  ← Date pill (verde)
  └──────────────┘
         │
    ┌────┤
    │ ✉ │  Card Title 3                            ⏱ 09:43:48  [—]
    └────┤  Card Body 3
```

### Composição atômica (Regra 11)

| Elemento | Componente DS | Instância |
|---|---|---|
| Date pill | BC-04 Badge Filled SM | 1× por grupo de data — cor semântica |
| Ícone do evento | BC-15 Icons SM | 1× por card — dentro de circle indicator |
| Card do evento | Frame com tokens | Layout: título + descrição + timestamp |
| Botão expandir/colapsar | Close Button (frame 24×24) | 1× por card — toggle |
| Linha vertical | Rectangle 2px | Linha de conexão entre eventos |
| Circle indicator | Ellipse 32×32 | Container circular para o ícone do evento |

---

## Estados e variantes

| Estado | Descrição visual |
|---|---|
| **Default** | Timeline completa, cards expandidos |
| **Collapsed** | Cards mostrando apenas título (descrição oculta) |
| **Loading** | Skeleton na área dos cards |
| **Empty** | "Nenhum evento registrado" |

### Lógica de cores dos date-pills

> ⚠️ **WCAG cor aten:** 1º grupo vermelho, demais verdes — lógica não documentada.

| Grupo | Cor atual | Interpretação proposta | Solução DS |
|---|---|---|---|
| Mais recente (1º) | Vermelho (#C4000B) | Destaque do grupo atual / mais recente | **BC-04 Badge Danger** — "mais recente" |
| Anteriores | Verde (#336633) / Âmbar | Grupos passados | **BC-04 Badge Success** ou **Neutral** — "anterior" |

> **Solução WCAG:** Date-pills não dependem apenas de cor — cada pill tem o **texto da data** como informação primária. A cor é reforço visual, não canal único. Adicionar `aria-label="Eventos de 02/06/2026 (mais recente)"` para o grupo vermelho.

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução neste spec |
|---|---|---|
| Uso de cor (cor aten) | Date-pills: vermelho vs. verde sem lógica documentada. Cor como único diferenciador do grupo mais recente | **Data como canal primário** — pill mostra dd/mm/yyyy como texto. Cor é reforço: vermelho (Danger) = mais recente, verde/neutro = anterior. `aria-label` com "mais recente" no primeiro grupo. Nunca apenas cor |
| Tipografia (tip aten) | Tamanhos de texto não padronizados | **Text styles DS** aplicados: Heading/SM (título do card), Body/SM (descrição), Label/SM (timestamp), Body/XS (date pill) |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução neste spec |
|---|---|---|
| H-1 Visibilidade (aten) | Sem indicação de quantos eventos existem | **Contador:** "6 eventos em 3 datas" acima da timeline. Date-pills servem como marcadores visuais de grupos |
| H-4 Consistência (aten) | Config object (padrão principal) | OK — segue padrão `[sispLib*Config]` |
| H-6 Reconhecimento (aten) | Ícones por tipo de evento sem legenda | **Tooltip** ao hover sobre o ícone do circle indicator: "Tipo: Aprovação" / "Tipo: Início". Ícones mapeados semanticamente |
| H-8 Estética (aten) | Layout Bootstrap | Tokens DS SISP: `--color-surface`, `--radius-lg`, `--shadow-sm`. Linha vertical com `--color-border`. Circle indicators com fundo `--color-bg-subtle` |

---

## Regras de acessibilidade

- [ ] Container com `role="feed"` ou `role="list"` e `aria-label="Linha do tempo de eventos"`
- [ ] Cada card com `role="article"` ou `role="listitem"`
- [ ] Date-pills com `aria-label="Eventos de DD/MM/YYYY"` + "(mais recente)" para o primeiro grupo
- [ ] Card expandido com `aria-expanded="true"`, colapsado com `aria-expanded="false"`
- [ ] Timestamp com `<time datetime="HH:MM:SS">` semântico
- [ ] Ícones dos circle indicators com `aria-hidden="true"` se tooltip com texto descritivo
- [ ] Linha vertical decorativa com `aria-hidden="true"`
- [ ] Focus ring: `2px solid var(--color-border-focus)`
- [ ] Contraste dos date-pills: texto branco sobre vermelho (≥ 4.5:1) e sobre verde (≥ 4.5:1)
- [ ] Labels em português

---

## Comportamentos esperados

### Renderização
- Quando `events` recebidos → agrupa por data (dd/mm/yyyy). Ordena cronologicamente (mais recente primeiro)
- Quando grupo renderizado → date-pill + linha vertical + cards dos eventos daquele dia
- Quando primeiro grupo → date-pill vermelho (Danger) = mais recente

### Expansão
- Quando clica — no card → colapsa (mostra apenas título)
- Quando clica novamente → expande (mostra título + descrição)
- Quando "Expandir todos" → todos os cards expandidos
- Quando "Colapsar todos" → todos os cards colapsados

### Ícones por tipo de evento
- Mapeamento semântico (confirmar com Demilis):
  - ⚙ = alteração de configuração
  - 📄 = documento
  - 🔵 = informação
  - ✉ = comunicação
  - ✓ = conclusão

---

## Comportamento responsivo

| Precisa de variante `Layout=Mobile`? | **Não** — timeline vertical é naturalmente responsiva |
|---|---|
| **Desktop** | Timeline vertical full-width. Cards com largura limitada (~800px) |
| **Mobile** | Mesmo layout. Linha vertical pode ser mais curta. Cards full-width |
| **Tablet** | Segue Desktop |

> Timeline vertical é auto-contida — adapta com width 100% do container. Sem necessidade de variante Mobile dedicada.

---

## Composição com outros componentes

| Componente | Relação | Composição no Figma |
|---|---|---|
| **BC-04 Badge** | Date-pills (agrupamento por data) | **Instância** — Filled SM, Danger (1º) + Neutral/Success (demais) |
| **BC-15 Icons** | Ícone de tipo de evento (circle indicator) | **Instância** — SM dentro de Ellipse 32×32 |

---

## Variantes no Figma

| Variante | Property | Descrição |
|---|---|---|
| **Default** | `State=Default` | 3 grupos de data, 6 cards expandidos, date-pills coloridos |
| **Collapsed** | `State=Collapsed` | Cards colapsados (apenas títulos) |
| **Empty** | `State=Empty` | "Nenhum evento registrado" |

> 3 variantes. Sem variante Mobile — timeline vertical é auto-contida.

---

## Tokens utilizados

| Token | Uso |
|---|---|
| `--color-surface` | Fundo dos cards |
| `--color-border` | Borda dos cards, linha vertical |
| `--color-danger` | Date-pill mais recente (1º grupo) |
| `--color-success` | Date-pills anteriores |
| `--color-bg-subtle` | Fundo dos circle indicators |
| `--color-text-primary` | Título do card |
| `--color-text-secondary` | Descrição, timestamp |
| `--color-text-inverse` | Texto do date-pill (branco sobre cor) |
| `--color-border-focus` | Focus ring |
| `--font-body` | Textos |
| `--font-mono` | Timestamp (HH:mm:ss) |
| `--text-sm` | Descrição, timestamp |
| `--text-base` | Título do card |
| `--text-xs` | Date-pill |
| `--space-4` | Padding dos cards |
| `--space-3` | Gap entre cards |
| `--space-6` | Gap entre grupos de data |
| `--radius-lg` | Cards |
| `--radius-full` | Date-pills, circle indicators |
| `--shadow-sm` | Sombra dos cards |

---

## Casos excepcionais / bordas

- **Muitos eventos no mesmo dia:** renderiza todos no mesmo grupo. Scroll vertical
- **Evento sem data:** tratar como erro de dados. Não renderizar, log de aviso
- **Evento sem descrição:** renderizar card apenas com título. Sem toggle de expansão
- **Timeline com 1 evento:** renderizar sem linha vertical (apenas 1 card)
- **Datas futuras:** aceitar — pode ser usado para programação de eventos
- **Orientação horizontal:** `orientation='horizontal'` — funcionalidade futura, não implementar neste sprint
- **Prop `color`:** sem escopo definido — omitir no Figma, documentar como prop reservada

---

## O que está fora deste spec

- **Timeline horizontal:** prop `orientation` existe mas horizontal não implementado. Manter vertical como padrão
- **Prop `color`:** escopo não definido — reservada para uso futuro
- **Interação entre eventos:** seleção de eventos para ação (ex: imprimir, compartilhar) — funcionalidade da app
- **Edição de eventos:** criar/editar eventos é responsabilidade da aplicação, não do componente de visualização
- **Agrupamento customizado:** agrupar por semana/mês — funcionalidade futura

---

## Critérios de aceite

- [ ] Timeline vertical com agrupamento por data (date-pills)
- [ ] Date-pills como instâncias de BC-04 Badge Filled SM (Regra 11)
- [ ] Circle indicators com instâncias de BC-15 Icons SM (Regra 11)
- [ ] Cards expansíveis com título, descrição e timestamp
- [ ] Lógica de cor documentada: 1º grupo (Danger/vermelho), demais (Success/verde)
- [ ] WCAG (cor aten) resolvida — data como canal primário, cor como reforço
- [ ] WCAG (tip aten) resolvida — text styles DS aplicados
- [ ] Tokens aplicados (Regra 8)
- [ ] Timestamp em fonte mono (`--font-mono`)
- [ ] Labels em português
- [ ] Revisado e aprovado por Giuliana
