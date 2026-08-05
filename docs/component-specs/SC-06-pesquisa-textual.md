---
component-id: SC-06
component-name: Pesquisa Textual
type: SISP
status: approved
sprint: 7
approved-by: [Giuliana]
approved-date: [2026-08-04]
figma-node-id: [804:6561]
---

# Component Spec — Pesquisa Textual

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → SC-06 (cor aten · vis aten)
> - `docs/analyses/nielsen-analysis.md` → SC-06 (H-1 aten · H-3 aten · H-4 aten · H-7 aten — sem críticos)
> - `docs/analyses/inventory.md` → SC-06
> - Screenshot de produção: `uikit-screencapture/...pesquisa-textual...2026-06-02-19_51_09.png`

---

## O que é

Pesquisa Textual é o componente de busca full-text em registros policiais do SISP. Diferente dos componentes de consulta específica (SC-02, SC-03, SC-04), este permite busca livre por conteúdo textual dos registros, com filtros de período e ordenação. O diferencial é a **pesquisa fonetizada** — recurso policial que busca variações fonéticas de nomes (ex: "Wagner" encontra "Vagner"), essencial quando a grafia exata é incerta.

---

## Audiência de uso

- **Policial na DV:** busca registros quando não tem identificador exato (placa, CPF, etc.). Usa pesquisa fonetizada para nomes com grafia incerta — funcionalidade específica do contexto policial
- **Devs CiASC / terceiros:** componente auto-suficiente (sem props), adicionar na tela sem configuração
- **Demilis (mantenedor):** documentar comportamento da pesquisa fonetizada e filtro por "data de abertura"

---

## Props / API

> **Padrão de API:** Auto-suficiente via BFF (sem props de configuração).

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| — | — | — | — | Nenhuma prop — auto-suficiente |

**Convenção Angular (auto-suficiente):**
```html
<sisp-lib-text-search></sisp-lib-text-search>
```

---

## Anatomia do componente

```
┌──────────────────────────────────────────────────────────────┐
│                                                              │
│  Conteúdo da busca* ℹ️    Data inicial      Data final       │
│                           (abertura)        (abertura)       │
│  ┌────────────────────┐  ┌──────────📅┐   ┌──────────📅┐     │
│  │ Digite a busca...  │  │ dd/mm/yyyy │   │ dd/mm/yyyy │     │
│  └────────────────────┘  └───────────┘   └───────────┘     │
│  (BC-13 Input)           (BC-13 Input)   (BC-13 Input)     │
│                                                              │
│  Ordenar por                    ☐ Pesquisa fonetizada        │
│  ┌──────────────────▾┐         (BC-13 Checkbox)              │
│  │ Relevância        │                                       │
│  └───────────────────┘                                       │
│  (BC-13 Select)                                              │
│                                                              │
│                           ┌──────────┐  ┌──────────────┐     │
│                           │ ✎ Limpar │  │ 🔍 Pesquisar │     │
│                           └──────────┘  └──────────────┘     │
└──────────────────────────────────────────────────────────────┘
```

### Composição atômica (Regra 11)

| Elemento | Componente DS | Instância |
|---|---|---|
| Campo de busca | BC-13 Input | 1× — campo principal com tooltip ℹ️ |
| Campos de data | BC-13 Input (type=date) | 2× — Data inicial e Data final |
| Ordenação | BC-13 Select | 1× — opções: Relevância, Data (mais recente), Data (mais antigo) |
| Pesquisa fonetizada | BC-13 Checkbox | 1× — toggle para busca fonética |
| Botão Limpar | BC-05 Button Secondary MD | 1× |
| Botão Pesquisar | BC-05 Button Primary MD | 1× |
| Ícones nos botões | BC-15 Icons XS | 2× — borracha + lupa |
| Tooltip ℹ️ | BC-23 Popover (ou tooltip nativo) | 1× — explica campo de busca |

---

## Estados e variantes

| Estado | Descrição visual | Composição |
|---|---|---|
| **Default** | Formulário vazio, checkbox desmarcado | Campos vazios + botões habilitados |
| **Preenchido** | Campo de busca com texto, filtros opcionais | Pesquisar habilitado |
| **Fonetizada ativa** | Checkbox marcado, visual de confirmação | Checkbox checked (BC-13) |
| **Loading** | Pesquisa em andamento | Spinner no botão, campos disabled |
| **Resultado** | Lista de registros encontrados | Formulário + lista de resultados abaixo |
| **Vazio** | Zero resultados | Mensagem descritiva + sugestão de ativar fonetizada |
| **Erro** | Falha no BFF | Alert Danger (BC-03) com mensagem |

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução neste spec |
|---|---|---|
| Uso de cor (cor aten) | Tooltip ℹ️ pode não ser visível | Tooltip com **ícone visível** (BC-15 Icons XS, cor `--color-info`) + texto ao hover/focus. `aria-describedby` para screen readers |
| Visual (vis aten) | Checkbox sem label clara, estados não documentados | **Label visível** "Pesquisa fonetizada" adjacente ao checkbox. Estados Loading/Erro/Vazio explícitos |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução neste spec |
|---|---|---|
| H-1 Visibilidade (aten) | Sem feedback durante busca | **Spinner** no botão + campos disabled. Contador de resultados: "42 registros encontrados" com `aria-live` |
| H-3 Controle (aten) | Sem limpar | **Botão "Limpar"** reseta tudo (campos, checkbox, resultados) |
| H-4 Consistência (aten) | Auto-suficiente (vs. config object de SC-02/04) | Padrão documentado. SC-06 é auto-suficiente por design — não precisa de config externo |
| H-7 Flexibilidade (aten) | Sem atalhos | **Enter** submete, **Ctrl+L** limpa, **Ctrl+F** foca no campo de busca |

> **Nota:** SC-06 não tem H-9 como CRIT (diferente de SC-02/03/04). Tratamento de erro é aten — ainda assim, documentar mensagens descritivas.

---

## Regras de acessibilidade

- [ ] Campo de busca com `aria-label="Conteúdo da busca textual"` e `aria-required="true"`
- [ ] Tooltip ℹ️ com `aria-describedby` — texto: "Busca no conteúdo textual de todos os registros. Use aspas para busca exata"
- [ ] Campos de data com `aria-label` descritivo ("Data inicial de abertura do registro")
- [ ] Checkbox "Pesquisa fonetizada" com `aria-describedby` tooltip explicativo
- [ ] Área de resultados com `aria-live="polite"` e contador
- [ ] Focus ring: `2px solid var(--color-border-focus)`
- [ ] Contraste 4.5:1 AA
- [ ] Labels em português
- [ ] Navegação: Tab entre campos, Enter submete

---

## Comportamentos esperados

### Pesquisa
- Quando preenche campo de busca e clica Pesquisar → Loading: Spinner + disabled
- Quando BFF retorna resultados → lista abaixo do formulário com contador "N registros encontrados"
- Quando BFF retorna vazio → "Nenhum registro encontrado. Tente ativar a pesquisa fonetizada para buscar variações de grafia"
- Quando BFF retorna erro → Alert Danger: "Erro na pesquisa — tente novamente"

### Pesquisa fonetizada
- Quando marca checkbox → busca inclui variações fonéticas (Wagner/Vagner, Luiz/Luis, etc.)
- Quando desmarca → busca literal (match exato)
- Quando resultado vazio sem fonetizada → sugestão: "Ativar pesquisa fonetizada pode encontrar variações de nomes"

### Filtros
- Quando preenche datas → filtra por período de abertura do registro
- Quando Data Final < Data Inicial → validação inline "Data Final deve ser posterior à Data Inicial"
- Quando muda "Ordenar por" → reordena resultados (Relevância, Data mais recente, Data mais antigo)

### Limpar
- Quando clica Limpar → todos os campos vazios, checkbox desmarcado, resultados removidos, Select volta para "Relevância"

---

## Comportamento responsivo

| Precisa de variante `Layout=Mobile`? | **Sim** — formulário com grid 3 colunas (busca + 2 datas) |
|---|---|
| **Desktop** | Linha 1: Busca (2/3 width) + Data inicial (1/6) + Data final (1/6). Linha 2: Ordenar por (1/2) + Checkbox (1/2). Linha 3: Botões à direita |
| **Mobile** | Stack vertical 1 coluna. Todos os campos full-width. Checkbox com label visível. Botões empilhados |
| **Tablet** | Segue Desktop |

---

## Composição com outros componentes

| Componente | Relação | Composição no Figma |
|---|---|---|
| **BC-13 Input** | Campo de busca, campos de data | **Instância** — 3× (busca + 2 datas) |
| **BC-13 Select** | Ordenação | **Instância** — 1× |
| **BC-13 Checkbox** | Pesquisa fonetizada | **Instância** — 1× |
| **BC-05 Button** | Limpar + Pesquisar | **Instância** — Secondary MD + Primary MD |
| **BC-15 Icons** | Ícones nos botões + tooltip | **Instância** — XS (3×) |
| **BC-03 Alert** | Feedback de erro | **Instância** — Danger |
| **BC-16 Loader** | Loading | **Instância** — Spinner SM |
| **BC-23 Popover** | Tooltip ℹ️ no campo de busca | **Instância** ou tooltip HTML nativo |

---

## Variantes no Figma

| Variante | Property | Descrição |
|---|---|---|
| **Default Desktop** | `State=Default, Layout=Desktop` | Formulário vazio, grid 3 colunas |
| **Default Mobile** | `State=Default, Layout=Mobile` | Stack vertical |
| **Loading Desktop** | `State=Loading, Layout=Desktop` | Spinner no botão, campos disabled |
| **Error Desktop** | `State=Error, Layout=Desktop` | Alert Danger inline |

> 4 variantes.

---

## Tokens utilizados

| Token | Uso |
|---|---|
| `--color-surface` | Fundo do card |
| `--color-border` | Borda do card |
| `--color-primary` | Botão Pesquisar |
| `--color-info` | Ícone tooltip ℹ️ |
| `--color-danger` | Alert de erro |
| `--color-text-primary` | Labels |
| `--color-text-secondary` | Placeholders |
| `--color-border-focus` | Focus ring |
| `--font-body` | Textos |
| `--text-sm` / `--text-base` | Tamanhos |
| `--space-4` | Padding |
| `--space-3` | Gap entre campos |
| `--space-2` | Gap label-campo |
| `--radius-lg` | Card |
| `--radius-sm` | Inputs |
| `--shadow-sm` | Sombra |

---

## Casos excepcionais / bordas

- **Busca com aspas:** "texto exato" → busca literal (match exato no BFF). Documentar no tooltip
- **Busca vazia:** botão Pesquisar desabilitado se campo de busca vazio (campo obrigatório)
- **Fonetizada + período:** ambos os filtros se acumulam — fonetizada no texto E filtro de período
- **Muitos resultados (> 100):** paginação server-side. Exibir contador total + página atual
- **"Data (abertura)":** refere-se à data de abertura do registro/BO, não à data do fato. Tooltip explica

---

## O que está fora deste spec

- **Resultados da pesquisa:** layout da lista é definido no layout de tela
- **Detalhes do registro:** navegar para detalhe é ação da aplicação
- **Algoritmo de fonetização:** lógica server-side no BFF, componente apenas ativa/desativa
- **Busca por voz:** funcionalidade futura, não neste sprint

---

## Critérios de aceite

- [ ] Formulário sem tabs — layout direto com 5 campos + checkbox
- [ ] Campos como instâncias de BC-13 (Input, Select, Checkbox — Regra 11)
- [ ] Botões como instâncias de BC-05 (Regra 11)
- [ ] Tooltip ℹ️ com texto explicativo do campo de busca
- [ ] Checkbox "Pesquisa fonetizada" com label e tooltip
- [ ] Loading com Spinner SM (BC-16, Regra 11)
- [ ] Erro com Alert Danger (BC-03, Regra 11)
- [ ] Variantes Desktop e Mobile (Regra 13)
- [ ] Tokens aplicados (Regra 8)
- [ ] WCAG (cor aten · vis aten) resolvidas
- [ ] Sugestão de fonetizada quando resultado vazio
- [ ] Labels em português
- [ ] Revisado e aprovado por Giuliana
