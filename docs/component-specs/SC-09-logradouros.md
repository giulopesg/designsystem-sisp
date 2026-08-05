---
component-id: SC-09
component-name: Logradouros
type: SISP
status: approved
sprint: 7
approved-by: [Giuliana]
approved-date: [2026-08-04]
figma-node-id: [804:6939]
---

# Component Spec — Logradouros

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → SC-09 (ok em todas as dimensões — único SC com WCAG ok)
> - `docs/analyses/nielsen-analysis.md` → SC-09 (H-9 **crit** · H-1 aten · H-2 aten · H-4 aten · H-5 aten · H-6 aten)
> - `docs/analyses/inventory.md` → SC-09
> - Screenshot de produção: `uikit-screencapture/...logradouros...2026-06-02-19_55_50.png`

> ⚠️ **Padrão Angular diferente.** SC-09 é o único componente SISP que usa `@Input/@Output` nativo ao invés de config object. Decisão: documentar como-está para retrocompatibilidade — recomendação para novos componentes é config object.

---

## O que é

Logradouros é o componente de busca e seleção de endereços no SISP. Permite buscar endereços por CEP (máscara) ou por logradouro (campos de localização: país, estado, município, bairro, nome). Essencial para formulários que requerem endereço (B.O., cadastros, consultas). O componente emite o endereço selecionado via `@Output`, permitindo à aplicação host consumir o resultado.

---

## Audiência de uso

- **Policial na DV:** busca endereço durante preenchimento de B.O. — por CEP (mais rápido) ou por nome do logradouro (quando CEP desconhecido). Pode restringir a Santa Catarina (`apenasSantaCatarina=true`)
- **Devs CiASC / terceiros:** integram via `@Input/@Output` — padrão diferente dos demais SCs (inconsistência documentada)
- **Demilis (mantenedor):** confirmar vocabulário ("Consultar" vs. "Pesquisar" — inconsistência com SC-02/03/04)

---

## Props / API

> **Padrão de API:** Angular nativo — `@Input/@Output` (difere do padrão config object). Padrão documentado como-está.

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `[apenasSantaCatarina]` | `boolean` | não | `false` | Restringe resultados a SC. Útil para consultas estaduais |
| `(selecionaLogradouro)` | `EventEmitter<Logradouro>` | não | — | Emitido quando o usuário seleciona um endereço da lista |

**Logradouro (interface inferida):**
```typescript
interface Logradouro {
  cep: string;
  logradouro: string;    // Nome da rua/avenida
  bairro: string;
  municipio: string;
  estado: string;
  pais?: string;         // Default: "Brasil"
  complemento?: string;
}
```

**Convenção Angular (@Input/@Output):**
```html
<sisp-lib-logradouros
  [apenasSantaCatarina]="true"
  (selecionaLogradouro)="onSelecionarLogradouro($event)">
</sisp-lib-logradouros>
```

> ⚠️ **Componente usa nome em inglês** no selector (`sisp-lib-logradouros`) mas em português na interface. OK — selector é interno ao código.

---

## Anatomia do componente

### Aba "Buscar por CEP" (default)
```
┌──────────────────────────────────────────────────────────────┐
│  [ 📍 Buscar por CEP ]  [ 🏠 Buscar por Logradouro ]  Tabs │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  CEP*                                                        │
│  ┌──────────────────────────────────────────────────────┐    │
│  │ __.__-___                                            │    │
│  └──────────────────────────────────────────────────────┘    │
│  (BC-13 Input com máscara)                                   │
│                                                              │
│                           ┌──────────┐  ┌──────────────┐     │
│                           │ ✎ Limpar │  │ 🔍 Consultar │     │
│                           └──────────┘  └──────────────┘     │
└──────────────────────────────────────────────────────────────┘
```

### Aba "Buscar por Logradouro"
```
┌──────────────────────────────────────────────────────────────┐
│  [ 📍 Buscar por CEP ]  [ 🏠 Buscar por Logradouro ]  Tabs │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  País        Estado          Município        Bairro         │
│  ┌────────┐  ┌────────────┐  ┌─────────────┐  ┌──────────┐  │
│  │ Brasil │  │ SC        ▾│  │ Selecione..▾│  │          │  │
│  └────────┘  └────────────┘  └─────────────┘  └──────────┘  │
│                                                              │
│  Logradouro*                                                 │
│  ┌──────────────────────────────────────────────────────┐    │
│  │ Nome do logradouro...                                │    │
│  └──────────────────────────────────────────────────────┘    │
│                                                              │
│                           ┌──────────┐  ┌──────────────┐     │
│                           │ ✎ Limpar │  │ 🔍 Consultar │     │
│                           └──────────┘  └──────────────┘     │
└──────────────────────────────────────────────────────────────┘
```

### Lista de resultados (após consulta)
```
┌──────────────────────────────────────────────────────────────┐
│  Resultados (3 encontrados)                                  │
│  ──────────────────────────────────────────────────────────  │
│  ○ Rua XV de Novembro, 100 — Centro, Florianópolis/SC       │
│  ○ Rua XV de Novembro, 200 — Centro, Florianópolis/SC       │
│  ○ Rua XV de Novembro, 300 — Trindade, Florianópolis/SC     │
│                                                              │
│  [Selecionar]                                                │
└──────────────────────────────────────────────────────────────┘
```

### Composição atômica (Regra 11)

| Elemento | Componente DS | Instância |
|---|---|---|
| Barra de abas | BC-26 Tabs (Contained) | 1× — 2 Tab Items com ícones |
| Campo CEP | BC-13 Input (máscara) | 1× |
| Campos de localização | BC-13 Input / Select | 4-5× (País, Estado, Município, Bairro, Logradouro) |
| Botão Limpar | BC-05 Button Secondary MD | 1× |
| Botão Consultar | BC-05 Button Primary MD | 1× |
| Ícones nos botões/tabs | BC-15 Icons XS | 4× (📍 CEP, 🏠 Logradouro, borracha, lupa) |
| Alert de erro | BC-03 Alert Danger | 1× (quando erro) |
| Lista de resultados | Radio group ou lista selecionável | Lista com `role="listbox"` |

---

## Estados e variantes

| Estado | Descrição visual | Composição |
|---|---|---|
| **Default** | Aba CEP ativa, campo vazio com máscara | Tabs + campo CEP + botões |
| **Logradouro** | Aba Logradouro ativa, campos de localização | Tabs + campos em grid + botões |
| **Loading** | Consulta em andamento | Spinner no botão, campos disabled |
| **Resultados** | Lista de endereços encontrados | Form + lista selecionável abaixo |
| **Selecionado** | Endereço escolhido | Item selecionado com visual de confirmação |
| **Vazio** | Nenhum resultado | Mensagem "Nenhum endereço encontrado para o CEP informado" |
| **Erro** | Falha no BFF / CEP inválido | Alert Danger inline |

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução neste spec |
|---|---|---|
| Contraste (ok) | Sem violação documentada | Manter — verificar contraste na implementação Figma |
| Visual (ok) | Sem violação documentada | Manter — adicionar estados Loading/Erro/Vazio |

> SC-09 é o único componente SISP com WCAG ok em todas as dimensões. Spec resolve preventivamente os estados não documentados.

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução neste spec |
|---|---|---|
| H-9 Recuperação (CRIT) | Erro de consulta sem orientação | **Alert Danger (BC-03)** com mensagens específicas: "CEP não encontrado — verifique o formato (00000-000)" / "Nenhum endereço encontrado para os critérios informados. Tente ampliar a busca" / "Serviço de CEP temporariamente indisponível" |
| H-1 Visibilidade (aten) | Sem feedback durante consulta | **Spinner** no botão + campos disabled. `aria-live` na área de resultados |
| H-2 Mundo real (aten) | "Logradouro" pode ser jargão para usuários leigos | **Label** mantido "Logradouro" (termo correto em contexto institucional). Placeholder: "Nome da rua, avenida ou praça" para contextualizar |
| H-4 Consistência (aten) | Padrão @Input/@Output (difere de todos os outros SCs). Botão "Consultar" (outros usam "Pesquisar") | **Documentar** discrepância: SC-09 usa @Input/@Output por design original. **Botão:** manter "Consultar" — semanticamente correto para endereços (consulta de CEP, não pesquisa textual). Anotar como exceção aceita |
| H-5 Prevenção (aten) | Máscara de CEP pode aceitar formato inválido | **Validação** de formato CEP (8 dígitos + máscara __.__-___) antes de submeter. Estado/Município dependentes: listar municipios do estado selecionado (cascading selects) |
| H-6 Reconhecimento (aten) | Formato do CEP não indicado | **Máscara visual** __.__-___ no campo. Helper text: "Formato: 00000-000" |

---

## Regras de acessibilidade

- [ ] Tab bar com `role="tablist"`, abas com `role="tab"` e `aria-selected`
- [ ] Campo CEP com `aria-label="CEP no formato 00000-000"` e `aria-required="true"`
- [ ] Máscara de CEP anunciada ao screen reader
- [ ] Campos de localização com labels descritivos
- [ ] Lista de resultados com `role="listbox"`, itens com `role="option"` e `aria-selected`
- [ ] Cascading selects: ao mudar Estado, Município atualiza com `aria-live="polite"`
- [ ] Focus ring: `2px solid var(--color-border-focus)`
- [ ] Contraste 4.5:1 AA
- [ ] Labels em português
- [ ] Ícones nas abas com `aria-hidden="true"` (texto da aba é suficiente)

---

## Comportamentos esperados

### Busca por CEP
- Quando digita CEP e clica Consultar → Loading: Spinner + disabled
- Quando BFF retorna endereço → preenche automaticamente (logradouro, bairro, município, estado). Emite `(selecionaLogradouro)` automaticamente se resultado único
- Quando BFF retorna múltiplos → lista de resultados para seleção
- Quando CEP inválido → inline error "CEP inválido — formato: 00000-000"
- Quando CEP não encontrado → Alert Danger "CEP não encontrado nos Correios"

### Busca por Logradouro
- Quando `apenasSantaCatarina=true` → Estado fixo em "SC", campo de estado desabilitado
- Quando seleciona Estado → Município carrega opções do estado (cascading)
- Quando preenche Logradouro e clica Consultar → Loading + busca no BFF
- Quando BFF retorna → lista de endereços encontrados
- Quando seleciona endereço da lista → emite `(selecionaLogradouro)` com dados completos

### Seleção
- Quando seleciona endereço → visual de confirmação (radio checked ou highlight)
- Quando clica "Selecionar" ou double-click → emite evento e fecha lista (se dentro de modal/form)

---

## Comportamento responsivo

| Precisa de variante `Layout=Mobile`? | **Sim** — aba Logradouro tem grid 4 colunas |
|---|---|
| **Desktop** | Aba CEP: 1 campo. Aba Logradouro: grid 4 colunas (País, Estado, Município, Bairro) + Logradouro full-width |
| **Mobile** | Aba CEP: mesmo. Aba Logradouro: stack vertical, todos os campos full-width |
| **Tablet** | Segue Desktop |

---

## Composição com outros componentes

| Componente | Relação | Composição no Figma |
|---|---|---|
| **BC-26 Tabs** | 2 modos de busca (CEP / Logradouro) | **Instância** — Tabs Contained, 2 Tab Items com ícones |
| **BC-13 Input** | CEP (máscara), País, Bairro, Logradouro | **Instância** — 4× |
| **BC-13 Select** | Estado, Município (cascading) | **Instância** — 2× |
| **BC-05 Button** | Limpar + Consultar | **Instância** — Secondary MD + Primary MD |
| **BC-15 Icons** | Ícones nas abas e botões | **Instância** — XS (4×) |
| **BC-03 Alert** | Feedback de erro | **Instância** — Danger |
| **BC-16 Loader** | Loading | **Instância** — Spinner SM |

---

## Variantes no Figma

| Variante | Property | Descrição |
|---|---|---|
| **CEP Desktop** | `State=CEP, Layout=Desktop` | Aba CEP ativa, 1 campo com máscara |
| **CEP Mobile** | `State=CEP, Layout=Mobile` | Mesmo layout (1 campo) |
| **Logradouro Desktop** | `State=Logradouro, Layout=Desktop` | Aba Logradouro, grid 4 colunas |
| **Logradouro Mobile** | `State=Logradouro, Layout=Mobile` | Stack vertical |
| **Resultados** | `State=Results, Layout=Desktop` | Lista de resultados selecionável |
| **Error** | `State=Error, Layout=Desktop` | Alert Danger inline |

> 6 variantes. Aba Logradouro precisa de Mobile por causa do grid 4 colunas.

---

## Tokens utilizados

| Token | Uso |
|---|---|
| `--color-surface` | Fundo do card |
| `--color-border` | Borda do card |
| `--color-primary` | Botão Consultar, aba ativa, item selecionado |
| `--color-danger` | Alert de erro |
| `--color-text-primary` | Labels, texto dos resultados |
| `--color-text-secondary` | Placeholders, metadata dos resultados |
| `--color-border-focus` | Focus ring |
| `--color-bg-subtle` | Fundo do item selecionado na lista |
| `--font-body` | Textos |
| `--text-sm` / `--text-base` | Tamanhos |
| `--space-4` | Padding |
| `--space-3` | Gap |
| `--radius-lg` | Card |
| `--radius-sm` | Inputs |

---

## Casos excepcionais / bordas

- **CEP de área rural:** pode retornar endereço genérico (sem logradouro). Exibir dados disponíveis
- **Município homônimo:** vários municípios com mesmo nome em estados diferentes. Lista deve incluir UF
- **`apenasSantaCatarina=true` em aba CEP:** validar que CEP informado é de SC. Se não, mensagem "Este CEP não pertence a Santa Catarina"
- **Sem conexão com API dos Correios:** fallback para busca interna do SISP. Alert Info "Usando base local — dados podem estar desatualizados"
- **Endereço com complemento:** complemento não está no formulário de busca — é responsabilidade do formulário pai
- **Cascading selects lentos:** Loading inline no Select de Município enquanto carrega

---

## O que está fora deste spec

- **Mapa / geolocalização:** componente é textual, sem mapa
- **Complemento / número:** campo de número e complemento é responsabilidade do formulário pai
- **Autocomplete de endereço:** Google Places ou similar — funcionalidade futura
- **Validação de endereço existente:** componente busca, não valida existência real

---

## Critérios de aceite

- [ ] Tabs (BC-26 Contained) com 2 abas: CEP e Logradouro
- [ ] Máscara de CEP (__.__-___) com validação
- [ ] Aba Logradouro com cascading selects (Estado → Município)
- [ ] Lista de resultados selecionável
- [ ] `@Output (selecionaLogradouro)` emite endereço selecionado
- [ ] `@Input [apenasSantaCatarina]` restringe a SC
- [ ] Instâncias de BC-26, BC-13, BC-05, BC-15, BC-03, BC-16 (Regra 11)
- [ ] Variantes Desktop e Mobile para aba Logradouro (Regra 13)
- [ ] WCAG ok — manter e verificar
- [ ] Nielsen H-9 (CRIT) resolvida — mensagens de erro específicas
- [ ] Botão "Consultar" (não "Pesquisar") — exceção aceita para este componente
- [ ] Labels em português
- [ ] Revisado e aprovado por Giuliana
