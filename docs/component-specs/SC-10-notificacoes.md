---
component-id: SC-10
component-name: Notificações
type: SISP
status: approved
sprint: 5
approved-by: [Giuliana]
approved-date: [2026-07-22]
figma-node-id: [328:1294]
---

# Component Spec — Notificações

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → SC-10 (cor **crit** — lida/não lida diferenciada apenas por cor · vis aten)
> - `docs/analyses/nielsen-analysis.md` → SC-10 (H-1 **crit** · H-3 **crit** · H-4 aten · H-6 aten · H-8 aten)
> - `docs/analyses/inventory.md` → SC-10
> - Bug confirmado em produção: erro BFF aciona mensagem inline + 2 toasts Danger simultâneos

---

## O que é

Notificações é o componente de centro de notificações do SISP. Gerencia mensagens do sistema recebidas via BFF — alertas operacionais, atualizações de B.O., comunicados internos, e erros de sistema. Organizado em duas views: Caixa de Entrada (não lidas + recentes) e Arquivo (histórico). Na DV, acessado via ícone de sino no Header. Atualmente com bug crítico confirmado em produção: erro de BFF dispara feedback triplo (inline + 2 toasts Danger simultâneos), diferenciação lida/não lida exclusivamente por cor.

---

## Audiência de uso

- **Policial na DV:** recebe notificações de atualização de B.O., medidas protetivas, comunicados da delegacia. Precisa identificar rapidamente notificações urgentes vs. informativas. Precisa marcar como lida sem perder o contexto da tela atual
- **Devs CiASC / terceiros:** componente é auto-suficiente (sem props, dados via BFF). Precisam entender o contrato de eventos para reagir a notificações na aplicação host
- **Demilis (mantenedor):** bug de feedback duplicado é conhecido — resolve neste redesign
- **POs (Sommer/Holiwod):** notificações são canal de comunicação operacional — precisam funcionar sem ruído

---

## Props / API

> **Padrão de API:** Auto-suficiente via BFF (sem props de configuração). O componente busca notificações diretamente do BFF via polling ou WebSocket.

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `(notificationClick)` | `EventEmitter<Notification>` | não | — | Emitido quando usuário clica em uma notificação — app pode navegar para o contexto |
| `(unreadCountChange)` | `EventEmitter<number>` | não | — | Emitido quando contagem de não lidas muda — app pode atualizar badge no Header |
| `maxVisible` | `number` | não | `5` | Máximo de notificações visíveis na lista antes de scroll |
| `pollingInterval` | `number` | não | `30000` | Intervalo de polling em ms (default 30s). 0 = desativa polling (usa WebSocket) |

**Notification:**
```typescript
interface Notification {
  id: string;
  type: 'info' | 'warning' | 'danger' | 'success';
  title: string;
  message: string;
  timestamp: Date;
  read: boolean;
  actionUrl?: string;       // Rota para navegar ao clicar
  actionLabel?: string;     // Label do link de ação (ex: "Ver B.O.")
  source?: string;          // Origem (ex: "Delegacia Central", "Sistema")
}
```

**Convenção Angular (auto-suficiente):**
```html
<sisp-lib-notifications
  (notificationClick)="onNotificationClick($event)"
  (unreadCountChange)="onUnreadCountChange($event)">
</sisp-lib-notifications>
```

**Exemplo de uso (aplicação host):**
```typescript
onNotificationClick(notification: Notification) {
  if (notification.actionUrl) {
    this.router.navigate([notification.actionUrl]);
  }
}

onUnreadCountChange(count: number) {
  this.headerBadgeCount = count;
}
```

---

## Anatomia do componente

### Trigger (no Header)
```
┌──────────────────────────────────────────────────────────────────┐
│  [Logo]  Delegacia Virtual  │  Painel  │  Consultas  │  🔔(3)  │
└──────────────────────────────────────────────────────────────────┘
                                                         Trigger
                                                     (ícone + badge)
```

### Painel de notificações (dropdown / offcanvas)
```
┌───────────────────────────────────────┐
│  Notificações                    [×]  │  ← Header
│  ─────────────────────────────────── │
│  [Caixa de Entrada (3)]  [Arquivo]   │  ← Tabs (BC-26)
│  ─────────────────────────────────── │
│                                       │
│  ● B.O. 2024/001 atualizado          │  ← Não lida (dot + bold)
│    Delegacia Central · há 5 min       │
│    [Ver B.O. →]                       │
│  ─────────────────────────────────── │
│  ● Medida protetiva urgente          │  ← Não lida + Danger
│    Sistema · há 12 min                │
│    [Ver detalhes →]                   │
│  ─────────────────────────────────── │
│  ○ Comunicado interno               │  ← Lida (sem dot, regular)
│    COSEG · há 2h                      │
│  ─────────────────────────────────── │
│                                       │
│  [Marcar todas como lidas]           │  ← Button Tertiary
└───────────────────────────────────────┘
```

### Item de notificação (detalhado)
```
┌───────────────────────────────────────┐
│  [●] [⚠]  B.O. 2024/001 atualizado  │  ← Dot (não lida) + Ícone tipo + Título (bold)
│            Delegacia Central · 5 min  │  ← Fonte + Timestamp
│            [Ver B.O. →]               │  ← Action link (opcional)
└───────────────────────────────────────┘
     Dot   Icon   Title (Heading/SM)
                   Source · Time (Body/XS muted)
                   Action (Button Tertiary SM)
```

- **Trigger:** ícone de sino (fa-bell) com Badge de contagem (instância BC-04 Badge Danger Filled SM). Badge oculto se count = 0
- **Painel:** dropdown em desktop (360px width, max-height 480px), offcanvas em mobile (100% width)
- **Tabs:** instância de BC-26 Tabs — "Caixa de Entrada" (com contagem) e "Arquivo"
- **Item de notificação:** card com dot indicator (não lida), ícone de tipo, título, fonte, timestamp relativo, ação opcional
- **Footer:** Button Tertiary "Marcar todas como lidas" (instância BC-05 Button Tertiary SM)

> **Regra 11 — Composição atômica:** Badge, Tabs, Button, Toast, Icons são instâncias dos componentes existentes.

---

## Estados e variantes

### Estados do componente

| Estado | Descrição | Conteúdo |
|---|---|---|
| **Com notificações** | Lista populada | Items de notificação (lidos + não lidos) |
| **Vazio** | Sem notificações | Mensagem "Nenhuma notificação" + ícone fa-bell-slash |
| **Loading** | Buscando do BFF | Skeleton (instância BC-18 Skeleton Layers — 3 barras) |
| **Erro de BFF** | Falha na busca | Mensagem de erro + Button "Tentar novamente". **Apenas 1 feedback** (resolve bug de feedback triplo) |

### Estados dos items de notificação

| Estado | Descrição visual | Tokens |
|---|---|---|
| **Não lida** | Dot 8px preenchido + título bold + fundo sutil | dot: `--color-primary` · title: `--font-semibold` · bg: `--color-bg-subtle` |
| **Lida** | Sem dot + título regular + fundo branco | title: `--font-regular` · bg: `--color-surface` |
| **Hover** | Fundo hover | bg: `--color-bg-muted` |
| **Focus** | Focus ring | outline: `2px solid --color-border-focus` |

### Tipos de notificação

| Tipo | Ícone | Cor do ícone | Exemplo DV |
|---|---|---|---|
| `info` | fa-circle-info | `--color-info` | "Comunicado interno da COSEG" |
| `warning` | fa-triangle-exclamation | `--color-warning` | "B.O. 2024/001 pendente há 48h" |
| `danger` | fa-circle-exclamation | `--color-danger` | "Medida protetiva urgente — prazo 24h" |
| `success` | fa-circle-check | `--color-success` | "B.O. 2024/002 homologado com sucesso" |

### Views (Tabs)

| Tab | Conteúdo | Contagem |
|---|---|---|
| **Caixa de Entrada** | Notificações não lidas + lidas recentes (últimos 7 dias) | Número de não lidas entre parênteses |
| **Arquivo** | Histórico completo (todas as lidas, ordenado por data) | — |

### Verificação de contraste (WCAG AA)

| Elemento | Texto | Fundo | Ratio | Resultado |
|---|---|---|---|---|
| Título não lida | #08060F (bold) | #F9FAFB | ≥ 14.8:1 | ✅ AAA |
| Título lida | #08060F | #FFFFFF | ≥ 18.1:1 | ✅ AAA |
| Source + timestamp | #9CA3AF | #FFFFFF | ≥ 2.7:1 | ⚠ AA large only — corrigir para #6B7280 |
| Source + timestamp (corrigido) | #6B7280 | #FFFFFF | ≥ 4.6:1 | ✅ AA |
| Dot não lida | #C4000B | #F9FAFB | ≥ 5.0:1 | ✅ AA (gráfico 3:1) |
| Ícone info | #1E3A8A | #FFFFFF | ≥ 8.9:1 | ✅ AAA |
| Ícone warning | #92400E | #FFFFFF | ≥ 6.3:1 | ✅ AAA |
| Ícone danger | #991B1B | #FFFFFF | ≥ 5.7:1 | ✅ AAA |
| Ícone success | #166534 | #FFFFFF | ≥ 7.5:1 | ✅ AAA |
| Badge contagem (trigger) | #FFFFFF | #991B1B | ≥ 5.7:1 | ✅ AAA |

### Dimensões

| Propriedade | Valor | Token |
|---|---|---|
| Painel width (desktop) | 360px | — |
| Painel max-height | 480px | — |
| Painel border-radius | 8px | `--radius-lg` |
| Painel shadow | shadow/lg | Effect Style |
| Header padding | 16px | `--space-4` |
| Header font size | 16px | `--text-base` |
| Header font weight | 600 | `--font-semibold` |
| Item padding | 12px 16px | `--space-3` / `--space-4` |
| Item gap (interno) | 4px | `--space-1` |
| Dot size (não lida) | 8px | — |
| Dot margin right | 8px | `--space-2` |
| Ícone tipo size | 16px (MD) | — |
| Ícone margin right | 8px | `--space-2` |
| Título font size | 14px | `--text-sm` |
| Source + time font size | 12px | `--text-xs` |
| Action link font size | 12px | `--text-xs` |
| Separador | 1px solid `--color-border` | — |
| Footer padding | 12px 16px | `--space-3` / `--space-4` |
| Badge (trigger) | SM | instância BC-04 |
| Trigger icon size | 20px (LG) | instância BC-15 |
| z-index | `--z-dropdown` (100) | — |

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução neste spec |
|---|---|---|
| Uso de cor (cor **crit**) | Lida vs. não lida diferenciada apenas por cor — sem indicador visual adicional | **3 canais para não lida:** (1) dot 8px preenchido à esquerda, (2) título em bold (--font-semibold), (3) fundo sutil (--color-bg-subtle). Lida: sem dot, título regular, fundo branco. Diferença percepcível sem cor |
| Visual (vis aten) | Estados visuais insuficientes | Dot + bold + fundo sutil para não lida. Ícone de tipo por notificação (info/warning/danger/success). Hover com fundo. Focus ring visível. Timestamp relativo |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução neste spec |
|---|---|---|
| H-1 Visibilidade (CRIT) | Erro de BFF aciona 2 toasts Danger simultâneos + mensagem inline — feedback triplo confuso | **Regra de feedback único:** cada evento gera exatamente 1 feedback. Erro de BFF → 1 mensagem inline no painel "Não foi possível carregar notificações" + Button "Tentar novamente". Zero toasts para erros internos do componente. Toasts reservados para ações do usuário (ex: "Notificação marcada como lida") |
| H-3 Controle (CRIT) | Sem controle sobre quantidade de notificações simultâneas | **Limite visual:** máximo `maxVisible` (default 5) notificações visíveis antes de scroll. Painel tem max-height fixo. "Marcar todas como lidas" limpa contagem. Click no item abre contexto sem remover da lista. Fechar painel preserva estado |
| H-4 Consistência (aten) | Padrão de notificação inconsistente com Toasts | Notificações persistentes (centro de notificações) ≠ Toasts (feedback momentâneo). Tipos de notificação (info/warning/danger/success) usam mesma paleta semântica dos Toasts mas com ícones e cores consistentes. Documentar quando usar cada um |
| H-6 Reconhecimento (aten) | Tipo de notificação não reconhecível | Ícone de tipo (info/warning/danger/success) reforça texto. Badge de contagem no trigger indica pendências sem abrir o painel. Timestamp relativo ("há 5 min") em vez de data absoluta. Source indica origem |
| H-8 Estética (aten) | Design genérico | Painel com sombra, border-radius, separadores sutis. Items com hierarquia tipográfica clara (título bold > source muted > action link). Dot como indicador minimal de status |

---

## Regras de acessibilidade

- [ ] Trigger (sino) com `aria-label="Notificações, N não lidas"` e `aria-haspopup="true"`, `aria-expanded="true|false"`
- [ ] Painel com `role="dialog"` ou `role="region"`, `aria-label="Centro de notificações"`
- [ ] Lista de notificações com `<ul>` / `<li>`, cada item com `role="article"` ou nenhum role especial
- [ ] Item não lido com `aria-label` incluindo "não lida" (ex: "Não lida: B.O. 2024/001 atualizado, Delegacia Central, há 5 minutos")
- [ ] Tabs com `role="tablist"` / `role="tab"` / `aria-selected` (herda BC-26)
- [ ] Badge de contagem com `aria-label="N notificações não lidas"` (atualiza dinamicamente)
- [ ] **Navegação por teclado:**
  - `Tab` foca o trigger (sino)
  - `Enter` / `Space` abre/fecha o painel
  - `↑` / `↓` navega entre items dentro do painel
  - `Enter` no item → navega para `actionUrl`
  - `Escape` fecha o painel, foco retorna ao trigger
- [ ] `aria-live="polite"` na contagem — anuncia novas notificações
- [ ] Focus ring visível: `2px solid var(--color-border-focus)`
- [ ] Contraste verificado — mínimo 4.5:1 AA (source corrigido de #9CA3AF para #6B7280)
- [ ] Labels em português

---

## Comportamentos esperados

### Painel
- Quando clica no ícone de sino → painel abre (dropdown em desktop, offcanvas em mobile). Foco move para primeiro item ou para Tabs
- Quando clica fora do painel ou pressiona Escape → painel fecha, foco retorna ao trigger
- Quando painel abre → busca atualizações do BFF (polling imediato)
- Quando view muda (Caixa de Entrada ↔ Arquivo) → conteúdo troca via Tabs. Estado do scroll reseta

### Items
- Quando clica em item não lido → marca como lido (dot some, bold vira regular, fundo volta a branco). Se `actionUrl` definido, emite `(notificationClick)` e app navega
- Quando clica em item lido → apenas emite `(notificationClick)` se actionUrl
- Quando "Marcar todas como lidas" clicado → todas as não lidas na Caixa de Entrada ficam lidas. Badge de contagem no trigger atualiza para 0. Emite `(unreadCountChange)`
- Quando nova notificação chega via polling → item aparece no topo da Caixa de Entrada com animação slide-in. Badge de contagem incrementa. Emite `(unreadCountChange)`

### Feedback — regra de feedback único
- Quando BFF retorna erro ao buscar notificações → **1 mensagem inline** no painel: "Não foi possível carregar notificações" + Button "Tentar novamente". Zero toasts
- Quando marcar como lida falha → **1 toast Danger** (instância BC-27): "Erro ao marcar notificação como lida". Item volta ao estado anterior
- Quando marcar todas como lidas com sucesso → **1 toast Success** (instância BC-27, auto-dismiss 3s): "Todas as notificações marcadas como lidas"

### Trigger
- Quando count > 0 → Badge Danger Filled SM com número visível no trigger
- Quando count > 99 → Badge exibe "99+"
- Quando count = 0 → Badge oculto

---

## Composição com outros componentes

> **Regra 11 — Composição atômica:** todo elemento que já exista como componente no DS deve ser instância.

| Componente | Relação | Composição no Figma |
|---|---|---|
| **BC-04 Badge** | Badge de contagem no trigger (sino) | **Instância direta** — Badge Danger Filled SM |
| **BC-26 Tabs** | Alternância Caixa de Entrada / Arquivo | **Instância direta** — Tabs Underline com 2 Tab Items |
| **BC-05 Button** | "Marcar todas como lidas", "Tentar novamente", action links | **Instância direta** — Button Tertiary SM |
| **BC-27 Toast** | Feedback de ações do usuário (marcar como lida, erros pontuais) | **Instância direta** — Toast Success/Danger |
| **BC-15 Icons** | Ícone de sino (trigger), ícones de tipo (info/warning/danger/success) | **Font Awesome** — fa-bell, fa-circle-info, fa-triangle-exclamation, etc. |
| **BC-18 Skeleton** | Estado loading | **Instância direta** — 3 Skeleton Layers Text |

> **Regra 12 — auditoria:** Notificações é composição de Badge + Tabs + Button + Toast + Icons + Skeleton. Todos existem como Base Components. O item de notificação é layout custom (não é um Card — é mais leve que BC-06).

---

## Integração com BFF

| Endpoint / Serviço | Ação | Resposta esperada |
|---|---|---|
| Listar notificações | GET periódico (polling) ou WebSocket | `Notification[]` ordenadas por timestamp desc |
| Marcar como lida | PATCH com id da notificação | `{ success: true }` |
| Marcar todas como lidas | PATCH em batch | `{ success: true, count: N }` |
| Contar não lidas | GET (polling rápido para badge) | `{ unreadCount: number }` |

**Estratégia de polling:** contagem de não lidas a cada 30s (configurável via `pollingInterval`). Lista completa apenas quando painel abre. WebSocket preferido se disponível.

**Regra de feedback único (resolve bug de produção):**
- Erro de BFF na **busca** → inline no painel, zero toasts
- Erro de BFF em **ação do usuário** → 1 toast Danger
- Sucesso em **ação do usuário** → 1 toast Success (auto-dismiss)
- **Nunca:** inline + toast. **Nunca:** 2 toasts simultâneos

---

## Guia: Notificação vs. Toast

| Critério | Notificação (SC-10) | Toast (BC-27) |
|---|---|---|
| **Persistência** | Persistente — fica no centro de notificações | Momentâneo — desaparece após timeout |
| **Origem** | BFF / sistema / outros usuários | Ação do usuário na sessão atual |
| **Acesso** | Via trigger (sino) no Header | Automático (aparece no topo/canto) |
| **Histórico** | Sim — Caixa de Entrada + Arquivo | Não — após fechar, perdido |
| **Exemplo DV** | "B.O. 2024/001 atualizado por outro policial" | "B.O. salvo com sucesso" |
| **Quando usar** | Informação que o usuário pode precisar consultar depois | Feedback imediato de ação recém-executada |

---

## Mapeamento de retrocompatibilidade

| Estado atual | Mapeamento novo | Nota |
|---|---|---|
| Caixa de entrada | Tab "Caixa de Entrada" com contagem | Mantido |
| Arquivo | Tab "Arquivo" | Mantido |
| Erro de BFF → 2 toasts + inline | **1 mensagem inline no painel** | Bug resolvido — regra de feedback único |
| Lida/não lida (apenas cor) | 3 canais: dot + bold + fundo | WCAG crit resolvida |
| Loading (não documentado) | Skeleton Layers | **Novo** |
| Vazio | Mensagem + ícone fa-bell-slash | **Novo** |
| — | EventEmitters (notificationClick, unreadCountChange) | **Novo** |
| — | maxVisible, pollingInterval | **Novo** |

---

## Variantes no Figma

| Variante | Property | Descrição |
|---|---|---|
| **Painel com notificações** | `State=Populated` | Lista com 3-4 items (mix lidos + não lidos), tabs, badge no trigger |
| **Painel vazio** | `State=Empty` | Mensagem "Nenhuma notificação" + ícone |
| **Painel loading** | `State=Loading` | Skeleton layers |
| **Painel erro** | `State=Error` | Mensagem de erro + botão retry |
| **Trigger com badge** | `Badge=Visible` | Ícone sino + Badge Danger SM com contagem |
| **Trigger sem badge** | `Badge=Hidden` | Ícone sino sem badge |

---

## Casos excepcionais / bordas

- **0 notificações:** painel exibe mensagem vazia "Nenhuma notificação" com ícone fa-bell-slash muted. Badge oculto no trigger
- **100+ notificações:** badge exibe "99+" no trigger. Lista com scroll. Paginação no BFF (20 por página, load more ao scroll)
- **Notificação sem actionUrl:** item clicável (marca como lida) mas sem link de ação
- **Notificação com título longo (> 80 chars):** trunca com ellipsis em 2 linhas. Tooltip com título completo
- **Polling com rede instável:** retry silencioso. Se 3 falhas consecutivas, exibe inline "Falha ao atualizar notificações"
- **Mobile (< 1024px):** painel vira offcanvas (100% width), ativado pelo trigger no Header
- **Múltiplas abas:** cada aba mantém polling independente. Marcar como lida em uma aba reflete nas outras via BFF na próxima poll
- **Notificação danger de medida protetiva:** prioridade visual — sempre no topo da lista, destaque com borda esquerda 3px `--color-danger`

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-surface` | Fundo do painel e items lidos |
| `--color-bg-subtle` | Fundo items não lidos |
| `--color-bg-muted` | Fundo hover |
| `--color-text-primary` | Título de notificação |
| `--color-text-secondary` | Source + timestamp (corrigido de #9CA3AF para #6B7280) |
| `--color-text-muted` | Ícone sino (trigger default), mensagem vazia |
| `--color-primary` | Dot de não lida |
| `--color-danger` / `--color-danger-bg` | Badge trigger, ícone tipo danger |
| `--color-warning` | Ícone tipo warning |
| `--color-info` | Ícone tipo info |
| `--color-success` | Ícone tipo success |
| `--color-border` | Separadores entre items |
| `--color-border-focus` | Focus ring |
| `--font-body` | Família tipográfica |
| `--text-sm` | Título (14px) |
| `--text-xs` | Source + timestamp + action (12px) |
| `--text-base` | Header do painel (16px) |
| `--font-semibold` | Título não lida, header |
| `--font-regular` | Título lida |
| `--space-1` / `--space-2` / `--space-3` / `--space-4` | Gaps e paddings |
| `--radius-lg` | Border-radius do painel (8px) |
| `--shadow-lg` | Sombra do painel |
| `--z-dropdown` | Z-index do painel (100) |

---

## O que está fora deste spec

- **Push notifications (browser):** desativado por contexto policial (delegacia). Apenas polling/WebSocket no BFF
- **Notificações com ações inline (aprovar/rejeitar):** complexidade excessiva. Ação é navegar para o contexto (actionUrl)
- **Configuração de preferências de notificação:** tela de settings separada, não do componente
- **Notificações por tipo de B.O.:** filtragem é responsabilidade do BFF, não do componente visual
- **Notificações em tempo real (WebSocket):** documentado como preferência, mas polling é o fallback. Implementação de WebSocket é responsabilidade do BFF
- **Som/vibração:** excluído por contexto operacional

---

## Critérios de aceite

- [ ] 4 estados do painel no Figma: Populated, Empty, Loading, Error
- [ ] 2 estados do trigger: com Badge (contagem), sem Badge
- [ ] Diferenciação lida/não lida com 3 canais: dot + bold + fundo (resolve WCAG cor **crit**)
- [ ] 4 tipos de notificação com ícone semântico (info/warning/danger/success)
- [ ] Tabs como instância de BC-26 Tabs Underline (Regra 11)
- [ ] Badge como instância de BC-04 Badge Danger Filled SM (Regra 11)
- [ ] Buttons como instâncias de BC-05 Button (Regra 11)
- [ ] Skeleton como instância de BC-18 Skeleton Layers Text (Regra 11)
- [ ] **Regra de feedback único:** cada evento = exatamente 1 feedback (resolve bug de produção H-1 crit)
- [ ] Guia Notificação vs. Toast documentado
- [ ] Contraste verificado — mínimo 4.5:1 AA (source corrigido para #6B7280)
- [ ] ARIA documentado: trigger com aria-haspopup/expanded, lista com ul/li, badges com aria-label
- [ ] Navegação por teclado: Tab, ↑↓, Enter, Escape
- [ ] EventEmitters documentados: notificationClick, unreadCountChange
- [ ] Violações WCAG (cor **crit** · vis aten) resolvidas
- [ ] Violações Nielsen (H-1 **crit** · H-3 **crit** · H-4 aten · H-6 aten · H-8 aten) resolvidas
- [ ] Integração BFF documentada (polling, ações, regra de feedback)
- [ ] Mapeamento de retrocompatibilidade documentado
- [ ] Labels em português
- [ ] Revisado e aprovado por Giuliana
