---
component-id: SC-12
component-name: Session Control
type: SISP
status: approved
sprint: 5
approved-by: [Giuliana]
approved-date: [2026-07-22]
figma-node-id: [325:1070]
---

# Component Spec — Session Control

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → SC-12 (cor aten — countdown sem feedback progressivo · vis aten)
> - `docs/analyses/nielsen-analysis.md` → SC-12 (H-1 **crit** · H-3 **crit** · H-5 **crit** · H-9 **crit** · H-2 aten · H-6 aten)
> - `docs/analyses/inventory.md` → SC-12
> - Contexto operacional: pesquisa discovery — caso de medidas protetivas de urgência com perda de dados por expiração de sessão

> ⚠️ **Maior risco operacional do DS SISP.** Policiais em operação perdem dados de B.O. se a sessão expirar sem aviso suficiente. 4 violações Nielsen críticas. Componente mais importante do Sprint 5.

---

## O que é

Session Control é o componente de gerenciamento visual de sessão do SISP. Exibe o estado da sessão autenticada (SISP + OAuth), tempo restante com feedback progressivo de urgência (verde → âmbar → vermelho), e permite renovação proativa da sessão. Na DV, está embarcado no layout global — visível em todas as telas. Atualmente sem feedback progressivo, sem botão de renovação proeminente, e sem documentação do comportamento na expiração.

---

## Audiência de uso

- **Policial na DV:** precisa saber quanto tempo resta de sessão enquanto preenche B.O. Se a sessão expirar durante registro de medida protetiva de urgência, dados podem ser perdidos. Precisa renovar sessão sem interromper o trabalho
- **Devs CiASC / terceiros:** precisam integrar o controle de sessão no layout global. Componente é auto-suficiente (lê estado interno do sisp-lib), mas precisa emitir eventos de expiração para que a aplicação possa salvar rascunhos
- **Demilis (mantenedor):** confirma que o componente estava embarcado no portal sem ser reconhecido como componente separado — formalizar como componente do DS
- **POs (Sommer/Holiwod):** risco operacional real documentado em pesquisa — prioridade máxima

---

## Props / API

> **Padrão de API:** Auto-suficiente via BFF (sem props de configuração). O componente lê o estado de sessão interno do `sisp-lib`. Padrão documentado como-está para retrocompatibilidade. Para novos componentes, recomenda-se o padrão Config object.

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `(sessionExpiring)` | `EventEmitter<SessionState>` | não | — | Emitido quando sessão entra em estado `warning` (≤ 5min) |
| `(sessionExpired)` | `EventEmitter<void>` | não | — | Emitido quando sessão expira — app pode salvar rascunho |
| `(sessionRenewed)` | `EventEmitter<SessionState>` | não | — | Emitido após renovação bem-sucedida |
| `warningThreshold` | `number` | não | `300` | Segundos antes da expiração para entrar em estado warning (default 5min) |
| `criticalThreshold` | `number` | não | `60` | Segundos antes da expiração para entrar em estado critical (default 1min) |

**SessionState:**
```typescript
interface SessionState {
  sispAuthenticated: boolean;    // Sessão SISP ativa
  oauthAuthenticated: boolean;   // OAuth ativo
  timeRemaining: number;         // Segundos restantes
  status: 'active' | 'warning' | 'critical' | 'expired';
  userName?: string;             // Nome do usuário logado
}
```

**Convenção Angular (auto-suficiente):**
```html
<!-- Sem props — lê estado interno do sisp-lib -->
<sisp-lib-session-control
  (sessionExpiring)="onSessionWarning($event)"
  (sessionExpired)="onSessionExpired()"
  (sessionRenewed)="onSessionRenewed($event)">
</sisp-lib-session-control>
```

**Exemplo de uso (aplicação host):**
```typescript
onSessionWarning(state: SessionState) {
  // Salvar rascunho automaticamente
  this.autoSave();
}

onSessionExpired() {
  // Redirecionar para login com mensagem
  this.router.navigate(['/login'], {
    queryParams: { reason: 'session-expired' }
  });
}
```

---

## Anatomia do componente

### Barra de sessão (embarcada no layout global)
```
┌─────────────────────────────────────────────────────────────────┐
│  [●] Sessão ativa   │   ⏱ 28:45   │   João Silva   │ [Renovar] │
└─────────────────────────────────────────────────────────────────┘
         Badge             Timer         Usuário         Button
       (status)         (countdown)                   (renovar)
```

### Estado warning (≤ 5 min)
```
┌─────────────────────────────────────────────────────────────────┐
│  [●] Sessão expirando │  ⏱ 4:32   │  João Silva  │ [Renovar ▸] │
│  ██████████░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░  (progress)   │
└─────────────────────────────────────────────────────────────────┘
   Badge Warning           Timer âmbar                 Button Primary
```

### Estado critical (≤ 1 min)
```
┌─────────────────────────────────────────────────────────────────┐
│  [●] Sessão expirando! │  ⏱ 0:42  │  João Silva  │ [RENOVAR!]  │
│  ████░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░  (progress)   │
└─────────────────────────────────────────────────────────────────┘
   Badge Danger            Timer vermelho               Button Danger
```

### Modal de expiração
```
┌───────────────────────────────────────┐
│  ⚠ Sessão Expirada               [×] │
│  ─────────────────────────────────── │
│                                       │
│  Sua sessão SISP expirou.            │
│  O rascunho do seu trabalho foi      │
│  salvo automaticamente.              │
│                                       │
│  ─────────────────────────────────── │
│              [Fazer login novamente]  │
└───────────────────────────────────────┘
   Instância BC-19 Modal (Confirmation)
```

- **Badge:** instância de BC-04 Badge — indica status da sessão com cor + ícone + texto (3 canais, nunca apenas cor)
- **Timer:** countdown mm:ss com cor progressiva
- **Progress bar:** barra de progresso determinada — visualização linear do tempo restante
- **Botão Renovar:** instância de BC-05 Button — muda de Secondary (ativo) para Primary (warning) para Danger (critical)
- **Informação do usuário:** nome do usuário logado
- **Modal de expiração:** instância de BC-19 Modal Confirmation ao expirar

> **Regra 11 — Composição atômica:** Badge, Button e Modal são instâncias dos componentes existentes. Progress bar usa o padrão visual de BC-16 Loader Bar Determinate.

---

## Estados e variantes

### Estados da sessão

| Estado | Condição | Descrição visual | Badge | Timer | Button | Progress |
|---|---|---|---|---|---|---|
| **Active** | > warningThreshold | Tudo normal | Success Filled SM "Ativa" | Verde `--color-success` | Secondary SM "Renovar" | 100% verde |
| **Warning** | ≤ warningThreshold, > criticalThreshold | Urgência moderada | Warning Filled SM "Expirando" | Âmbar `--color-warning` | Primary SM "Renovar" | Decrescente âmbar |
| **Critical** | ≤ criticalThreshold | Urgência máxima | Danger Filled SM "Expirando!" | Vermelho `--color-danger`, bold, pulsa | Primary SM "RENOVAR" (caps) | Decrescente vermelho, pulsa |
| **Expired** | = 0 | Sessão perdida | Danger Filled SM "Expirada" | — | — | 0% |
| **OAuth Inativo** | SISP ativo, OAuth não | Login parcial | Info Filled SM "Sem OAuth" | — | Secondary SM "Conectar OAuth" | — |
| **Renovando** | Após clique em Renovar | Aguardando BFF | — | — | Loading (instância BC-16 Spinner SM) | — |
| **Erro de Renovação** | BFF falhou | Feedback de erro | Danger Filled SM "Erro" | Mantém countdown | Danger SM "Tentar novamente" | Mantém estado |

### Verificação de contraste (WCAG AA)

| Elemento | Texto | Fundo | Ratio | Resultado |
|---|---|---|---|---|
| Badge Success | #166534 | #DCFCE7 | ≥ 6.2:1 | ✅ AAA |
| Badge Warning | #92400E | #FEF3C7 | ≥ 6.2:1 | ✅ AAA |
| Badge Danger | #991B1B | #FEE2E2 | ≥ 6.2:1 | ✅ AAA |
| Badge Info | #1E3A8A | #DBEAFE | ≥ 6.2:1 | ✅ AAA |
| Timer active (verde) | #166534 | #FFFFFF | ≥ 7.5:1 | ✅ AAA |
| Timer warning (âmbar) | #92400E | #FFFFFF | ≥ 6.3:1 | ✅ AAA |
| Timer critical (vermelho) | #991B1B | #FFFFFF | ≥ 5.7:1 | ✅ AAA |
| User name | #4B5563 | #FFFFFF | ≥ 7.2:1 | ✅ AAA |

### Dimensões

| Propriedade | Valor | Token |
|---|---|---|
| Barra height | 40px | — |
| Barra padding horizontal | 16px | `--space-4` |
| Barra padding vertical | 8px | `--space-2` |
| Gap entre elementos | 12px | `--space-3` |
| Progress bar height | 4px | — |
| Progress bar border-radius | 9999px | `--radius-full` |
| Timer font size | 14px | `--text-sm` |
| Timer font weight (active) | 400 | `--font-regular` |
| Timer font weight (critical) | 700 | `--font-bold` |
| Timer font family | Fira Code | `--font-mono` |
| User name font size | 14px | `--text-sm` |
| Badge | SM (instância BC-04) | — |
| Button | SM (instância BC-05) | — |
| Separador vertical | 1px solid `--color-border` | — |
| Barra fundo | `--color-surface` | — |
| Barra border-bottom | 1px solid `--color-border` | — |
| Pulse animation (critical) | `pulse 1s ease-in-out infinite` | — |
| z-index | `--z-sticky` (200) | — |

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução neste spec |
|---|---|---|
| Uso de cor (cor aten) | Countdown sem feedback progressivo — apenas verde sem transição | **3 estados visuais progressivos:** Active (verde) → Warning (âmbar) → Critical (vermelho). Cada estado comunica urgência via 4 canais: (1) cor do timer, (2) Badge com texto + ícone + cor, (3) progress bar visual, (4) mudança de estilo do Button. Nunca depende apenas de cor — Badge tem texto "Ativa"/"Expirando"/"Expirada" |
| Visual (vis aten) | Estados de sessão sem indicadores visuais claros | Progress bar mostra tempo restante linearmente. Timer em font mono para legibilidade. Badge com 3 canais. Estado critical pulsa para chamar atenção. Modal de expiração é overlay blocking |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução neste spec |
|---|---|---|
| H-1 Visibilidade (CRIT) | Countdown existe mas sem feedback progressivo — urgência invisível | **4 canais progressivos:** Badge (texto + cor + ícone muda com estado), Timer (cor muda: verde → âmbar → vermelho), Progress bar (diminui linearmente), Button (muda de Secondary para Primary para Danger). Transições são automáticas nos thresholds configuráveis |
| H-3 Controle (CRIT) | Sem botão de renovar sessão proeminente — usuário perde trabalho | **Botão "Renovar" sempre visível** na barra de sessão. Em warning, botão promovido para Primary (vermelho). Em critical, botão promovido para Danger com texto "RENOVAR" em caps. Clique único renova — sem confirmação necessária para ação positiva |
| H-5 Prevenção (CRIT) | Sessão pode expirar sem aviso sonoro/visual suficiente | **Prevenção em 3 camadas:** (1) Warning aos 5min — Badge muda, timer muda de cor, progress bar aparece, botão promovido. (2) Critical ao 1min — pulsação visual, timer bold. (3) EventEmitter `(sessionExpiring)` permite que a aplicação salve rascunho automaticamente. Thresholds configuráveis via props |
| H-9 Recuperação (CRIT) | Comportamento na expiração não documentado — usuário não sabe o que acontece | **Modal de expiração** (instância BC-19) informa: (1) que a sessão expirou, (2) que o rascunho foi salvo automaticamente, (3) botão "Fazer login novamente" com redireção. EventEmitter `(sessionExpired)` permite que a app salve estado antes do redirect. Após re-login, rascunho recuperável |
| H-2 Mundo real (aten) | Vocabulário técnico ("OAuth inativo") | Labels em português sem jargão: "Sessão ativa", "Sessão expirando", "Sessão expirada". OAuth informado como "Conexão complementar inativa" com tooltip explicativo |
| H-6 Reconhecimento (aten) | Controles de sessão não evidentes | Barra de sessão é persistente (sticky), visível em todas as telas. Timer em fonte mono para destaque. Badge com texto explícito ("Ativa", "Expirando"). Ícones semânticos reforçam texto |

---

## Regras de acessibilidade

- [ ] Barra de sessão com `role="status"` e `aria-live="polite"` (anuncia mudanças de estado)
- [ ] Timer com `aria-label="Tempo restante de sessão: X minutos e Y segundos"`
- [ ] Em estado warning, `aria-live` muda para `"assertive"` (prioridade de anúncio)
- [ ] Badge com `aria-label` descritivo ("Status da sessão: ativa" / "expirando" / "expirada")
- [ ] Botão Renovar com `aria-label="Renovar sessão"` — navegável por Tab
- [ ] Progress bar com `role="progressbar"`, `aria-valuenow`, `aria-valuemin="0"`, `aria-valuemax` (tempo total da sessão), `aria-label="Tempo restante de sessão"`
- [ ] Modal de expiração com focus trap (herda de BC-19)
- [ ] Focus ring visível em todos os elementos interativos: `2px solid var(--color-border-focus)`
- [ ] Contraste verificado — mínimo 4.5:1 AA para todos os textos e estados
- [ ] **Não usar som/alerta sonoro** para indicar expiração — pode ser inadequado em operação policial (ambiente de delegacia). Feedback exclusivamente visual
- [ ] Labels em português
- [ ] Animação de pulso respeita `prefers-reduced-motion` — desativa se ativo

---

## Comportamentos esperados

### Ciclo de vida da sessão
- Quando sessão inicia → barra de sessão exibe estado Active: Badge Success "Ativa", timer verde, Button Secondary "Renovar"
- Quando timer atinge warningThreshold (default 5min) → transição para Warning: Badge Warning "Expirando", timer âmbar, progress bar aparece, Button promovido para Primary. Emite `(sessionExpiring)`
- Quando timer atinge criticalThreshold (default 1min) → transição para Critical: Badge Danger "Expirando!", timer vermelho bold pulsante, Button "RENOVAR". `aria-live` muda para `"assertive"`
- Quando timer atinge 0 → estado Expired: Modal de expiração abre (BC-19 Confirmation). Emite `(sessionExpired)`. App deve salvar rascunho

### Renovação
- Quando clica em "Renovar" → estado Renovando: Button mostra Spinner SM (instância BC-16). Timer pausa. Chamada ao BFF para renovar token
- Quando BFF retorna sucesso → estado Active restaurado. Timer reinicia. Emite `(sessionRenewed)`. Toast Success "Sessão renovada" (instância BC-27, auto-dismiss 3s)
- Quando BFF retorna erro → estado Erro de Renovação: Badge Danger "Erro", Button Danger "Tentar novamente". Timer continua. Toast Danger com mensagem do BFF (instância BC-27, sem auto-dismiss)

### OAuth
- Quando SISP autenticado mas OAuth não → estado OAuth Inativo: Badge Info "Sem OAuth", Button Secondary "Conectar OAuth". Funcionalidade limitada documentada no tooltip
- Quando OAuth conectado → Badge atualiza, funcionalidade completa

### Modal de expiração
- Quando modal de expiração abre → overlay blocking, foco preso na modal (herda BC-19)
- Quando clica "Fazer login novamente" → redirect para rota de login com `queryParams: { reason: 'session-expired' }`
- Quando fecha modal (× ou Escape) → mesma ação que "Fazer login novamente" (não há como continuar sem sessão)

### Layout
- Quando viewport ≥ 1024px (desktop) → barra horizontal sticky abaixo do BC-14 Header
- Quando viewport < 1024px (mobile) → barra compacta: Badge + Timer + Button (sem nome de usuário). Progress bar abaixo

---

## Composição com outros componentes

> **Regra 11 — Composição atômica:** todo elemento que já exista como componente no DS deve ser instância.

| Componente | Relação | Composição no Figma |
|---|---|---|
| **BC-04 Badge** | Status da sessão (Active/Warning/Critical/Expired/OAuth) | **Instância direta** — Badge Filled SM em 4 cores semânticas |
| **BC-05 Button** | Botão "Renovar" / "Conectar OAuth" | **Instância direta** — muda de Secondary SM para Primary SM para Danger SM conforme estado |
| **BC-16 Loader** | Progress bar de tempo + Spinner durante renovação | **Padrão visual** — Progress bar usa o padrão de Loader Bar Determinate. Spinner SM durante chamada BFF |
| **BC-19 Modal** | Modal de expiração | **Instância direta** — Modal Confirmation |
| **BC-27 Toast** | Feedback de renovação (sucesso/erro) | **Instância direta** — Toast Success (auto-dismiss) ou Toast Danger (sem auto-dismiss) |
| **BC-15 Icons** | Ícones nos badges e timer | **Font Awesome** — fa-circle-check (active), fa-clock (warning), fa-triangle-exclamation (critical), fa-circle-xmark (expired) |

> **Regra 12 — auditoria:** "este elemento já existe?" Badge, Button, Loader, Modal, Toast, Icons — todos existem como Base Components. Session Control é composição pura de componentes existentes com lógica de domínio.

---

## Integração com BFF

> SISP Components são auto-suficientes via BFF. Session Control lê estado interno do `sisp-lib`.

| Endpoint / Serviço | Ação | Resposta esperada |
|---|---|---|
| Estado de sessão | Leitura periódica (polling ou WebSocket) | `SessionState` com `timeRemaining`, `sispAuthenticated`, `oauthAuthenticated` |
| Renovar sessão | POST ao renovar | Novo token + `timeRemaining` resetado |
| OAuth connect | Redirect para provedor OAuth | Token OAuth + callback de retorno |

**Estratégia de polling:** a cada 30s quando Active, a cada 10s quando Warning, a cada 5s quando Critical. WebSocket preferido se disponível.

---

## Mapeamento de retrocompatibilidade

| Estado atual | Mapeamento novo | Nota |
|---|---|---|
| Barra verde + countdown (SISP ativa) | Estado Active (badge + timer + progress) | Expandido com feedback progressivo |
| "Login sem OAuth" | Estado OAuth Inativo (badge Info + botão) | Formalizado como estado |
| Sessão expirando (visual não documentado) | Estados Warning + Critical | **Novo** — não existia |
| Sessão expirada (não documentado) | Estado Expired + Modal de expiração | **Novo** — não existia |
| — | EventEmitters (sessionExpiring/sessionExpired/sessionRenewed) | **Novo** — permite que a app reaja |
| — | warningThreshold / criticalThreshold | **Novo** — thresholds configuráveis |

---

## Variantes no Figma

| Variante | Property | Descrição |
|---|---|---|
| **Active** | `State=Active` | Barra com Badge Success + Timer verde + Button Secondary |
| **Warning** | `State=Warning` | Barra com Badge Warning + Timer âmbar + Progress bar + Button Primary |
| **Critical** | `State=Critical` | Barra com Badge Danger + Timer vermelho pulsante + Progress bar + Button Danger |
| **Expired** | `State=Expired` | Modal de expiração (BC-19 Confirmation) |
| **OAuth Inativo** | `State=OAuth-Inactive` | Barra com Badge Info + Button "Conectar OAuth" |

---

## Comportamento responsivo

> Regra 13 — Responsividade obrigatória. Breakpoints: Mobile (<768px), Tablet (768–1023px), Desktop (≥1024px).

| Precisa de variante `Layout=Mobile`? | **Sim** — barra full-width precisa de demonstração mobile |
|---|---|
| **Desktop** | Barra 800px full-width. User Name com espaço amplo |
| **Mobile** | Barra 375px. User Name comprimido via FILL. Elementos HUG (Badge, Timer, Button) mantêm tamanho. Conteúdo cabe porque User Name é flexível |
| **Tablet** | Segue Desktop |

**Variantes no Figma:** 10 (5 estados × 2 layouts)

---

## Casos excepcionais / bordas

- **Múltiplas abas abertas:** cada aba tem sua própria instância de Session Control. Renovação em uma aba propaga para as outras via BFF (token compartilhado). Timer sincroniza na próxima poll
- **Sessão expirada durante preenchimento de B.O.:** EventEmitter `(sessionExpiring)` aos 5min aciona auto-save. `(sessionExpired)` salva rascunho final. Após re-login, rascunho recuperável (responsabilidade da app, não do componente)
- **BFF indisponível:** renovação falha → Toast Danger. Timer continua. Se expirar, modal de expiração abre normalmente
- **Conexão de rede perdida:** componente detecta falha de polling → Badge Warning "Sem conexão". Renovação desabilitada até reconexão
- **Thresholds customizados:** app pode definir warningThreshold e criticalThreshold. Valores mínimos: warning ≥ 60s, critical ≥ 30s
- **Sessão muito longa (> 99:59):** timer exibe "99:59+" e não mostra progress bar. Ativa progress bar apenas quando entra em warning
- **Mobile (< 1024px):** barra compacta sem nome de usuário. Badge compacto (ícone sem texto). Timer e botão mantidos

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-surface` | Fundo da barra |
| `--color-border` | Border-bottom da barra, separadores |
| `--color-success` / `--color-success-bg` | Timer active, Badge Success |
| `--color-warning` / `--color-warning-bg` | Timer warning, Badge Warning |
| `--color-danger` / `--color-danger-bg` | Timer critical, Badge Danger |
| `--color-info` / `--color-info-bg` | Badge OAuth inativo |
| `--color-primary` | Button Primary / Danger |
| `--color-text-primary` | Nome do usuário |
| `--color-text-secondary` | Timer active |
| `--color-text-muted` | Separadores |
| `--color-border-focus` | Focus ring |
| `--font-mono` | Timer (Fira Code) |
| `--font-body` | Labels |
| `--text-sm` | Font size (14px) |
| `--font-regular` / `--font-bold` | Peso do timer |
| `--space-2` / `--space-3` / `--space-4` | Padding e gaps |
| `--radius-full` | Progress bar |
| `--z-sticky` | Z-index da barra |
| `--transition-normal` | Transições de estado |

---

## O que está fora deste spec

- **Auto-logout com confirmação:** Session Control gerencia o visual e emite eventos. A lógica de logout/redirect é responsabilidade da aplicação host
- **Persistência de rascunho:** o componente emite `(sessionExpiring)` e `(sessionExpired)` — a app decide o que salvar e como recuperar
- **Notificação sonora/vibração:** excluído por contexto operacional (delegacia). Apenas feedback visual
- **Integração com SC-08 Login:** Session Control não gerencia o fluxo de login — apenas monitora e renova sessão existente. Login é componente separado (Sprint 5, bloqueado)
- **Refresh token automático (background):** pode ser implementado na app. Session Control foca no feedback visual e renovação manual
- **Multi-sessão (SISP + outro sistema):** fora do escopo. Uma sessão por vez

---

## Critérios de aceite

- [ ] 5 estados visuais no Figma: Active, Warning, Critical, Expired (modal), OAuth Inactive
- [ ] Feedback progressivo com 4 canais: Badge (texto + ícone + cor), Timer (cor), Progress bar (linear), Button (estilo)
- [ ] Badge como instância de BC-04 Badge Filled SM (Regra 11)
- [ ] Button como instância de BC-05 Button SM — muda de Secondary → Primary → Danger (Regra 11)
- [ ] Progress bar seguindo padrão visual de BC-16 Loader Bar Determinate (Regra 11)
- [ ] Modal de expiração como instância de BC-19 Modal Confirmation (Regra 11)
- [ ] Toast de feedback como instância de BC-27 Toast (Regra 11)
- [ ] Timer em fonte mono (Fira Code) para legibilidade
- [ ] Contraste verificado — mínimo 4.5:1 AA para todos os estados (AAA atingido em todos)
- [ ] `role="status"`, `aria-live`, `role="progressbar"` documentados
- [ ] EventEmitters documentados: sessionExpiring, sessionExpired, sessionRenewed
- [ ] Thresholds configuráveis (warningThreshold, criticalThreshold)
- [ ] Violações WCAG (cor aten · vis aten) resolvidas — 4 canais, nunca apenas cor
- [ ] Violações Nielsen (H-1 **crit** · H-3 **crit** · H-5 **crit** · H-9 **crit** · H-2 aten · H-6 aten) resolvidas
- [ ] Mapeamento de retrocompatibilidade documentado
- [ ] Integração BFF documentada (polling, renovação, erros)
- [ ] Labels em português
- [ ] Revisado e aprovado por Giuliana
