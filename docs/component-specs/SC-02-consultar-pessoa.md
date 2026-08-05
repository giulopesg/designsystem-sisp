---
component-id: SC-02
component-name: Consultar Pessoa
type: SISP
status: approved
sprint: 7
approved-by: [Giuliana]
approved-date: [2026-08-04]
figma-node-id: [804:6190]
---

# Component Spec — Consultar Pessoa

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → SC-02 (cor aten · vis aten)
> - `docs/analyses/nielsen-analysis.md` → SC-02 (H-9 **crit** · H-1 aten · H-2 aten · H-3 aten · H-4 aten · H-5 aten · H-6 aten · H-7 aten · H-8 aten)
> - `docs/analyses/inventory.md` → SC-02
> - Screenshot de produção: `uikit-screencapture/...consultar-pessoa...2026-06-02-19_43_32.png`

> **Padrão compartilhado:** SC-02, SC-03 e SC-04 seguem o mesmo padrão estrutural "Query Form" — Tabs (modo de consulta) + Form fields (variáveis por aba) + Action buttons (Limpar/Pesquisar). As violações WCAG e Nielsen são sistêmicas entre os três.

---

## O que é

Consultar Pessoa é o componente de busca de pessoas no SISP. Permite consultar informações de uma pessoa a partir de diferentes critérios (documento, nome, ou outros identificadores como Nº Base e RG). Faz parte do subgrupo Consultas Policiais da Delegacia Virtual e é um dos componentes mais usados no fluxo operacional policial.

---

## Audiência de uso

- **Policial na DV:** consulta pessoa por CPF, RG, nome ou Nº Base durante atendimento. Precisa de feedback rápido e claro sobre resultados e erros
- **Devs CiASC / terceiros:** integram o componente via config object, definindo quais campos/abas ficam visíveis conforme o contexto de uso (BO, consulta avulsa, etc.)
- **Demilis (mantenedor):** relação entre `camposVisiveis` e abas visíveis não está documentada — formalizar

---

## Props / API

> **Padrão de API:** Config object — `[sispLibConsultPersonConfig]="config"`

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `camposVisiveis` | `string[]` | sim | — | Campos/abas visíveis no formulário. Valores possíveis: `'documento'`, `'nome'`, `'outros'` |
| `onConsultar` | `Function` | sim | — | Callback chamado ao submeter a busca. Recebe o critério de busca como parâmetro |
| `abaInicial` | `string` | não | `'documento'` | Aba selecionada ao iniciar. Deve ser uma das `camposVisiveis` |

> ⚠️ **Nota do inventário:** props documentadas como "entre outros" — props adicionais não detalhadas. Confirmar com Demilis.

**Convenção Angular:**
```html
<sisp-lib-consult-person
  [sispLibConsultPersonConfig]="{
    camposVisiveis: ['documento', 'nome', 'outros'],
    onConsultar: consultarPessoa.bind(this)
  }">
</sisp-lib-consult-person>
```

---

## Anatomia do componente

```
┌──────────────────────────────────────────────────────────────┐
│  [ Documento ]  [ Nome ]  [ Outros ]          ← BC-26 Tabs  │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Tipo de consulta*        Nº Base*                           │
│  ┌─────────────────┐      ┌───────────────────────────────┐  │
│  │ Nº Base        ▾│      │ Digite o número base...       │  │
│  └─────────────────┘      └───────────────────────────────┘  │
│  (BC-13 Select)           (BC-13 Input)                      │
│                                                              │
│                           ┌──────────┐  ┌──────────────┐     │
│                           │ ✎ Limpar │  │ 🔍 Pesquisar │     │
│                           └──────────┘  └──────────────┘     │
│                           (BC-05 Sec)   (BC-05 Primary)      │
└──────────────────────────────────────────────────────────────┘
```

### Composição atômica (Regra 11)

| Elemento | Componente DS | Instância |
|---|---|---|
| Barra de abas | BC-26 Tabs (Contained) | 1× — com N Tab Items conforme `camposVisiveis` |
| Campos de formulário | BC-13 Input / Select | Variável por aba (1-3 campos) |
| Botão Limpar | BC-05 Button Secondary MD | 1× — com ícone de borracha (BC-15 Icons) |
| Botão Pesquisar | BC-05 Button Primary MD | 1× — com ícone de lupa (BC-15 Icons) |
| Ícones nos botões | BC-15 Icons XS | 2× — borracha + lupa |

### Campos por aba

| Aba | Campos | Tipo |
|---|---|---|
| **Documento** | Tipo de documento (Select) + Nº do documento (Input) | Config object define opções |
| **Nome** | Nome completo (Input) + filtros opcionais | Busca textual |
| **Outros** | Tipo de consulta (Select: Nº Base, etc.) + Campo de busca (Input) | Critério variável |

> Campos inferidos do screenshot. Confirmar detalhamento de cada aba com Demilis.

---

## Estados e variantes

| Estado | Descrição visual | Composição |
|---|---|---|
| **Default** | Formulário vazio, primeira aba ativa | Tabs + campos vazios + botões habilitados |
| **Preenchido** | Campos com valores, pronto para submissão | Campos com valor, Pesquisar habilitado |
| **Loading** | Consulta em andamento | Botão Pesquisar com Spinner (BC-16 SM), campos desabilitados |
| **Resultado** | Tabela/lista com resultados | Formulário + área de resultado abaixo (BC-25 Table ou lista) |
| **Vazio** | Consulta retornou zero resultados | Mensagem "Nenhum resultado encontrado" + sugestão de ajustar filtros |
| **Erro** | Falha no BFF | Alert Danger (BC-03) inline com mensagem descritiva + botão "Tentar novamente" |

> ⚠️ **Inventário:** estados Resultado e Erro não documentados na versão atual. Layout do resultado não especificado.

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução neste spec |
|---|---|---|
| Uso de cor (cor aten) | Abas diferenciadas por cor verde vs. branco — sem indicador adicional | **BC-26 Tabs (Contained)** já resolve: aba ativa tem fundo preenchido (fill) + texto bold — diferenciação por cor + forma + peso. Tabs do DS usam `--color-primary` como fill e borda inferior na variante Underline |
| Visual (vis aten) | Estados de loading e erro não documentados | **4 estados visuais explícitos:** Loading (Spinner no botão + campos disabled), Erro (Alert Danger inline com mensagem), Vazio (mensagem sem resultados), Resultado (tabela abaixo do form). Focus ring `var(--color-border-focus)` em todos os campos |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução neste spec |
|---|---|---|
| H-9 Recuperação (CRIT) | Erro de consulta sem mensagem clara — usuário não sabe o que corrigir | **Alert Danger (BC-03)** inline abaixo do formulário com: (1) mensagem descritiva do erro ("Serviço temporariamente indisponível" / "Nenhum resultado para os critérios informados"), (2) botão "Tentar novamente" para resubmeter, (3) sugestão de ação corretiva ("Verifique o número do documento e tente novamente") |
| H-1 Visibilidade (aten) | Sem feedback durante consulta | **Spinner (BC-16 SM)** no botão Pesquisar durante loading. Campos desabilitados para indicar processamento. `aria-live="polite"` na área de resultados |
| H-2 Mundo real (aten) | Labels técnicos ("camposVisiveis") | Labels em português natural: "Documento", "Nome", "Outros". Campos com labels descritivos: "Nº do CPF", "Nome completo" |
| H-3 Controle (aten) | Sem botão de limpar formulário | **Botão "Limpar"** (BC-05 Secondary) reseta todos os campos. Confirma antes de limpar se já há resultados visíveis |
| H-4 Consistência (aten) | Config object inconsistente com SC-03 (auto-suficiente) | Documentar ambos os padrões. SC-02 usa config object, SC-03 usa BFF direto — diferença aceita pois são componentes distintos |
| H-5 Prevenção (aten) | Sem validação antes de submeter | **Validação inline:** campos obrigatórios (*) validados antes de submeter. Botão Pesquisar desabilitado se campos obrigatórios vazios. Máscara de documento formata automaticamente |
| H-6 Reconhecimento (aten) | Campos sem placeholder descritivo | **Placeholders** indicam formato esperado: "000.000.000-00" para CPF, "Digite o nome completo" para nome |
| H-7 Flexibilidade (aten) | Sem atalhos de teclado | **Enter** submete formulário na aba ativa. **Tab** navega entre campos. **Ctrl+L** limpa formulário |
| H-8 Estética (aten) | Layout Bootstrap padrão sem identidade SISP | Tokens DS SISP aplicados: `--color-primary` nos botões, `--font-body` nos campos, `--space-*` para espaçamento. Tabs Contained com visual DS |

---

## Regras de acessibilidade

- [ ] Tab bar com `role="tablist"`, cada aba com `role="tab"` e `aria-selected`
- [ ] Painel de conteúdo de cada aba com `role="tabpanel"` e `aria-labelledby` referenciando a aba
- [ ] Campos obrigatórios com `aria-required="true"` e indicador visual (*)
- [ ] Estado de erro com `aria-invalid="true"` e `aria-describedby` apontando para mensagem de erro
- [ ] Botão Pesquisar com `aria-label="Pesquisar pessoa"` quando sem texto visível
- [ ] Área de resultados com `aria-live="polite"` — anuncia novos resultados
- [ ] Focus ring visível: `2px solid var(--color-border-focus)`
- [ ] Navegação por teclado: Arrow keys entre abas, Tab entre campos, Enter para submeter
- [ ] Contraste mínimo 4.5:1 AA em todos os textos
- [ ] Labels em português
- [ ] Spinner com `aria-label="Pesquisando..."` durante loading

---

## Comportamentos esperados

### Consulta
- Quando seleciona aba → formulário exibe campos correspondentes. Campos de outras abas são limpos
- Quando preenche campos e clica Pesquisar → estado Loading: Spinner no botão, campos desabilitados
- Quando BFF retorna resultados → estado Resultado: tabela/lista exibida abaixo do formulário
- Quando BFF retorna vazio → estado Vazio: mensagem "Nenhum resultado encontrado para os critérios informados"
- Quando BFF retorna erro → estado Erro: Alert Danger inline com mensagem descritiva + "Tentar novamente"

### Validação
- Quando campo obrigatório vazio e clica Pesquisar → inline error no campo ("Campo obrigatório")
- Quando formato de documento inválido → inline error ("Formato inválido. Use: 000.000.000-00")
- Quando todos os campos válidos → submete consulta

### Limpar
- Quando clica Limpar → todos os campos resetados. Área de resultados limpa. Primeira aba selecionada
- Quando há resultados visíveis e clica Limpar → confirma "Limpar formulário e resultados?" (prevenção H-5)

---

## Comportamento responsivo

> Regra 13 — Responsividade obrigatória.

| Precisa de variante `Layout=Mobile`? | **Sim** — formulário com tabs e múltiplos campos |
|---|---|
| **Desktop** | Tabs horizontais. Campos em grid 2 colunas (label/input lado a lado). Botões alinhados à direita |
| **Mobile** | Tabs horizontais (scroll se necessário). Campos em stack vertical 1 coluna. Botões full-width empilhados |
| **Tablet** | Segue Desktop com padding `--space-card-padding` ajustado |

---

## Composição com outros componentes

> **Regra 11 — Composição atômica:** todo elemento que já exista como componente no DS deve ser instância.

| Componente | Relação | Composição no Figma |
|---|---|---|
| **BC-26 Tabs** | Seleção de modo de consulta (Documento/Nome/Outros) | **Instância direta** — Tabs Contained, 3 Tab Items |
| **BC-13 Input** | Campos de texto (Nº documento, nome, etc.) | **Instância direta** — Input com label e placeholder |
| **BC-13 Select** | Tipo de documento, tipo de consulta | **Instância direta** — Select com label e opções |
| **BC-05 Button** | Ações Limpar e Pesquisar | **Instância direta** — Secondary MD + Primary MD |
| **BC-15 Icons** | Ícones nos botões (borracha, lupa) | **Instância direta** — XS dentro dos botões |
| **BC-03 Alert** | Feedback de erro | **Instância direta** — Alert Danger inline |
| **BC-16 Loader** | Loading durante consulta | **Instância direta** — Spinner SM no botão |
| **BC-25 Table** | Área de resultados (se tabular) | **Instância direta** — Table Default |

> **Regra 12 — Auditoria:** "este elemento já existe?" Tabs, Input, Select, Button, Alert, Loader, Table — todos existem como Base Components. Consultar Pessoa é composição pura de componentes existentes com lógica de domínio policial.

---

## Variantes no Figma

| Variante | Property | Descrição |
|---|---|---|
| **Default Desktop** | `State=Default, Layout=Desktop` | Formulário vazio, aba Documento ativa |
| **Default Mobile** | `State=Default, Layout=Mobile` | Stack vertical, campos full-width |
| **Loading Desktop** | `State=Loading, Layout=Desktop` | Spinner no botão, campos disabled |
| **Error Desktop** | `State=Error, Layout=Desktop` | Alert Danger inline abaixo do form |

> 4 variantes mínimas. Resultado é layout de tela (Sprint 8+), não variante do componente.

---

## Tokens utilizados

| Token | Uso |
|---|---|
| `--color-surface` | Fundo do card do formulário |
| `--color-border` | Borda do card, separadores |
| `--color-primary` | Botão Pesquisar, aba ativa (via BC-26) |
| `--color-danger` | Alert de erro |
| `--color-text-primary` | Labels dos campos |
| `--color-text-secondary` | Placeholders |
| `--color-text-muted` | Texto auxiliar |
| `--color-border-focus` | Focus ring em campos e botões |
| `--font-body` | Todos os textos (Arial) |
| `--text-sm` | Labels dos campos (14px) |
| `--text-base` | Valores dos campos (16px) |
| `--space-4` | Padding interno do card (16px) |
| `--space-3` | Gap entre campos (12px) |
| `--space-2` | Gap entre label e campo (8px) |
| `--radius-lg` | Border-radius do card (8px) |
| `--radius-sm` | Border-radius dos inputs (4px) |
| `--shadow-sm` | Sombra do card |

---

## Casos excepcionais / bordas

- **Nenhuma aba visível:** se `camposVisiveis` é array vazio, exibir mensagem "Configuração inválida — nenhum campo de consulta configurado" (prevenção H-5)
- **Resultado muito grande (> 100 registros):** paginação server-side. Exibir total de resultados + paginação (BC-05 Button group ou paginação nativa)
- **Timeout de BFF:** após 30s sem resposta, exibir Alert Warning "A consulta está demorando mais que o esperado. Tente novamente"
- **Máscara de documento:** CPF = 000.000.000-00 (11 dígitos). RG = formato varia por estado — aceitar livre com validação server-side
- **Múltiplas consultas simultâneas:** segunda consulta cancela a primeira. Exibir apenas o resultado mais recente
- **Campos com conteúdo colado (paste):** máscara aplica automaticamente ao colar

---

## O que está fora deste spec

- **Layout da área de resultados:** formato da tabela/lista de resultados será definido no layout de tela (Sprint 8+)
- **Detalhes da pessoa:** tela de detalhe individual é um layout, não parte deste componente
- **Integração com SC-03/SC-04:** cada consulta é componente independente, mesmo compartilhando padrão visual
- **Impressão de resultados:** funcionalidade de exportação/impressão é responsabilidade da aplicação host
- **Props não documentadas:** "entre outros" do inventário — confirmar com Demilis antes do Figma

---

## Critérios de aceite

- [ ] Tabs (BC-26 Contained) com 3 abas: Documento, Nome, Outros
- [ ] Campos de formulário como instâncias de BC-13 Input/Select (Regra 11)
- [ ] Botões Limpar (Secondary) e Pesquisar (Primary) como instâncias de BC-05 (Regra 11)
- [ ] Estado Loading com Spinner SM no botão (BC-16, Regra 11)
- [ ] Estado Erro com Alert Danger inline (BC-03, Regra 11)
- [ ] Variantes Desktop e Mobile (Regra 13)
- [ ] Todos os tokens aplicados — sem valores hardcoded (Regra 8)
- [ ] Contraste verificado — mínimo 4.5:1 AA
- [ ] WCAG (cor aten · vis aten) resolvidas — tabs com diferenciação multicanal, estados visuais explícitos
- [ ] Nielsen H-9 (CRIT) resolvida — mensagens de erro descritivas com ação corretiva
- [ ] Labels em português
- [ ] Revisado e aprovado por Giuliana
