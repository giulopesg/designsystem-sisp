---
component-id: BC-27
component-name: Toasts
type: Base
status: approved
sprint: 3
approved-by: [Giuliana]
approved-date: [2026-07-13]
figma-node-id: [124:2862]
---

# Component Spec — Toasts

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → BC-27 (cor **crit** · wcag aten · vis aten)
> - `docs/analyses/nielsen-analysis.md` → BC-27 (H-1 **crit** · H-3 **crit** · H-9 aten)
> - `docs/analyses/inventory.md` → BC-27

---

## O que é

Toast é o componente de notificação temporária e flutuante do DS SISP. Comunica feedback de ações do sistema — confirmações, erros, avisos — sem bloquear a interface. Diferente do Alert (BC-03), o toast **flutua** sobre o conteúdo e **desaparece automaticamente** (exceto erros). Validado em produção: SC-10 dispara toasts Danger em erro de BFF. Atualmente sem controle de tempo e sem diferenciação clara de urgência.

---

## Audiência de uso

- **Policial na DV:** recebe feedback de ações — "BO salvo com sucesso", "Erro ao consultar CPF", "Sessão expirando"
- **Devs CiASC / terceiros:** disparam toasts programaticamente via service Angular após operações CRUD
- **Cidadão (DV externa):** vê toasts de confirmação ao registrar ocorrência

---

## Toast vs. Alert — quando usar cada

| Critério | Toast (BC-27) | Alert (BC-03) |
|---|---|---|
| Posição | Flutuante, canto superior direito | Inline, dentro do fluxo da página |
| Permanência | Temporário — auto-dismiss | Persistente — até condição mudar |
| Contexto | Feedback de ação do sistema | Informação contextual do conteúdo |
| Bloqueio | Não bloqueia interação | Não bloqueia, mas ocupa espaço |
| Exemplo | "Dados salvos" | "Campos obrigatórios pendentes" |

**Regra:** se a mensagem é resultado de uma ação do usuário e não requer leitura prolongada → Toast. Se a mensagem é contextual e deve permanecer visível → Alert.

---

## Tipos semânticos — 4 níveis

> Mesmos 4 tipos do sistema de status do DS (Alerts, Badges, Cards). `SispLibStyleType` mantido com retrocompatibilidade.

| Tipo | Ícone padrão | Quando usar | Auto-dismiss |
|---|---|---|---|
| **Success** | `fa-solid fa-circle-check` | Ação concluída com sucesso | Sim — 5s |
| **Warning** | `fa-solid fa-triangle-exclamation` | Atenção necessária, ação parcial | Sim — 8s |
| **Danger** | `fa-solid fa-circle-xmark` | Erro, falha de operação | **Não** — manual |
| **Info** | `fa-solid fa-circle-info` | Informação do sistema, dica | Sim — 5s |

---

## Props / API

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `type` | `'success' \| 'warning' \| 'danger' \| 'info'` | sim | — | Tipo semântico — define cor, ícone, duração e role ARIA |
| `message` | `string` | sim | — | Texto principal do toast |
| `description` | `string` | não | — | Texto secundário com detalhes |
| `duration` | `number` | não | (por tipo) | Duração em ms antes do auto-dismiss. `0` = sem auto-dismiss |
| `dismissible` | `boolean` | não | `true` | Exibe botão de fechar (×) |
| `onDismiss` | `Function` | não | — | Callback ao fechar (manual ou auto) |
| `action` | `{ label: string, onClick: Function }` | não | — | Botão de ação inline (ex: "Desfazer") |
| `showProgress` | `boolean` | não | `true` | Exibe barra de progresso do tempo restante |
| `position` | `'top-right' \| 'top-left' \| 'bottom-right' \| 'bottom-left'` | não | `'top-right'` | Posição na viewport |

**Convenção Angular:**
```html
<sisp-lib-toast [sispLibToastConfig]="config"></sisp-lib-toast>
```

**Exemplo de uso:**
```typescript
// Via service (padrão recomendado)
this.toastService.show({
  type: 'success',
  message: 'BO registrado com sucesso',
  description: 'Protocolo: 2026.001234',
  duration: 5000
});

// Toast com ação de desfazer
this.toastService.show({
  type: 'warning',
  message: 'Pessoa removida',
  action: { label: 'Desfazer', onClick: () => this.undoRemove() }
});
```

---

## Anatomia do toast

```
┌──────────────────────────────────────┐
│  [icon]  Message                [×]  │
│          Description                 │
│          [Desfazer]                  │
│  ▓▓▓▓▓▓▓▓▓▓░░░░░░░░░░░░░░░         │
└──────────────────────────────────────┘
```

- **Ícone:** sempre presente, alinhado ao topo da primeira linha
- **Message:** texto principal, semibold
- **Description:** texto secundário, regular (opcional)
- **Action:** link/botão terciário inline (opcional) — ex: "Desfazer"
- **Close button (×):** sempre presente por padrão
- **Progress bar:** barra na parte inferior indicando tempo restante — desaparece quando `duration = 0`
- **Fundo:** escuro (`--color-dark-surface`) — destaca sobre qualquer fundo de página
- **Sombra:** `--shadow-lg` — reforça flutuação

---

## Estados e variantes

### Visual por tipo (fundo escuro)

| Tipo | Fundo | Ícone / Accent | Texto | Tokens |
|---|---|---|---|---|
| Success | Escuro | Verde | Branco | `bg: --color-dark-surface` · `icon: --color-success-bg` · `text: --color-dark-text` |
| Warning | Escuro | Amarelo | Branco | `bg: --color-dark-surface` · `icon: --color-warning-bg` · `text: --color-dark-text` |
| Danger | Escuro | Vermelho | Branco | `bg: --color-dark-surface` · `icon: --color-danger-bg` · `text: --color-dark-text` |
| Info | Escuro | Azul | Branco | `bg: --color-dark-surface` · `icon: --color-info-bg` · `text: --color-dark-text` |

> **Decisão de design:** fundo escuro único para todos os tipos. O tipo é diferenciado pelo **ícone colorido** + **barra de progresso colorida** + **texto da mensagem**. Fundo escuro garante contraste máximo e destaque visual sobre qualquer página.

### Verificação de contraste

| Elemento | Cor | Fundo | Ratio | Resultado |
|---|---|---|---|---|
| Texto branco | #FFFFFF | #192D22 | ~12.1:1 | ✅ AAA |
| Ícone Success | #DCFCE7 | #192D22 | ~8.5:1 | ✅ AAA |
| Ícone Warning | #FEF3C7 | #192D22 | ~10.2:1 | ✅ AAA |
| Ícone Danger | #FEE2E2 | #192D22 | ~9.8:1 | ✅ AAA |
| Ícone Info | #DBEAFE | #192D22 | ~9.4:1 | ✅ AAA |

### Progress bar

| Propriedade | Valor | Token |
|---|---|---|
| Altura | 3px | — |
| Cor | Mesma do ícone (success-bg, warning-bg, etc.) | `--color-[tipo]-bg` |
| Fundo (trilha) | Branco 20% opacidade | `rgba(255,255,255,0.2)` |
| Posição | Parte inferior do toast, full-width | — |
| Animação | Width 100% → 0% linear durante `duration` | — |
| Border radius | Inferior segue o toast (0 0 radius-md radius-md) | — |

### Close button (×)

| Propriedade | Token |
|---|---|
| Cor | `--color-dark-text` (#FFFFFF) |
| Hover | Opacidade 0.7 |
| Focus | Ring 2px branco |
| Tamanho | 20×20px, target area 24×24px |

### Dimensões

| Propriedade | Valor | Token |
|---|---|---|
| Largura | 360px (fixa) | — |
| Padding | 16px | `--space-4` |
| Gap ícone → texto | 12px | `--space-3` |
| Gap message → description | 4px | `--space-1` |
| Border radius | 6px | `--radius-md` |
| Sombra | Elevada | `--shadow-lg` |
| Font size (message) | 14px | `--text-sm` |
| Font weight (message) | 600 | `--font-semibold` |
| Font size (description) | 14px | `--text-sm` |
| Font weight (description) | 400 | `--font-regular` |
| Font size (action) | 14px | `--text-sm` |
| Font weight (action) | 600 | `--font-semibold` |

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução neste spec |
|---|---|---|
| Contraste (wcag aten) | Não verificado | Fundo escuro (#192D22) com texto branco = 12.1:1 ✅ AAA. Ícones coloridos claros sobre fundo escuro ≥ 8.5:1 ✅ AAA |
| Uso de cor (cor **crit**) | Danger/Success diferenciados só por cor sem ícone consistente | Cada tipo tem: **ícone semântico** colorido + **barra de progresso** colorida + **texto descritivo**. Fundo escuro único elimina diferenciação por cor de fundo |
| Visual (vis aten) | Estados não documentados | Toast com entrada/saída animada. Progress bar mostra tempo restante visualmente |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução neste spec |
|---|---|---|
| H-1 Visibilidade (CRIT) | Sem distinção clara de urgência entre Danger e Warning | Ícone semântico distinto por tipo (✕ vs ⚠). Danger **não tem auto-dismiss** — permanece até interação. Warning tem 8s (mais que Success/Info). Progress bar diferencia visualmente |
| H-3 Controle do usuário (CRIT) | Toast desaparece antes do usuário ler — sem controle de tempo | **Hover pausa o timer**. Danger não tem auto-dismiss. Duração configurável via `duration`. Botão × sempre visível. Progress bar mostra tempo restante |
| H-9 Recuperação de erros (aten) | Sem ação de "desfazer" em toasts de ação irreversível | Prop `action` com label + callback — ex: `{ label: 'Desfazer', onClick: fn }`. Toast com action tem duração estendida automaticamente (+3s) |

---

## Regras de acessibilidade

- [ ] `role="alert"` para tipos Danger e Warning (interrompe screen reader)
- [ ] `role="status"` para tipos Success e Info (anuncia na próxima pausa)
- [ ] `aria-live="assertive"` implícito em `role="alert"`
- [ ] `aria-live="polite"` implícito em `role="status"`
- [ ] Ícone com `aria-hidden="true"` (decorativo)
- [ ] Botão fechar com `aria-label="Fechar notificação"`
- [ ] **Hover pausa o timer** — usuário que precisa de mais tempo não perde a mensagem
- [ ] **Focus no toast pausa o timer** — screen reader não perde conteúdo
- [ ] Danger **não auto-dismiss** — erros devem ser lidos e resolvidos
- [ ] Action button navegável por teclado (Tab → Enter)
- [ ] Progress bar com `aria-hidden="true"` (visual only — tempo já comunicado pelo duration)
- [ ] Máximo 3 toasts visíveis — evita sobrecarga cognitiva

---

## Comportamentos esperados

- Quando toast é disparado → entra com animação `slideIn` da direita (translateX 100% → 0) + `opacity 0→1`, duração `--transition-normal` (200ms)
- Quando `duration` expira → sai com animação `fadeOut` + `slideOut` (translateX 0 → 100%), `--transition-normal`
- Quando usuário faz hover no toast → timer **pausa**. Ao sair do hover, timer retoma
- Quando usuário clica × → dispara `onDismiss`, toast sai com animação
- Quando `type = 'danger'` → `duration = 0` (não auto-dismiss) — permanece até × ou `onDismiss` programático
- Quando `action` definido → botão de ação aparece abaixo do description. Duração estendida automaticamente (+3s)
- Quando múltiplos toasts → stack vertical, novos aparecem abaixo dos existentes. Gap de `--space-3` (12px)
- Quando 4º toast é disparado → o toast mais antigo é removido (max 3 visíveis)
- Quando `showProgress = false` → progress bar não renderiza (mas timer continua)

---

## Stacking e posicionamento

| Propriedade | Valor | Token |
|---|---|---|
| Posição padrão | Top-right, 24px da borda | `--space-6` |
| Z-index | Acima de tudo | `--z-toast` (500) |
| Gap entre toasts | 12px | `--space-3` |
| Máximo visível | 3 | — |
| Overflow | 4º toast remove o mais antigo | — |
| Ordem | Mais recente embaixo | — |

---

## Composição com outros componentes

| Componente | Relação |
|---|---|
| BC-03 Alerts | Alert é inline e persistente. Toast é flutuante e temporário. Nunca usar toast para informação que o usuário precisa consultar depois |
| BC-05 Buttons | Ação no toast usa estilo de link (terciário), não botão completo |
| SC-10 Login | Dispara toasts Danger em erro de BFF (validado em produção) |
| SC-02/03/04 Consultas | Toast Success após consulta bem-sucedida |

---

## Mapeamento de retrocompatibilidade

| SispLibStyleType antigo | Mapeamento novo | Nota |
|---|---|---|
| `success` | **Success** | Direto |
| `warning` | **Warning** | Direto |
| `danger` | **Danger** | Direto — duration = 0 |
| `info` | **Info** | Direto |
| `primary` | **Info** | Sem uso semântico em toasts |
| `secondary` | **Info** | Sem uso real |
| `dark` | **Info** | Fundo escuro agora é padrão de todos |
| `light` | **Info** | Removido |

---

## Casos excepcionais / bordas

- **Message muito longo:** texto quebra em múltiplas linhas. Largura fixa (360px) garante previsibilidade
- **Toast em mobile (< 640px):** full-width, posição bottom-center (mais acessível ao polegar), stack para cima
- **Toast com action + auto-dismiss:** duração estendida +3s automaticamente para dar tempo de ler e agir
- **Muitos toasts simultâneos (> 3):** o mais antigo é removido. Em produção, considerar debounce de toasts iguais
- **Toast durante modal aberto:** toast aparece acima do modal (`z-toast > z-modal`)

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-dark-surface` | Fundo do toast (#192D22) |
| `--color-dark-text` | Texto, botão fechar (#FFFFFF) |
| `--color-success-bg / warning-bg / danger-bg / info-bg` | Ícone e progress bar por tipo |
| `--color-border-focus` | Ring de foco no botão fechar |
| `--shadow-lg` | Sombra de flutuação |
| `--radius-md` | Border radius (6px) |
| `--font-body` | Família tipográfica |
| `--text-sm` | Font size (14px) |
| `--font-semibold / --font-regular` | Pesos |
| `--space-1 / --space-3 / --space-4 / --space-6` | Gaps e paddings |
| `--transition-normal` | Animação entrada/saída (200ms) |
| `--z-toast` | Z-index (500) |

---

## O que está fora deste spec

- **Toast com rich content (imagem, avatar):** não identificado no SISP. Não adicionar
- **Toast center (confirmação modal-like):** usar BC-09 Confirmation Modal, não toast
- **Toast com múltiplas ações:** máximo 1 ação. Se precisa de mais, usar Alert (BC-03) ou Modal
- **Toast sonoro (beep/notification sound):** decisão de produto, não do componente
- **Toast push notification (service worker):** fora do escopo do DS — responsabilidade do produto

---

## Critérios de aceite

- [ ] 4 tipos semânticos (Success, Warning, Danger, Info) no Figma com fundo escuro
- [ ] Ícone semântico colorido por tipo
- [ ] Progress bar na parte inferior com cor do tipo
- [ ] Variante com action ("Desfazer")
- [ ] Variante com description
- [ ] Botão × sempre presente
- [ ] Danger sem auto-dismiss documentado
- [ ] Hover pausa timer documentado
- [ ] Contraste verificado: texto branco sobre fundo escuro ≥ 12:1 (AAA)
- [ ] Violação WCAG AA (cor **crit** · wcag aten · vis aten) resolvida
- [ ] Violações Nielsen (H-1 **crit** · H-3 **crit** · H-9 aten) resolvidas
- [ ] Mapeamento de retrocompatibilidade com `SispLibStyleType` documentado
- [ ] Stacking e posicionamento documentados
- [ ] Revisado e aprovado por Giuliana
