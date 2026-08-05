---
component-id: SC-04
component-name: Consultar Veículo
type: SISP
status: approved
sprint: 7
approved-by: [Giuliana]
approved-date: [2026-08-04]
figma-node-id: [804:6390]
---

# Component Spec — Consultar Veículo

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → SC-04 (cor aten · vis aten)
> - `docs/analyses/nielsen-analysis.md` → SC-04 (H-9 **crit** · H-1 aten · H-2 aten · H-3 aten · H-4 aten · H-5 aten · H-6 aten · H-7 aten · H-8 aten)
> - `docs/analyses/inventory.md` → SC-04
> - Screenshot de produção: `uikit-screencapture/...consultar-veiculo...2026-06-02-19_47_22.png`

> **Padrão compartilhado "Query Form":** SC-02, SC-03 e SC-04 seguem a mesma estrutura. Este spec documenta diferenças específicas de SC-04.

---

## O que é

Consultar Veículo é o componente de busca de veículos no SISP. Permite consultar informações de um veículo por placa, chassi, RENAVAM ou fragmento (busca parcial — feature policial específica para casos onde apenas parte do identificador é conhecido). Faz parte do subgrupo Consultas Policiais da DV.

---

## Audiência de uso

- **Policial na DV:** consulta veículo durante atendimento — por placa (mais comum), chassi, RENAVAM, ou fragmento (caso parcial). "Fragmento" é busca por substring, funcionalidade policial específica para situações onde a placa/chassi completo não é conhecido (ex: testemunha visual parcial)
- **Devs CiASC / terceiros:** integram via config object, definindo quais campos ficam visíveis
- **Demilis (mantenedor):** confirmar formato de máscara de placa (antigo AAA-0000 ou Mercosul AAA0A00 ou ambos)

---

## Props / API

> **Padrão de API:** Config object — `[sispLibConsultVehicleConfig]="config"`

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `camposVisiveis` | `string[]` | sim | — | Abas visíveis: `'placa'`, `'chassi'`, `'renavam'`, `'fragmento'` |
| `onConsultar` | `Function` | sim | — | Callback de submissão com critério de busca |
| `abaInicial` | `string` | não | `'placa'` | Aba ativa ao iniciar |

**Convenção Angular:**
```html
<sisp-lib-consult-vehicle
  [sispLibConsultVehicleConfig]="{
    camposVisiveis: ['placa', 'chassi', 'renavam', 'fragmento'],
    onConsultar: consultarVeiculo.bind(this)
  }">
</sisp-lib-consult-vehicle>
```

---

## Anatomia do componente

### Aba "Placa" (mais usada)
```
┌──────────────────────────────────────────────────────────────┐
│  [ Placa ]  [ Chassi ]  [ Renavam ]  [ Fragmento ]    Tabs  │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Nº da Placa*                                                │
│  ┌──────────────────────────────────────────────────────┐    │
│  │ ___-____                                             │    │
│  └──────────────────────────────────────────────────────┘    │
│  (BC-13 Input com máscara)                                   │
│                                                              │
│                           ┌──────────┐  ┌──────────────┐     │
│                           │ ✎ Limpar │  │ 🔍 Pesquisar │     │
│                           └──────────┘  └──────────────┘     │
└──────────────────────────────────────────────────────────────┘
```

### Composição atômica (Regra 11)

| Elemento | Componente DS | Instância |
|---|---|---|
| Barra de abas | BC-26 Tabs (Contained) | 1× — 4 Tab Items |
| Campos de texto | BC-13 Input | 1× por aba (variável) |
| Botão Limpar | BC-05 Button Secondary MD | 1× |
| Botão Pesquisar | BC-05 Button Primary MD | 1× |
| Ícones nos botões | BC-15 Icons XS | 2× |
| Alert de erro | BC-03 Alert Danger | 1× (quando estado Erro) |

### Campos por aba

| Aba | Campo | Tipo | Máscara/Validação |
|---|---|---|---|
| **Placa** | Nº da Placa* | Input com máscara | `AAA-0000` (antigo) ou `AAA0A00` (Mercosul) |
| **Chassi** | Nº do Chassi* | Input | 17 caracteres alfanuméricos |
| **Renavam** | Nº RENAVAM* | Input | 11 dígitos numéricos |
| **Fragmento** | Fragmento* | Input | Mínimo 3 caracteres (substring) |

> ⚠️ **Máscara de placa:** confirmar com Demilis se aceita ambos os formatos (antigo + Mercosul). O campo deve aceitar os dois e validar automaticamente.

---

## Estados e variantes

| Estado | Descrição visual | Composição |
|---|---|---|
| **Default** | Formulário vazio, aba Placa ativa | Tabs + campo vazio + botões |
| **Preenchido** | Campo com valor, pronto para submissão | Máscara aplicada, Pesquisar habilitado |
| **Loading** | Consulta em andamento | Spinner no botão, campo desabilitado |
| **Resultado** | Dados do veículo encontrado | Formulário + card/tabela de resultado |
| **Vazio** | Zero resultados | Mensagem "Nenhum veículo encontrado" |
| **Erro** | Falha no BFF | Alert Danger com mensagem descritiva |

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução neste spec |
|---|---|---|
| Uso de cor (cor aten) | Abas diferenciadas apenas por cor | **BC-26 Tabs (Contained)** resolve: aba ativa com fill + bold. Diferenciação multicanal |
| Visual (vis aten) | Estados não documentados | **Estados explícitos:** Loading, Erro, Vazio. Focus ring. Máscara de placa com formato visível |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução neste spec |
|---|---|---|
| H-9 Recuperação (CRIT) | Erro sem orientação de correção | **Alert Danger (BC-03)** com mensagens específicas: "Placa não encontrada — verifique o formato (AAA-0000 ou AAA0A00)" / "Chassi inválido — deve conter 17 caracteres" / "Serviço indisponível". Botão "Tentar novamente" mantém campo preenchido |
| H-1 Visibilidade (aten) | Sem feedback de loading | **Spinner** no botão + campo disabled. `aria-live` na área de resultados |
| H-2 Mundo real (aten) | "Fragmento" é jargão policial | Tooltip ℹ️: "Busca parcial — digite parte da placa ou chassi quando o identificador completo não é conhecido" |
| H-3 Controle (aten) | Sem limpar | **Botão "Limpar"** reseta campo e resultados |
| H-4 Consistência (aten) | Padrão config object (consistente com SC-02) | OK — ambos usam `[sispLib*Config]` |
| H-5 Prevenção (aten) | Máscara não documentada, formato livre | **Máscara automática** no campo Placa (formata enquanto digita). **Validação** de formato antes de submeter. Fragmento exige mínimo 3 caracteres |
| H-6 Reconhecimento (aten) | Formato da placa não indicado | **Placeholder** com máscara visual: "AAA-0000" ou helper text "Formatos aceitos: AAA-0000 (antigo) ou AAA0A00 (Mercosul)" |
| H-7 Flexibilidade (aten) | Sem atalhos | **Enter** submete, **Tab** navega |
| H-8 Estética (aten) | Layout Bootstrap padrão | Tokens DS SISP aplicados |

---

## Regras de acessibilidade

- [ ] Tab bar com `role="tablist"`, abas com `role="tab"` e `aria-selected`
- [ ] Painéis com `role="tabpanel"` e `aria-labelledby`
- [ ] Campo com `aria-required="true"` e indicador visual (*)
- [ ] Máscara de placa anunciada ao screen reader: `aria-label="Número da placa no formato A A A traço zero zero zero zero"`
- [ ] Estado de erro com `aria-invalid="true"` e `aria-describedby`
- [ ] Área de resultados com `aria-live="polite"`
- [ ] Focus ring: `2px solid var(--color-border-focus)`
- [ ] Contraste 4.5:1 AA
- [ ] Labels em português
- [ ] Tooltip "Fragmento" com `aria-describedby`

---

## Comportamentos esperados

### Consulta
- Quando seleciona aba → campo correspondente exibido, campo anterior limpo
- Quando digita placa → máscara aplica automaticamente (AAA-0000 ou AAA0A00)
- Quando clica Pesquisar → Loading: Spinner + campo disabled
- Quando BFF retorna resultado → exibe dados do veículo
- Quando BFF retorna vazio → "Nenhum veículo encontrado para a placa informada"
- Quando BFF retorna erro → Alert Danger com mensagem e "Tentar novamente"

### Validação
- Quando placa incompleta → "Placa incompleta — formato: AAA-0000 ou AAA0A00"
- Quando chassi ≠ 17 caracteres → "Chassi deve conter exatamente 17 caracteres"
- Quando RENAVAM ≠ 11 dígitos → "RENAVAM deve conter 11 dígitos numéricos"
- Quando fragmento < 3 caracteres → "Mínimo de 3 caracteres para busca por fragmento"

### Máscara de placa
- Quando digita letras (posições 1-3) → aceita A-Z, converte minúsculas para maiúsculas
- Quando digita na posição 4 → insere "-" automaticamente (formato antigo) ou aceita número/letra (Mercosul)
- Quando cola placa → máscara aplica automaticamente, remove caracteres inválidos

---

## Comportamento responsivo

| Precisa de variante `Layout=Mobile`? | **Não** — cada aba tem apenas 1 campo + botões. Auto-contido e simples |
|---|---|
| **Desktop** | Tabs horizontais. 1 campo full-width. Botões à direita |
| **Mobile** | Mesmo layout — tabs scroll se necessário, campo full-width, botões empilhados |
| **Tablet** | Segue Desktop |

> Componente simples: 4 abas, cada uma com 1 campo. Não precisa de variante Mobile dedicada — adapta com width 100% do container.

---

## Composição com outros componentes

| Componente | Relação | Composição no Figma |
|---|---|---|
| **BC-26 Tabs** | 4 modos de busca | **Instância** — Tabs Contained, 4 Tab Items |
| **BC-13 Input** | Campo de busca (1 por aba) | **Instância** — com máscara na aba Placa |
| **BC-05 Button** | Limpar + Pesquisar | **Instância** — Secondary MD + Primary MD |
| **BC-15 Icons** | Ícones nos botões | **Instância** — XS |
| **BC-03 Alert** | Feedback de erro | **Instância** — Danger |
| **BC-16 Loader** | Loading | **Instância** — Spinner SM |

---

## Variantes no Figma

| Variante | Property | Descrição |
|---|---|---|
| **Default** | `State=Default` | Aba Placa ativa, campo vazio com máscara |
| **Loading** | `State=Loading` | Spinner no botão, campo disabled |
| **Error** | `State=Error` | Alert Danger inline |

> 3 variantes. Sem variante Mobile — componente auto-contido (1 campo por aba).

---

## Tokens utilizados

| Token | Uso |
|---|---|
| `--color-surface` | Fundo do card |
| `--color-border` | Borda do card |
| `--color-primary` | Botão Pesquisar, aba ativa |
| `--color-danger` | Alert de erro |
| `--color-text-primary` | Labels |
| `--color-text-secondary` | Placeholder, máscara |
| `--color-border-focus` | Focus ring |
| `--font-body` | Textos |
| `--text-sm` / `--text-base` | Tamanhos |
| `--space-4` | Padding |
| `--space-3` | Gap |
| `--radius-lg` | Card |
| `--radius-sm` | Input |
| `--shadow-sm` | Sombra |

---

## Casos excepcionais / bordas

- **Placa formato antigo vs. Mercosul:** campo deve aceitar ambos. Detecção automática: se posição 5 é letra → Mercosul (AAA0A00), se número → antigo (AAA-0000). Ambos válidos
- **Fragmento com caracteres especiais:** sanitizar input — aceitar apenas alfanuméricos
- **Veículo com restrições judiciais:** responsabilidade do BFF/aplicação, não do componente. Componente apenas exibe resultado
- **Múltiplos resultados (fragmento):** busca por fragmento pode retornar vários. Exibir como lista paginada
- **Campo colado (paste):** máscara aplica automaticamente, strip caracteres inválidos

---

## O que está fora deste spec

- **Detalhes do veículo:** tela de detalhe é layout, não componente
- **Histórico de consultas:** funcionalidade de SC-16 Relatório de Consultas
- **Alertas de veículo roubado:** responsabilidade do BFF, não do componente de consulta
- **Layout da área de resultados:** definido no layout de tela (Sprint 8+)

---

## Critérios de aceite

- [ ] Tabs (BC-26 Contained) com 4 abas: Placa, Chassi, Renavam, Fragmento
- [ ] Campo com máscara de placa (antigo + Mercosul)
- [ ] Validação inline por tipo de busca (formato, tamanho mínimo)
- [ ] Instâncias de BC-13, BC-05, BC-15, BC-03, BC-16 (Regra 11)
- [ ] Tokens aplicados (Regra 8)
- [ ] WCAG (cor aten · vis aten) resolvidas
- [ ] Nielsen H-9 (CRIT) resolvida — mensagens de erro específicas por aba
- [ ] Tooltip em "Fragmento" explicando busca parcial
- [ ] Labels em português
- [ ] Revisado e aprovado por Giuliana
