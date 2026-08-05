---
component-id: SC-03
component-name: Consultar Registro
type: SISP
status: approved
sprint: 7
approved-by: [Giuliana]
approved-date: [2026-08-04]
figma-node-id: [804:6312]
---

# Component Spec — Consultar Registro

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → SC-03 (cor aten · vis aten)
> - `docs/analyses/nielsen-analysis.md` → SC-03 (H-9 **crit** · H-1 aten · H-2 aten · H-3 aten · H-4 aten · H-5 aten · H-6 aten · H-7 aten · H-8 aten)
> - `docs/analyses/inventory.md` → SC-03
> - Screenshot de produção: `uikit-screencapture/...consultar-registro...2026-06-02-19_45_29.png`

> **Padrão compartilhado "Query Form":** SC-02, SC-03 e SC-04 seguem a mesma estrutura — Tabs + Form fields + Action buttons. As soluções WCAG e Nielsen são sistêmicas. Este spec documenta diferenças específicas de SC-03.

---

## O que é

Consultar Registro é o componente de busca de registros policiais (boletins de ocorrência, procedimentos, etc.) no SISP. Diferente de SC-02, este componente é auto-suficiente via BFF — não recebe config object externo. Usa `sisp-lib-form` internamente para renderizar os campos. Suporta 3 modos de busca: por número do registro, por período (com filtros de unidade/módulo/data), e por base nacional (integração federal).

---

## Audiência de uso

- **Policial na DV:** busca registros por número (consulta rápida) ou por período/unidade (consulta complexa). Aba "Período" é a mais usada quando há volume de registros a filtrar
- **Devs CiASC / terceiros:** componente auto-suficiente — adicionar na tela sem configuração. Usa `sisp-lib-form` internamente (confirmar se é BC-13 ou submódulo separado)
- **Demilis (mantenedor):** confirmar relação `sisp-lib-form` vs. BC-13 Forms. Documentar aba "Base Nacional" (integração federal)

---

## Props / API

> **Padrão de API:** Auto-suficiente via BFF (sem props de configuração). O componente usa `sisp-lib-form` internamente.

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| — | — | — | — | Nenhuma prop documentada — componente auto-suficiente |

> ⚠️ **Inventário:** relação entre `sisp-lib-form` e BC-13 Forms não documentada. Confirmar com Demilis se é a mesma base de componentes ou submódulo.

**Convenção Angular (auto-suficiente):**
```html
<sisp-lib-consult-record></sisp-lib-consult-record>
```

---

## Anatomia do componente

### Aba "Nº do Registro" (busca direta)
```
┌──────────────────────────────────────────────────────────────┐
│  [ Nº do Registro ]  [ Período ]  [ Base Nacional ]   Tabs  │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Nº do Registro*                                             │
│  ┌──────────────────────────────────────────────────────┐    │
│  │                                                      │    │
│  └──────────────────────────────────────────────────────┘    │
│  (BC-13 Input)                                               │
│                                                              │
│                           ┌──────────┐  ┌──────────────┐     │
│                           │ ✎ Limpar │  │ 🔍 Pesquisar │     │
│                           └──────────┘  └──────────────┘     │
└──────────────────────────────────────────────────────────────┘
```

### Aba "Período" (busca complexa — mais usada)
```
┌──────────────────────────────────────────────────────────────┐
│  [ Nº do Registro ]  [ Período ]  [ Base Nacional ]   Tabs  │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Unidade*                          Módulo*                   │
│  ┌────┬───────────────────┐       ┌─────────────────────▾┐   │
│  │ N° │ Nome da unidade...│       │ Selecione o módulo...│   │
│  └────┴───────────────────┘       └──────────────────────┘   │
│  (BC-13 Input composto)           (BC-13 Select)             │
│                                                              │
│  Data Inicial*                     Data Final*               │
│  ┌─────────────────────📅┐       ┌─────────────────────📅┐   │
│  │ dd/mm/yyyy            │       │ dd/mm/yyyy            │   │
│  └───────────────────────┘       └───────────────────────┘   │
│  (BC-13 Input date)               (BC-13 Input date)         │
│                                                              │
│  Nº de Registro*                                             │
│  ┌───────────────────┐                                       │
│  │ 10                │                                       │
│  └───────────────────┘                                       │
│  (BC-13 Input number)                                        │
│                                                              │
│                           ┌──────────┐  ┌──────────────┐     │
│                           │ ✎ Limpar │  │ 🔍 Pesquisar │     │
│                           └──────────┘  └──────────────┘     │
└──────────────────────────────────────────────────────────────┘
```

### Composição atômica (Regra 11)

| Elemento | Componente DS | Instância |
|---|---|---|
| Barra de abas | BC-26 Tabs (Contained) | 1× — 3 Tab Items |
| Campos de texto | BC-13 Input | Variável por aba (1-5 campos) |
| Campo de seleção | BC-13 Select | 1× (Módulo, na aba Período) |
| Campos de data | BC-13 Input (type=date) | 2× (Data Inicial, Data Final) |
| Botão Limpar | BC-05 Button Secondary MD | 1× |
| Botão Pesquisar | BC-05 Button Primary MD | 1× |
| Ícones nos botões | BC-15 Icons XS | 2× — borracha + lupa |
| Alert de erro | BC-03 Alert Danger | 1× (quando estado Erro) |

### Campos por aba

| Aba | Campos | Todos obrigatórios? |
|---|---|---|
| **Nº do Registro** | Nº do Registro (Input) | Sim |
| **Período** | Unidade (Input composto: N° + nome) + Módulo (Select) + Data Inicial (Date) + Data Final (Date) + Nº de Registro (Input number) | Sim (todos com *) |
| **Base Nacional** | *Não documentado — integração federal* | Confirmar com Demilis |

> ⚠️ **Campo composto "Unidade":** input com prefixo "N°" embutido + campo de texto. Padrão incomum no DS — pode ser implementado como BC-13 Input com addon ou como variante customizada. Confirmar comportamento com Demilis.

---

## Estados e variantes

| Estado | Descrição visual | Composição |
|---|---|---|
| **Default** | Formulário vazio, primeira aba ativa | Tabs + campos vazios + botões habilitados |
| **Preenchido** | Campos com valores | Campos com valor, Pesquisar habilitado |
| **Loading** | Consulta em andamento | Spinner no botão Pesquisar (BC-16 SM), campos desabilitados |
| **Resultado** | Lista de registros encontrados | Formulário + tabela de resultados abaixo |
| **Vazio** | Zero resultados | Mensagem "Nenhum registro encontrado para o período informado" |
| **Erro** | Falha no BFF | Alert Danger (BC-03) com mensagem descritiva |

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução neste spec |
|---|---|---|
| Uso de cor (cor aten) | Abas diferenciadas por cor verde vs. branco | **BC-26 Tabs (Contained)** resolve: aba ativa com fundo preenchido + texto bold. Diferenciação multicanal (cor + forma + peso) |
| Visual (vis aten) | Estados de loading/erro não documentados | **Estados explícitos:** Loading (Spinner + disabled), Erro (Alert Danger), Vazio (mensagem descritiva). Focus ring em todos os campos interativos |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução neste spec |
|---|---|---|
| H-9 Recuperação (CRIT) | Erro de consulta sem orientação | **Alert Danger (BC-03)** inline: mensagem específica por tipo de erro. "Período inválido — Data Final anterior à Data Inicial" / "Serviço indisponível — tente novamente em alguns minutos". Botão "Tentar novamente" mantém campos preenchidos |
| H-1 Visibilidade (aten) | Sem feedback durante busca | **Spinner (BC-16 SM)** no botão + campos desabilitados. Área de resultados com `aria-live="polite"` |
| H-2 Mundo real (aten) | "Módulo" é jargão interno | Tooltip ℹ️ no label: "Módulo do sistema onde o registro foi criado (ex: BO, Flagrante, Termo)" |
| H-3 Controle (aten) | Sem botão limpar | **Botão "Limpar"** (BC-05 Secondary) com confirmação se há resultados |
| H-4 Consistência (aten) | Auto-suficiente (difere de SC-02 config object) | Padrão documentado como-está. Nota no spec: componente auto-suficiente, sem props de config — usa sisp-lib-form internamente |
| H-5 Prevenção (aten) | Data Final < Data Inicial aceita | **Validação inline:** "Data Final deve ser posterior à Data Inicial". Nº de Registro valida mínimo 1. Campos obrigatórios validados antes de submeter |
| H-6 Reconhecimento (aten) | Campo "Unidade" com prefixo N° sem explicação | **Placeholder** descritivo: "N°" (prefixo fixo) + "Nome da unidade..." (campo pesquisável). Tooltip: "Delegacia ou unidade policial responsável" |
| H-7 Flexibilidade (aten) | Sem atalhos | **Enter** submete, **Tab** navega, **Ctrl+L** limpa |
| H-8 Estética (aten) | Layout Bootstrap padrão | Tokens DS SISP: `--color-primary`, `--font-body`, `--space-*`. Grid de 2 colunas na aba Período |

---

## Regras de acessibilidade

- [ ] Tab bar com `role="tablist"`, abas com `role="tab"` e `aria-selected`
- [ ] Painéis com `role="tabpanel"` e `aria-labelledby`
- [ ] Campos obrigatórios com `aria-required="true"` e indicador visual (*)
- [ ] Campos de data com `aria-label="Data inicial no formato dia/mês/ano"`
- [ ] Campo composto "Unidade" com `aria-label="Número e nome da unidade policial"`
- [ ] Estado de erro com `aria-invalid="true"` e `aria-describedby`
- [ ] Área de resultados com `aria-live="polite"`
- [ ] Focus ring visível: `2px solid var(--color-border-focus)`
- [ ] Contraste mínimo 4.5:1 AA
- [ ] Labels em português

---

## Comportamentos esperados

### Consulta
- Quando seleciona aba → formulário exibe campos correspondentes, campos de outras abas limpos
- Quando preenche campos e clica Pesquisar → Loading: Spinner no botão, campos desabilitados
- Quando BFF retorna resultados → exibe tabela/lista abaixo do formulário
- Quando BFF retorna vazio → mensagem "Nenhum registro encontrado para o período informado"
- Quando BFF retorna erro → Alert Danger inline com mensagem descritiva

### Validação (aba Período)
- Quando Data Final < Data Inicial → inline error "Data Final deve ser posterior à Data Inicial"
- Quando campo obrigatório vazio → inline error "Campo obrigatório"
- Quando Nº de Registro < 1 → inline error "Valor mínimo: 1"

### Limpar
- Quando clica Limpar → todos os campos resetados, resultados limpos, primeira aba ativa

---

## Comportamento responsivo

| Precisa de variante `Layout=Mobile`? | **Sim** — aba Período tem 5 campos em grid |
|---|---|
| **Desktop** | Grid 2 colunas (Unidade + Módulo, Data Inicial + Data Final). Nº Registro em linha separada. Botões à direita |
| **Mobile** | Stack vertical 1 coluna. Todos os campos full-width. Botões empilhados full-width |
| **Tablet** | Segue Desktop |

---

## Composição com outros componentes

| Componente | Relação | Composição no Figma |
|---|---|---|
| **BC-26 Tabs** | Modos de busca (Nº Registro/Período/Base Nacional) | **Instância direta** — Tabs Contained, 3 Tab Items |
| **BC-13 Input** | Campos de texto, data, número | **Instância direta** — variantes por tipo |
| **BC-13 Select** | Módulo | **Instância direta** — Select com opções |
| **BC-05 Button** | Limpar + Pesquisar | **Instância direta** — Secondary MD + Primary MD |
| **BC-15 Icons** | Ícones nos botões | **Instância direta** — XS |
| **BC-03 Alert** | Feedback de erro | **Instância direta** — Danger |
| **BC-16 Loader** | Loading | **Instância direta** — Spinner SM |

---

## Variantes no Figma

| Variante | Property | Descrição |
|---|---|---|
| **Default Desktop** | `State=Default, Layout=Desktop` | Aba Nº Registro ativa, campos vazios |
| **Periodo Desktop** | `State=Periodo, Layout=Desktop` | Aba Período ativa, 5 campos visíveis |
| **Default Mobile** | `State=Default, Layout=Mobile` | Stack vertical |
| **Error Desktop** | `State=Error, Layout=Desktop` | Alert Danger inline |

> 4 variantes. Aba Período merece variante própria por ser a mais complexa (5 campos, grid 2 colunas).

---

## Tokens utilizados

| Token | Uso |
|---|---|
| `--color-surface` | Fundo do card |
| `--color-border` | Borda do card, separadores |
| `--color-primary` | Botão Pesquisar, aba ativa |
| `--color-danger` | Alert de erro |
| `--color-text-primary` | Labels |
| `--color-text-secondary` | Placeholders |
| `--color-border-focus` | Focus ring |
| `--font-body` | Textos (Arial) |
| `--text-sm` / `--text-base` | Tamanhos de texto |
| `--space-4` | Padding do card |
| `--space-3` | Gap entre campos |
| `--space-2` | Gap label-campo |
| `--radius-lg` | Border-radius card |
| `--radius-sm` | Border-radius inputs |
| `--shadow-sm` | Sombra do card |

---

## Casos excepcionais / bordas

- **Campo "Unidade" composto:** input com prefixo fixo "N°" + campo de texto pesquisável. Se o prefixo não couber na spec de BC-13, criar como frame manual com 2 inputs lado a lado
- **Aba "Base Nacional":** integração federal não documentada — confirmar se existe e quais campos contém
- **Período muito longo (> 1 ano):** BFF pode retornar erro de range. Mensagem: "Período máximo de 12 meses por consulta"
- **Muitos resultados:** paginação server-side (BFF controla)
- **Data inválida:** validação HTML5 nativa do input type=date + validação server-side

---

## O que está fora deste spec

- **Layout da área de resultados:** formato será definido no layout de tela
- **Integração `sisp-lib-form` vs. BC-13:** resolver com Demilis — pode impactar implementação Angular
- **Aba "Base Nacional":** aguarda documentação da integração federal
- **Detalhes do registro:** tela de detalhe individual é layout separado

---

## Critérios de aceite

- [ ] Tabs (BC-26 Contained) com 3 abas: Nº do Registro, Período, Base Nacional
- [ ] Aba Período com 5 campos em grid 2 colunas (Desktop)
- [ ] Campos como instâncias de BC-13 Input/Select (Regra 11)
- [ ] Botões como instâncias de BC-05 (Regra 11)
- [ ] Loading com Spinner SM (BC-16, Regra 11)
- [ ] Erro com Alert Danger (BC-03, Regra 11)
- [ ] Variantes Desktop e Mobile (Regra 13)
- [ ] Tokens aplicados (Regra 8)
- [ ] WCAG (cor aten · vis aten) resolvidas
- [ ] Nielsen H-9 (CRIT) resolvida — mensagens de erro específicas com ação corretiva
- [ ] Validação inline: Data Final > Data Inicial, campos obrigatórios
- [ ] Labels em português
- [ ] Revisado e aprovado por Giuliana
