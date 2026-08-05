---
component-id: SC-13
component-name: Steppers
type: SISP
status: approved
sprint: 5
approved-by: [Giuliana]
approved-date: [2026-07-22]
figma-node-id: [330:1650]
---

# Component Spec — Steppers

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → SC-13 (cor **crit** — status dos steps diferenciados exclusivamente por cor)
> - `docs/analyses/nielsen-analysis.md` → SC-13 (H-1 **crit** · H-3 **crit** · H-5 aten · H-9 **crit** · H-4 aten · H-6 aten · H-8 aten)
> - `docs/analyses/inventory.md` → SC-13
> - Nota: componente mais bem documentado do DS — serve de referência de qualidade

> **Nota sobre enum compartilhado:** Status de step (Completed/Required/Optional) provavelmente compartilha enum com SC-11 Resource Trees. Confirmar com Demilis antes de refatorar Angular.

---

## O que é

Stepper é o componente de navegação multi-etapas do SISP. Guia o usuário por um fluxo sequencial de steps com indicação visual de progresso, step atual, steps completados e steps pendentes. Na DV, steppers são usados para registro de B.O. (fluxo principal — 5+ steps), cadastro de envolvidos, e registro de medidas protetivas. Atualmente o componente mais bem documentado do DS existente, mas com 3 violações Nielsen críticas e status de steps diferenciados exclusivamente por cor.

---

## Audiência de uso

- **Policial na DV:** usa o stepper para preencher B.O. em etapas (Dados da Ocorrência → Envolvidos → Narrativa → Anexos → Revisão). Precisa saber onde está, quantos steps faltam, e se pode voltar para corrigir um dado anterior
- **Devs CiASC / terceiros:** configuram steps via array, controlam validação por step, e reagem a mudanças via EventEmitter. Padrão híbrido (Config + EventEmitter)
- **Demilis (mantenedor):** stepper é o componente mais usado na DV — qualquer mudança impacta o fluxo principal do produto
- **POs (Sommer/Holiwod):** fluxo de B.O. é o core da DV — stepper bem desenhado = experiência fluida para policiais

---

## Props / API

> **Padrão de API:** Híbrido — Config object + EventEmitter. Padrão documentado como-está para retrocompatibilidade.

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `steps` | `Step[]` | sim | — | Array de steps do fluxo |
| `currentStep` | `number` | não | `0` | Índice do step ativo (0-based) |
| `justified` | `boolean` | não | `false` | Steps ocupam largura total igualmente |
| `orientation` | `'horizontal' \| 'vertical'` | não | `'horizontal'` | Orientação do stepper |
| `useSeparator` | `boolean` | não | `true` | Exibe linha conectora entre steps |
| `linear` | `boolean` | não | `true` | Quando `true`, só permite avançar sequencialmente (não pula steps) |
| `finishButtonLabel` | `string` | não | `'Finalizar'` | Texto do botão do último step |
| `finishButtonAction` | `Function` | não | — | Callback ao clicar no botão de finalizar |
| `(stepChange)` | `EventEmitter<StepChangeEvent>` | não | — | Emitido quando step muda |

**Step:**
```typescript
interface Step {
  id: string;                        // Identificador único
  title: string;                     // Nome do step — em português
  subtitle?: string;                 // Descrição curta (ex: "Dados básicos da ocorrência")
  status: 'pending' | 'active' | 'completed' | 'error' | 'optional';
  icon?: string;                     // Classe Font Awesome (override do número)
  disabled?: boolean;                // Step desabilitado (não navegável)
  validationFn?: () => boolean;      // Função de validação antes de sair do step
}
```

**StepChangeEvent:**
```typescript
interface StepChangeEvent {
  previousStep: number;
  currentStep: number;
  direction: 'forward' | 'backward';
  step: Step;
}
```

**Convenção Angular:**
```html
<sisp-lib-stepper
  [sispLibStepperConfig]="config"
  (stepChange)="onStepChange($event)">

  <ng-template #step0>
    <!-- Conteúdo do step 1 -->
  </ng-template>

  <ng-template #step1>
    <!-- Conteúdo do step 2 -->
  </ng-template>

</sisp-lib-stepper>
```

**Exemplo de uso (B.O. na DV):**
```typescript
config: SispLibStepperConfig = {
  orientation: 'horizontal',
  linear: true,
  finishButtonLabel: 'Registrar B.O.',
  finishButtonAction: () => this.submitBO(),
  steps: [
    {
      id: 'dados',
      title: 'Dados da Ocorrência',
      subtitle: 'Tipo, local e data',
      status: 'active'
    },
    {
      id: 'envolvidos',
      title: 'Envolvidos',
      subtitle: 'Vítimas, autores, testemunhas',
      status: 'pending'
    },
    {
      id: 'narrativa',
      title: 'Narrativa',
      subtitle: 'Descrição dos fatos',
      status: 'pending'
    },
    {
      id: 'anexos',
      title: 'Anexos',
      subtitle: 'Fotos, documentos',
      status: 'optional'
    },
    {
      id: 'revisao',
      title: 'Revisão',
      subtitle: 'Conferir e registrar',
      status: 'pending'
    }
  ]
};

onStepChange(event: StepChangeEvent) {
  // Validar step anterior antes de avançar
  if (event.direction === 'forward') {
    this.autoSave(event.previousStep);
  }
}
```

---

## Anatomia do componente

### Horizontal (default)
```
  ①───────②───────③───────④───────⑤
 Dados    Envol-   Narra-   Anexos   Revisão
  da      vidos    tiva    (opcional)
Ocorr.

  [← Anterior]                    [Próximo →]
```

### Horizontal (com estados)
```
  ✓─────── ●───────③───────④───────⑤
 Dados    Envol-   Narra-   Anexos   Revisão
  ✅       vidos    tiva    (opc.)
          (ativo)

  [← Anterior]                    [Próximo →]
```

### Vertical
```
  ✓  Dados da Ocorrência
  │   Tipo, local e data — Completado
  │
  ●  Envolvidos
  │   Vítimas, autores, testemunhas
  │   [Conteúdo do step ativo aqui]
  │
  ③  Narrativa
  │   Descrição dos fatos
  │
  ④  Anexos (opcional)
  │   Fotos, documentos
  │
  ⑤  Revisão
      Conferir e registrar

  [← Anterior]       [Próximo →]
```

- **Step indicator:** círculo 32px com número, ícone de check (completed), ícone de erro (error), ou ponto (optional)
- **Conector:** linha horizontal ou vertical entre steps (1px, --color-border para pendente, --color-success para completed)
- **Label:** título do step (abaixo em horizontal, ao lado em vertical)
- **Subtitle:** descrição curta (Body/XS muted)
- **Navegação:** botões Anterior e Próximo (instâncias BC-05 Button)
- **Progresso textual:** indicador "Step 2 de 5" (visível por screen reader, opcional visualmente)

> **Regra 11 — Composição atômica:** botões de navegação são instâncias de BC-05 Button. Ícones de status são instâncias de BC-15 Icons.

---

## Estados e variantes

### Status dos steps

| Status | Indicador visual | Ícone | Cor do círculo | Cor do texto | Conector anterior |
|---|---|---|---|---|---|
| **Completed** | Círculo preenchido + check | fa-check (✓) | bg `--color-success`, texto branco | `--color-text-primary`, bold | `--color-success` (verde, preenchido) |
| **Active** | Círculo com borda + número | Número do step | borda `--color-primary`, texto `--color-primary` | `--color-text-primary`, bold | `--color-border` (neutro) |
| **Pending** | Círculo com borda cinza + número | Número do step | borda `--color-border-strong`, texto `--color-text-muted` | `--color-text-muted` | `--color-border` (neutro) |
| **Error** | Círculo preenchido + exclamação | fa-exclamation (!) | bg `--color-danger`, texto branco | `--color-danger`, bold | `--color-border` (neutro) |
| **Optional** | Círculo com borda tracejada + traço | fa-minus (—) | borda `--color-border` tracejada, texto `--color-text-muted` | `--color-text-muted`, itálico | `--color-border` (neutro) |
| **Disabled** | Círculo opaco | — | `opacity: 0.4` | `opacity: 0.4` | `--color-border` |

### Orientações

| Orientação | Layout | Uso típico |
|---|---|---|
| **Horizontal** | Steps em linha, labels abaixo, conteúdo abaixo | ≤ 6 steps, labels curtos |
| **Horizontal Justified** | Steps espaçados igualmente na largura total | Quando todos os steps têm tamanho visual similar |
| **Vertical** | Steps empilhados, labels ao lado, conteúdo inline | > 6 steps ou labels longos |

### Botões de navegação

| Posição | Botão | Instância BC-05 | Condição |
|---|---|---|---|
| Primeiro step | — | — | Botão "Anterior" oculto |
| Steps intermediários | Anterior + Próximo | Secondary SM + Primary SM | Sempre visíveis |
| Último step | Anterior + Finalizar | Secondary SM + Primary SM | `finishButtonLabel` como texto |
| Step com erro | Anterior + Corrigir | Secondary SM + Danger SM | Botão "Próximo" muda para "Corrigir" |

### Verificação de contraste (WCAG AA)

| Elemento | Texto | Fundo | Ratio | Resultado |
|---|---|---|---|---|
| Step completed (check) | #FFFFFF | #166534 | ≥ 7.5:1 | ✅ AAA |
| Step active (número) | #C4000B | #FFFFFF | ≥ 5.2:1 | ✅ AA |
| Step pending (número) | #9CA3AF | #FFFFFF | ≥ 2.7:1 | ⚠ Gráfico (3:1 para non-text) ✅ |
| Step error (!) | #FFFFFF | #991B1B | ≥ 5.7:1 | ✅ AAA |
| Label active | #08060F | #FFFFFF | ≥ 18.1:1 | ✅ AAA |
| Label pending | #9CA3AF | #FFFFFF | ≥ 2.7:1 | ⚠ Gráfico ✅ (text companion is muted) |
| Subtitle | #6B7280 | #FFFFFF | ≥ 4.6:1 | ✅ AA |
| Conector completed | #166534 | #FFFFFF | ≥ 7.5:1 | ✅ AAA |

### Dimensões

| Propriedade | Valor | Token |
|---|---|---|
| Indicador (círculo) | 32px × 32px | `--space-8` |
| Indicador border | 2px solid | — |
| Indicador border-radius | 9999px | `--radius-full` |
| Indicador font size | 14px | `--text-sm` |
| Indicador font weight | 700 | `--font-bold` |
| Ícone check/error | 14px | `--text-sm` |
| Conector height (horizontal) | 2px | — |
| Conector width (vertical) | 2px | — |
| Conector gap (entre círculos) | 0 (toca os círculos) | — |
| Label font size | 14px | `--text-sm` |
| Label font weight (active/completed) | 600 | `--font-semibold` |
| Label font weight (pending) | 400 | `--font-regular` |
| Subtitle font size | 12px | `--text-xs` |
| Gap indicador → label (horizontal) | 8px | `--space-2` |
| Gap indicador → label (vertical) | 12px | `--space-3` |
| Gap entre steps (horizontal) | 0 (conector preenche) | — |
| Gap entre steps (vertical) | 24px | `--space-6` |
| Gap conteúdo → botões | 24px | `--space-6` |
| Gap entre botões | 12px | `--space-3` |
| Content area padding | 24px | `--space-6` |
| Min width por step (horizontal) | 80px | — |
| Max label width (horizontal) | 120px | — |

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução neste spec |
|---|---|---|
| Uso de cor (cor **crit**) | Status dos steps (Completed/Required/Optional) diferenciados exclusivamente por cor — verde, vermelho, âmbar | **4 canais por status:** (1) Cor do círculo (verde/vermelho/cinza/tracejado), (2) Ícone dentro do círculo (✓ check / ! exclamação / número / — traço), (3) Estilo do conector (preenchido verde para completed, neutro para pendente), (4) Peso do texto (bold para active/completed, regular para pending, itálico para optional). Nunca depende apenas de cor |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução neste spec |
|---|---|---|
| H-1 Visibilidade (CRIT) | Não fica claro visualmente em qual step o usuário está e quantos faltam | **Step ativo claramente diferenciado:** círculo com borda vermelha (--color-primary), label bold, conteúdo visível. **Progresso visível:** steps completed com check verde + conector verde. Steps pendentes com número cinza. Counter textual "Step 2 de 5" para screen reader. Subtitle descreve o que esperar |
| H-3 Controle (CRIT) | Não está documentado se o usuário pode voltar a um step anterior | **Navegação bidirecional documentada:** botão "Anterior" sempre visível (exceto step 1). Em modo `linear: false`, steps completed são clicáveis — usuário pode pular para qualquer step já visitado. Em modo `linear: true`, apenas sequencial com "Anterior" |
| H-9 Recuperação (CRIT) | Sem mensagem clara se o step falhar | **Status error com ícone + cor + mensagem:** step que falhou validação mostra círculo vermelho com "!" + label em vermelho. Botão muda para "Corrigir". Mensagem de erro específica via validação do step (ex: "Preencha todos os campos obrigatórios"). Usuário permanece no step com erro — não avança |
| H-4 Consistência (aten) | Enum de status não documentado | Enum padronizado: `'pending' \| 'active' \| 'completed' \| 'error' \| 'optional'`. Se compartilhado com SC-11 Resource Trees, alinhar nomes e valores. Documentar que Optional = step que pode ser pulado |
| H-5 Prevenção (aten) | Sem validação por step antes de avançar | Prop `validationFn` opcional em cada step. Se definida, executada antes de avançar. Se retorna `false`, step entra em status error e não avança. Mensagem de erro exibida abaixo do conteúdo do step |
| H-6 Reconhecimento (aten) | Status de step precisa de memorização | Ícones semânticos: ✓ (completed), ! (error), — (optional), número (pending/active). Subtitle descreve o que cada step contém. Progresso visual (conectores verdes) mostra quanto já foi feito |
| H-8 Estética (aten) | Design genérico | Círculos com border-radius full, conectores alinhados, tipografia por tokens, espaçamento por tokens. Transição suave entre steps (--transition-normal). Layout limpo com hierarquia visual clara |

---

## Regras de acessibilidade

- [ ] Container com `role="navigation"` e `aria-label="Progresso do formulário"` (ou contextual: "Registro de B.O.")
- [ ] Lista de steps com `<ol>` (ordered list) e `<li>` por step
- [ ] Step ativo com `aria-current="step"`
- [ ] Step completed com `aria-label` incluindo "Completado" (ex: "Step 1: Dados da Ocorrência — Completado")
- [ ] Step error com `aria-label` incluindo "Erro" e mensagem
- [ ] Step optional com `aria-label` incluindo "Opcional"
- [ ] Step disabled com `aria-disabled="true"` e `tabindex="-1"`
- [ ] **Progresso textual** para screen reader: `aria-label="Step 2 de 5: Envolvidos"` no container ou elemento dedicado
- [ ] **Navegação por teclado:**
  - `Tab` foca step ativo
  - `←` / `→` (horizontal) ou `↑` / `↓` (vertical) navega entre steps (quando `linear: false`)
  - `Enter` / `Space` seleciona step (quando `linear: false` e step completed)
  - Botões Anterior/Próximo navegáveis por Tab normal
- [ ] Focus ring visível: `2px solid var(--color-border-focus)`
- [ ] Contraste verificado — mínimo 3:1 para gráficos (círculos), 4.5:1 para texto
- [ ] Conector muda de cor ao completar step — reforço visual de progresso (não apenas cor — acompanhado por check)
- [ ] Labels em português
- [ ] Animação de transição entre steps respeita `prefers-reduced-motion`

---

## Comportamentos esperados

### Navegação entre steps
- Quando clica em "Próximo" → se `validationFn` existe e retorna `true` (ou não existe) → step atual muda para `completed`, próximo step muda para `active`. Emite `(stepChange)` com `direction: 'forward'`. Conteúdo do step troca com transição
- Quando clica em "Próximo" e validação falha → step atual muda para `error`. Mensagem de erro exibida. Botão "Próximo" muda para "Corrigir". Usuário permanece no step
- Quando clica em "Anterior" → step atual volta para `pending` (ou mantém `completed` se já estava). Step anterior muda para `active`. Emite `(stepChange)` com `direction: 'backward'`
- Quando `linear: false` e clica em step completed → navega diretamente. Steps intermediários mantêm seus status
- Quando `linear: true` e tenta clicar em step pendente → ignorado (step não clicável)

### Finalização
- Quando clica em `finishButtonLabel` no último step → validação do step + `finishButtonAction()` executada. Se sucesso, todos os steps mostram `completed`
- Quando finalização falha → último step entra em `error`. Mensagem de erro exibida

### Steps opcionais
- Quando step tem `status: 'optional'` → exibido com borda tracejada e label "Opcional" / itálico. Botão "Próximo" disponível para pular. Conector antes do optional não fica verde ao pular

### Layout
- Quando viewport ≥ 1024px → horizontal (default). Labels abaixo dos indicadores
- Quando viewport < 768px → vertical automático. Labels ao lado dos indicadores. Conteúdo inline
- Quando `orientation: 'vertical'` forçado → vertical independente do viewport

### Conteúdo do step
- Quando step muda → conteúdo anterior desmonta (ou fica hidden), conteúdo novo monta. Foco move para primeiro elemento focável do conteúdo
- Quando step tem subtitle → exibido abaixo do título em Body/XS muted

---

## Composição com outros componentes

> **Regra 11 — Composição atômica:** todo elemento que já exista como componente no DS deve ser instância.

| Componente | Relação | Composição no Figma |
|---|---|---|
| **BC-05 Button** | Botões Anterior/Próximo/Finalizar/Corrigir | **Instância direta** — Secondary SM (Anterior), Primary SM (Próximo/Finalizar), Danger SM (Corrigir quando error) |
| **BC-15 Icons** | Ícones de status nos indicadores (check, error, minus) | **Font Awesome** — fa-check (completed), fa-exclamation (error), fa-minus (optional) |
| **BC-03 Alert** | Mensagem de erro de validação do step | **Instância direta** — Alert Danger (quando validação falha) |

> **Regra 12 — auditoria:** "este elemento já existe?" Botões de navegação existem (BC-05). Ícones de status existem (BC-15). A barra de progresso visual do stepper é custom (não é BC-16 Loader — é navegação, não loading). Indicadores circulares são custom (não Badge — formato e função diferentes).

> **Nota — relação com SC-11 Resource Trees:** SC-11 usa color coding com enum provavelmente compartilhado (Completed/Required/Optional). Alinhar nomes de status entre SC-13 Steppers e SC-11 Resource Trees. Confirmar com Demilis se é enum compartilhado ou independente.

---

## Integração com BFF

> SC-13 é híbrido — recebe configuração via Config mas emite eventos via EventEmitter. O componente não faz chamadas ao BFF diretamente — a validação e submissão são responsabilidade da aplicação host via `validationFn` e `finishButtonAction`.

| Fluxo | Responsabilidade | Componente |
|---|---|---|
| Configurar steps | App host → passa `steps[]` via config | SC-13 renderiza |
| Validar step | App host → `validationFn()` retorna boolean | SC-13 mostra error se false |
| Submeter no final | App host → `finishButtonAction()` | SC-13 marca todos completed se sucesso |
| Auto-save entre steps | App host → `(stepChange)` handler | SC-13 emite evento, app decide |

---

## Mapeamento de retrocompatibilidade

| Prop atual | Mapeamento novo | Nota |
|---|---|---|
| `steps: Step[]` | `steps: Step[]` | Extendido com `id`, `subtitle`, `icon`, `disabled`, `validationFn` |
| `currentStep: number` | `currentStep: number` | Mantido (0-based) |
| `justified?: boolean` | `justified?: boolean` | Mantido |
| `useSeparator?: boolean` | `useSeparator?: boolean` | Mantido |
| `finishButtonLabel?: string` | `finishButtonLabel?: string` | Mantido |
| `finishButtonAction?: Function` | `finishButtonAction?: Function` | Mantido |
| `(stepChange)` | `(stepChange): StepChangeEvent` | Extendido com `direction` e `step` |
| — | `orientation` (novo) | Horizontal default, vertical para fluxos longos |
| — | `linear` (novo) | Controla se pode pular steps |
| Status: Completed/Required/Optional | `'completed' \| 'active' \| 'pending' \| 'error' \| 'optional'` | Expandido — Required → mapped via `validationFn`, Active + Pending adicionados |

---

## Variantes no Figma

| Variante | Properties | Descrição |
|---|---|---|
| **Horizontal — início** | `Orientation=Horizontal, ActiveStep=1` | Step 1 ativo, demais pending. Sem botão Anterior |
| **Horizontal — meio** | `Orientation=Horizontal, ActiveStep=3` | Steps 1-2 completed, 3 ativo, 4-5 pending. Anterior + Próximo |
| **Horizontal — erro** | `Orientation=Horizontal, ActiveStep=2-Error` | Step 1 completed, step 2 com erro, 3-5 pending. Alert de validação visível |
| **Horizontal — final** | `Orientation=Horizontal, ActiveStep=5` | Steps 1-4 completed, 5 ativo. Anterior + Finalizar |
| **Vertical** | `Orientation=Vertical, ActiveStep=2` | Layout vertical, step 2 ativo com conteúdo inline |
| **Completo** | `Orientation=Horizontal, ActiveStep=Done` | Todos os steps completed (pós-finalização) |

---

## Comportamento responsivo

> Regra 13 — Responsividade obrigatória. Breakpoints: Mobile (<768px), Tablet (768–1023px), Desktop (≥1024px).

| Precisa de variante `Layout=Mobile`? | **Sim** — steps horizontais não cabem em viewport mobile |
|---|---|
| **Desktop** | Horizontal (720px): step indicators em linha, botões Anterior/Próximo abaixo. Vertical (360px): steps empilhados com descrições |
| **Mobile** | Vertical (343px): mesma estrutura do vertical Desktop mas mais estreito. Orientação horizontal não é usada em mobile |
| **Tablet** | Segue Desktop — largura suficiente para horizontal |

**O que muda entre Desktop e Mobile:**
- Orientação: Horizontal → Vertical (obrigatório)
- Largura: 720px horizontal / 360px vertical → 343px vertical
- Step indicators: em linha → empilhados verticalmente
- Composição atômica: mantida (BC-05 Buttons + BC-03 Alert são instâncias)

**Variantes no Figma:** 7 variantes (6 Desktop + 1 Mobile)
- 5× `Orientation=Horizontal, ActiveStep=X, Layout=Desktop`
- 1× `Orientation=Vertical, ActiveStep=2, Layout=Desktop`
- 1× `Orientation=Vertical, ActiveStep=2, Layout=Mobile` (343px)

---

## Casos excepcionais / bordas

- **1 step:** stepper não renderiza indicadores — apenas conteúdo. Recomendação: usar formulário direto, não stepper
- **2 steps:** renderiza normalmente — "Anterior" e "Próximo" com indicadores
- **10+ steps (horizontal):** labels truncam, indicadores ficam apertados. Recomendação: usar `orientation: 'vertical'` para > 6 steps
- **Step com título longo (> 20 chars, horizontal):** trunca com ellipsis. Tooltip com título completo. Subtitle ajuda a contextualizar
- **Validação assíncrona:** `validationFn` pode retornar Promise<boolean>. Botão "Próximo" mostra estado loading (Spinner SM) durante validação
- **Browser back/forward:** não afeta stepper diretamente — é estado interno. Se app usar query params para persistir step, sincronizar via `currentStep`
- **Mobile (< 768px):** horizontal muda para vertical automaticamente (ou manter horizontal com scroll horizontal e indicadores compactos)
- **Step optional pulado:** conector antes do optional não fica verde. Step seguinte ao optional pode ser active/completed independente
- **Todos os steps completed + voltar para editar:** ao clicar em step completed (modo `linear: false`), step volta para `active`, steps seguintes mantêm `completed`
- **Impressão:** stepper renderiza todos os steps com status visual. Conteúdo do step ativo visível

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-success` | Indicador completed (fundo), conector completed |
| `--color-success-bg` | — (check branco sobre success, não precisa bg) |
| `--color-primary` | Indicador active (borda + texto) |
| `--color-danger` | Indicador error (fundo) |
| `--color-text-primary` | Label active/completed |
| `--color-text-muted` | Label pending, indicador pending, subtitle |
| `--color-border` | Conector pending, indicador pending (borda) |
| `--color-border-strong` | Indicador pending (borda mais forte) |
| `--color-border-focus` | Focus ring |
| `--color-surface` | Fundo geral |
| `--font-body` | Família tipográfica |
| `--text-sm` | Label (14px), número no indicador |
| `--text-xs` | Subtitle (12px) |
| `--font-semibold` | Label active/completed |
| `--font-regular` | Label pending |
| `--font-bold` | Número no indicador |
| `--radius-full` | Border-radius do indicador (círculo) |
| `--space-2` | Gap indicador→label (horizontal) |
| `--space-3` | Gap indicador→label (vertical), gap entre botões |
| `--space-6` | Gap entre steps (vertical), gap conteúdo→botões, content padding |
| `--space-8` | Tamanho do indicador (32px) |
| `--transition-normal` | Transição entre steps |

---

## O que está fora deste spec

- **Stepper dentro de modal:** composição com BC-19 Modal — responsabilidade do layout da app, não do componente stepper
- **Stepper com ramificação (conditional steps):** fluxo condicional complexo. Se necessário, app controla visibility dos steps via array dinâmico
- **Stepper com salvar rascunho por step:** responsabilidade da app via `(stepChange)`. Componente não persiste dados
- **Stepper com progresso percentual global (ex: "40% completo"):** pode ser adicionado como extensão. Spec atual cobre indicadores por step
- **Stepper com ícones customizados por step (SVG):** manter Font Awesome. Prop `icon` aceita classe FA
- **Integração direta com SC-11 Resource Trees:** enum pode ser compartilhado, mas componentes são independentes
- **Animações de transição entre conteúdo de steps:** fade/slide pode ser adicionado. Não especificado neste sprint

---

## Critérios de aceite

- [ ] 6 variantes no Figma: Horizontal (início/meio/erro/final), Vertical, Completo
- [ ] 6 status visuais com 4 canais cada (cor + ícone + conector + peso do texto) — resolve WCAG cor **crit**
- [ ] Indicadores circulares 32px com número/ícone por status
- [ ] Conectores entre steps mudam de cor (neutro → verde) conforme progresso
- [ ] Botões como instâncias de BC-05 Button SM (Regra 11): Secondary (Anterior), Primary (Próximo/Finalizar), Danger (Corrigir)
- [ ] Ícones como Font Awesome via BC-15 Icons (Regra 11): fa-check, fa-exclamation, fa-minus
- [ ] Alert de validação como instância de BC-03 Alert Danger (Regra 11)
- [ ] Orientações horizontal e vertical
- [ ] Step optional com borda tracejada e label itálico
- [ ] Step error com ícone ! + cor danger + mensagem de erro
- [ ] Navegação bidirecional documentada (Anterior/Próximo/click em completed)
- [ ] Prop `linear` controla se pode pular steps
- [ ] Prop `validationFn` documentada para validação por step
- [ ] Contraste verificado — mínimo 3:1 para gráficos, 4.5:1 para texto
- [ ] ARIA documentado: `role="navigation"`, `<ol>`, `aria-current="step"`, progresso textual
- [ ] Navegação por teclado: ←→ (horizontal), ↑↓ (vertical), Enter/Space, Tab
- [ ] EventEmitter `(stepChange)` com direction e step
- [ ] Violações WCAG (cor **crit**) resolvidas — 4 canais, nunca apenas cor
- [ ] Violações Nielsen (H-1 **crit** · H-3 **crit** · H-9 **crit** · H-5 aten · H-4 aten · H-6 aten · H-8 aten) resolvidas
- [ ] Mapeamento de retrocompatibilidade documentado
- [ ] Enum compartilhado com SC-11 documentado (a confirmar com Demilis)
- [ ] Labels em português
- [ ] Revisado e aprovado por Giuliana
