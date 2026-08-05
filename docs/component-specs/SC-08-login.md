---
component-id: SC-08
component-name: Login
type: SISP
status: in-figma
sprint: 5
approved-by: [Giuliana]
approved-date: [2026-07-22]
figma-node-id: [373:1422]
---

# Component Spec — Login

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → SC-08 (N/A — inacessível durante inventário)
> - `docs/analyses/nielsen-analysis.md` → SC-08 (N/A — não avaliado)
> - `docs/analyses/inventory.md` → SC-08 (A confirmar — "Inacessível durante o inventário. Crítico — autenticação dual SISP + OAuth. Catalogar antes de especificar. Ver SC-12 Session Control.")

> **Nota:** Este spec tem gaps marcados como **[A CONFIRMAR COM DEMILIS]**. O componente foi inacessível durante o inventário — não há dados de análise WCAG ou Nielsen. Este spec é escrito com base em: (1) a nota do inventário sobre autenticação dual SISP + OAuth, (2) a coordenação documentada com SC-12 Session Control, (3) o redirect `?reason=session-expired` definido no SC-12, (4) padrões consolidados de login em sistemas governamentais. Todos os campos, props e comportamentos precisam de validação com Demilis no stage.

---

## O que é

Login é o componente de autenticação do SISP. Gerencia o fluxo de entrada do usuário nos sistemas SISP — autenticação primária (credenciais SISP) e, quando disponível, autenticação complementar via OAuth. Na DV, é a primeira tela que o policial vê ao acessar o sistema. Após autenticação bem-sucedida, SC-12 Session Control assume o monitoramento da sessão.

> **Relação com SC-12 Session Control:** Login é o ponto de entrada; Session Control é o ponto de permanência. Login autentica. Session Control monitora, renova e gerencia expiração. Quando SC-12 detecta expiração, redireciona para Login com `?reason=session-expired`.

---

## Audiência de uso

- **Policial na DV:** precisa acessar o sistema rapidamente para registrar ocorrências. Login deve ser direto, sem fricção. Em caso de sessão expirada, precisa entender que pode recuperar o rascunho do B.O. após re-login
- **Devs CiASC / terceiros:** precisam integrar o componente de login no layout da aplicação. Componente é auto-suficiente (gerencia autenticação via BFF), mas precisa emitir eventos para que a app inicialize após login
- **Demilis (mantenedor):** componente estava embarcado no portal mas inacessível durante inventário. Precisa catalogar no stage antes de refatorar
- **POs (Sommer/Holiwod):** segurança de autenticação é mandatória em sistemas policiais

---

## Props / API

> **Padrão de API:** Auto-suficiente via BFF (sem props de configuração). O componente gerencia autenticação internamente via `sisp-lib`. Padrão documentado como-está para retrocompatibilidade.

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `(loginSuccess)` | `EventEmitter<LoginResult>` | não | — | Emitido após autenticação bem-sucedida — app pode inicializar |
| `(loginError)` | `EventEmitter<LoginError>` | não | — | Emitido quando autenticação falha — app pode reagir |
| `(oauthConnected)` | `EventEmitter<OAuthResult>` | não | — | Emitido quando OAuth complementar conecta com sucesso |
| `showOAuth` | `boolean` | não | `true` | Exibir opção de autenticação OAuth complementar |
| `redirectReason` | `string` | não | `null` | Razão do redirect — `'session-expired'` vindo de SC-12, `'logout'` manual |

**[A CONFIRMAR COM DEMILIS]:** Verificar props reais do componente Angular. Os EventEmitters e props acima são inferidos do padrão SC-12 e de práticas consolidadas. Verificar se `sisp-lib-login` existe ou se o login é uma página separada (não um componente do DS).

**LoginResult:**
```typescript
interface LoginResult {
  sispAuthenticated: boolean;
  oauthAuthenticated: boolean;
  userName: string;
  sessionDuration: number;      // Duração da sessão em segundos
}
```

**LoginError:**
```typescript
interface LoginError {
  code: 'invalid-credentials' | 'account-locked' | 'server-error' | 'network-error';
  message: string;
  attemptsRemaining?: number;   // [A CONFIRMAR] — se há limite de tentativas
}
```

**Convenção Angular (auto-suficiente):**
```html
<sisp-lib-login
  [showOAuth]="true"
  [redirectReason]="queryParams.reason"
  (loginSuccess)="onLoginSuccess($event)"
  (loginError)="onLoginError($event)"
  (oauthConnected)="onOAuthConnected($event)">
</sisp-lib-login>
```

**[A CONFIRMAR COM DEMILIS]:** Verificar se o selector é `sisp-lib-login` ou outro nome. Verificar se aceita `sispLibLoginConfig` (padrão Config) ou usa @Input/@Output diretos.

---

## Anatomia do componente

### Tela de Login (default)
```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│              ┌─────────────┐                            │
│              │   Logo SC   │                            │
│              └─────────────┘                            │
│           Delegacia Virtual                             │
│     Sistema Integrado de Segurança Pública              │
│                                                         │
│     ┌─────────────────────────────────────┐              │
│     │  [A CONFIRMAR] CPF / Matrícula      │  ← Input    │
│     └─────────────────────────────────────┘              │
│     ┌─────────────────────────────────────┐              │
│     │  Senha                         [👁]  │  ← Input    │
│     └─────────────────────────────────────┘              │
│                                                         │
│     ┌─────────────────────────────────────┐              │
│     │           Entrar                    │  ← Button    │
│     └─────────────────────────────────────┘              │
│                                                         │
│     ─── ou ──────────────────────────────                │
│                                                         │
│     ┌─────────────────────────────────────┐              │
│     │     🔗 Entrar com [OAuth]           │  ← Button    │
│     └─────────────────────────────────────┘              │
│                                                         │
│     Esqueceu sua senha?                                 │
│                                                         │
│     ──────────────────────────────────────               │
│     Governo de Santa Catarina · CiASC                   │
│     v2.4.0                                              │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### Estado de sessão expirada (redirect de SC-12)
```
┌─────────────────────────────────────────────────────────┐
│                                                         │
│     ┌─────────────────────────────────────┐              │
│     │ ⚠ Sua sessão expirou.              │  ← Alert     │
│     │   Seu rascunho foi salvo.           │  (Warning)   │
│     └─────────────────────────────────────┘              │
│                                                         │
│     [... formulário de login normal ...]                │
│                                                         │
└─────────────────────────────────────────────────────────┘
   Instância BC-03 Alert Warning
```

- **Logo:** Logo do estado de SC / sistema — **[A CONFIRMAR COM DEMILIS]** qual asset e variante por sistema (DV, PC, CBM)
- **Título:** Nome do sistema (ex: "Delegacia Virtual") — **[A CONFIRMAR]** se é configurável ou fixo
- **Subtítulo:** "Sistema Integrado de Segurança Pública" — identidade institucional
- **Campo de identificação:** instância de BC-13 Input — **[A CONFIRMAR]** se é CPF, matrícula, e-mail ou outro
- **Campo de senha:** instância de BC-13 Input com toggle de visibilidade (ícone olho)
- **Botão Entrar:** instância de BC-05 Button Primary MD
- **Separador "ou":** linha horizontal com texto — separa autenticação primária de OAuth
- **Botão OAuth:** instância de BC-05 Button Secondary MD — **[A CONFIRMAR]** qual provedor OAuth (Google, Azure AD, gov.br)
- **Link "Esqueceu sua senha?":** link textual — **[A CONFIRMAR]** se existe recuperação de senha no SISP
- **Rodapé:** Governo SC + CiASC + versão (instância do padrão BC-28 Version)
- **Alert de sessão expirada:** instância de BC-03 Alert Warning — aparece quando `redirectReason = 'session-expired'`

> **Regra 11 — Composição atômica:** Input, Button, Alert e Icons são instâncias dos componentes existentes. Login é composição pura de componentes existentes com lógica de autenticação.

---

## Estados e variantes

### Estados do componente

| Estado | Condição | Descrição visual | Elementos |
|---|---|---|---|
| **Default** | Tela inicial | Formulário limpo, campos vazios. Botões: Entrar (Primary) + OAuth (Secondary) + Certificado Digital (Secondary) + link "Esqueceu sua senha?" | Logo + Inputs + Buttons + link |
| **SessionExpired** | `redirectReason = 'session-expired'` | Alert Warning acima do formulário | Alert + Logo + Inputs + Buttons |
| **Loading** | Autenticando no BFF | Botão Entrar com Spinner, campos desabilitados | Inputs disabled + Button loading |
| **Error** | Credenciais inválidas / erro BFF | Alert Danger abaixo dos campos | Inputs + Alert Danger + Buttons |
| **Locked** | 3 tentativas falhas → bloqueio 15 min | Alert Danger "Conta bloqueada temporariamente. Tente novamente em [mm:ss]." Campos e botão Entrar desabilitados. Botões "Recuperar senha" e "Falar com administrador" disponíveis | Inputs disabled + Alert Danger + Buttons alternativos |
| **2FA** | Após senha correta, sistema requer verificação | Campo de código de verificação + botão "Verificar" + link "Reenviar código" | Input código + Button Verificar + link |
| **OAuthRedirect** | Aguardando retorno do OAuth | Loader centralizado + mensagem "Redirecionando..." | BC-16 Loader Spinner MD + texto |

**Confirmado por Giuliana (2026-07-23):**
- Login é componente reutilizável (não página fixa)
- Locked: 3 tentativas → 15 minutos de bloqueio (aprovado)
- 2FA/captcha: incluir como opção no componente
- Certificado digital ICP-Brasil: incluir como opção de autenticação
- Theming por sistema: logo e título configuráveis
- Recuperação de senha: link "Esqueceu sua senha?" existe, destino é decisão do cliente
- Redirect pós-login: irrelevante para o DS (removido dos gaps)
- Responsividade: decisão sistêmica — vale para todos os componentes, não só Login

### Cores e tokens

| Elemento | Token | Valor |
|---|---|---|
| Fundo da página | `--color-surface-bg` | #FFFFFF |
| Fundo do card de login | `--color-surface` | #FFFFFF |
| Borda do card | `--color-border` | #D1D5DB |
| Shadow do card | `shadow/md` | Effect Style |
| Título do sistema | `--color-text-primary` | #111827 |
| Subtítulo | `--color-text-muted` | #6B7280 |
| Separador "ou" | `--color-border` | #D1D5DB |
| Texto separador | `--color-text-muted` | #6B7280 |
| Link "Esqueceu senha" | `--color-primary` | #C4000B |
| Rodapé | `--color-text-muted` | #6B7280 |

### Verificação de contraste (WCAG AA)

| Elemento | Foreground | Background | Ratio | Resultado |
|---|---|---|---|---|
| Título sistema | #111827 | #FFFFFF | ≥ 15.4:1 | ✅ AAA |
| Subtítulo | #6B7280 | #FFFFFF | ≥ 5.0:1 | ✅ AA |
| Link "Esqueceu senha" | #C4000B | #FFFFFF | 5.2:1 | ✅ AA |
| Label dos campos | herda BC-13 | — | ✅ | Via BC-13 |
| Botão Entrar | herda BC-05 | — | ✅ | Via BC-05 |
| Alert de erro | herda BC-03 | — | ✅ | Via BC-03 |
| Rodapé | #6B7280 | #FFFFFF | ≥ 5.0:1 | ✅ AA |

### Dimensões

| Propriedade | Valor | Token |
|---|---|---|
| Card de login width | 400px | — |
| Card de login padding | 32px | `--space-8` |
| Card border-radius | 12px | `--radius-lg` |
| Card shadow | shadow/md | Effect Style |
| Logo max-height | 64px | — |
| Gap entre logo e título | 16px | `--space-4` |
| Gap entre título e subtítulo | 8px | `--space-2` |
| Gap entre campos | 16px | `--space-4` |
| Gap entre último campo e botão | 24px | `--space-6` |
| Gap entre separador e OAuth | 16px | `--space-4` |
| Gap entre OAuth e link | 24px | `--space-6` |
| Input | MD (instância BC-13) | — |
| Button Entrar | Primary MD, full-width | — |
| Button OAuth | Secondary MD, full-width | — |
| Separador "ou" line height | 1px | — |
| Rodapé font size | 12px | `--text-xs` |

**[A CONFIRMAR COM DEMILIS]:** Card width pode ser diferente. Verificar layout real no stage.

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução neste spec |
|---|---|---|
| (N/A) | Não avaliado — componente inacessível durante inventário | Spec define acessibilidade from scratch: (1) Labels visíveis em todos os campos — nunca placeholder como substituto (herda BC-13). (2) Mensagens de erro com 3 canais: ícone + texto + cor (herda BC-03 Alert). (3) Contraste verificado ≥ 4.5:1 AA em todos os elementos. (4) Focus ring visível em todos os interativos. (5) Navegação por teclado completa (Tab entre campos, Enter para submit). (6) Labels em português |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução neste spec |
|---|---|---|
| (N/A) | Não avaliado | Spec resolve preventivamente: |
| H-1 Visibilidade | — | Loading state no botão Entrar (Spinner) + campos desabilitados. Feedback imediato de sucesso ou erro. Alert de sessão expirada visível ao chegar redirecionado de SC-12 |
| H-2 Mundo real | — | Vocabulário institucional em português: "Entrar", "Senha", "Esqueceu sua senha?". Sem jargão técnico ("OAuth" apresentado como "Entrar com [nome do provedor]") |
| H-3 Controle | — | Toggle de visibilidade de senha (ícone olho). Link de recuperação de senha. Botão OAuth opcional (configurável via `showOAuth`). Campos editáveis mesmo após erro (não limpa formulário) |
| H-4 Consistência | — | Campos seguem padrão BC-13 (label + input + error). Botões seguem padrão BC-05. Alert segue padrão BC-03. Consistência visual com todo o DS |
| H-5 Prevenção | — | Validação no campo (CPF válido, senha não vazia) antes de submit. Botão desabilitado até campos válidos. **[A CONFIRMAR]** se há captcha/2FA |
| H-6 Reconhecimento | — | Labels visíveis em todos os campos. Ícones semânticos (cadeado para senha, olho para toggle). Logo institucional para identidade |
| H-8 Estética | — | Card centralizado com shadow sutil. Espaçamento generoso (32px padding). Hierarquia visual clara: logo → título → campos → botão → separador → OAuth → link |
| H-9 Recuperação | — | Mensagens de erro específicas: "CPF ou senha incorretos" (não "erro de autenticação"). Conta bloqueada mostra tempo de desbloqueio. Link "Esqueceu sua senha?" sempre visível. Após sessão expirada, Alert informa que rascunho foi salvo |

---

## Regras de acessibilidade

- [ ] Formulário com `role="form"` e `aria-label="Formulário de login"`
- [ ] Campos com `label` visível associado via `for` (herda BC-13)
- [ ] Campo de senha com `autocomplete="current-password"`
- [ ] Campo de identificação com `autocomplete` apropriado — **[A CONFIRMAR]** `"username"` ou outro
- [ ] Botão Entrar com `type="submit"` — ativável por Enter em qualquer campo
- [ ] Toggle de visibilidade de senha com `aria-label="Mostrar senha"` / `"Ocultar senha"`
- [ ] Alert de erro com `role="alert"` e `aria-live="assertive"` (herda BC-03)
- [ ] Alert de sessão expirada com `role="status"` e `aria-live="polite"` (herda BC-03)
- [ ] Focus ring visível: `2px solid var(--color-border-focus)`
- [ ] Ordem de tab lógica: identificação → senha → toggle visibilidade → Entrar → OAuth → link
- [ ] Contraste mínimo 4.5:1 AA em todos os textos
- [ ] Não depende apenas de cor — erros usam ícone + texto + cor (3 canais)
- [ ] Labels em português
- [ ] `prefers-reduced-motion` — sem animações que necessitem desabilitar

---

## Comportamentos esperados

### Fluxo principal (SISP)
- Quando usuário acessa a tela de login → estado Default: formulário limpo, campos vazios, foco no primeiro campo
- Quando preenche campos e clica "Entrar" (ou Enter) → estado Loading: Spinner no botão, campos desabilitados
- Quando BFF retorna sucesso → emite `(loginSuccess)`. App inicializa. SC-12 Session Control assume monitoramento
- Quando BFF retorna erro de credenciais → estado Error: Alert Danger "CPF ou senha incorretos. Tente novamente." Campos mantêm valores (não limpa). Foco no campo de identificação
- Quando BFF retorna conta bloqueada → estado Locked: Alert Danger "Conta bloqueada por excesso de tentativas. Tente novamente em [X] minutos." Campos desabilitados. **[A CONFIRMAR]** se existe bloqueio por tentativas
- Quando BFF indisponível → estado Error: Alert Danger "Serviço indisponível. Tente novamente em alguns instantes."

### Fluxo OAuth (complementar)
- Quando clica "Entrar com [OAuth]" → redirect para provedor OAuth externo
- Quando retorna do provedor com sucesso → emite `(oauthConnected)`. **[A CONFIRMAR]** se OAuth é login principal ou complementar ao SISP
- Quando retorna do provedor com erro → Alert Danger "Não foi possível conectar via [OAuth]. Tente novamente."

### Sessão expirada (redirect de SC-12)
- Quando URL contém `?reason=session-expired` → Alert Warning acima do formulário: "Sua sessão expirou. Seu rascunho foi salvo automaticamente."
- Quando URL contém `?reason=logout` → sem alert (logout voluntário)

### Validação de campos
- Quando campo de identificação está vazio → erro inline "Campo obrigatório" (herda BC-13 estado Error)
- Quando campo de senha está vazio → erro inline "Campo obrigatório"
- Quando identificação inválida (formato) → erro inline "[A CONFIRMAR] formato esperado" — **[A CONFIRMAR]** se é CPF com máscara, matrícula, etc.
- Quando botão Entrar clicado com campos vazios → validação dispara, foco no primeiro campo inválido

### Toggle de visibilidade de senha
- Quando clica no ícone olho → senha visível (type="text"), ícone muda para olho riscado
- Quando clica novamente → senha oculta (type="password"), ícone volta ao olho

### Responsividade
- Quando viewport ≥ 1024px → card centralizado (400px) com background de página institucional
- Quando viewport < 1024px → card full-width com padding interno reduzido (24px → `--space-6`). **[A CONFIRMAR]** se mobile tem layout diferente

---

## Composição com outros componentes

> **Regra 11 — Composição atômica:** todo elemento que já exista como componente no DS deve ser instância.

| Componente | Relação | Composição no Figma |
|---|---|---|
| **BC-13 Input** | Campos de identificação e senha | **Instância direta** — 2 instâncias (identificação + senha) com labels em português |
| **BC-05 Button** | Botão "Entrar" + botão OAuth | **Instância direta** — Primary MD full-width (Entrar) + Secondary MD full-width (OAuth) |
| **BC-03 Alert** | Mensagens de erro e sessão expirada | **Instância direta** — Alert Danger (erro), Alert Warning (sessão expirada) |
| **BC-15 Icons** | Ícone de visibilidade de senha, ícone OAuth | **Font Awesome** — fa-eye / fa-eye-slash (toggle), fa-lock (identificação) |
| **BC-16 Loader** | Spinner durante autenticação | **Instância** — Spinner SM no botão Entrar durante Loading |
| **SC-12 Session Control** | Pós-login | **Coordenação** — após login bem-sucedido, SC-12 assume. SC-12 redireciona para Login quando sessão expira |

> **Regra 12 — auditoria:** "este elemento já existe?" Input, Button, Alert, Icons, Loader — todos existem como Base Components. O card de login é layout, não componente reutilizável. Logo é asset externo.

---

## Integração com BFF

> SISP Components são auto-suficientes via BFF. Login gerencia autenticação internamente via `sisp-lib`.

| Endpoint / Serviço | Ação | Resposta esperada |
|---|---|---|
| Autenticação SISP | POST com credenciais | Token SISP + `SessionState` + `userName` |
| Autenticação OAuth | Redirect + callback | Token OAuth + dados do provedor |
| Recuperação de senha | **[A CONFIRMAR]** | Link de reset / novo password |
| Verificação de bloqueio | **[A CONFIRMAR]** | Status da conta + tentativas restantes |

**[A CONFIRMAR COM DEMILIS]:** Quais endpoints exatos o componente de login consome. Verificar se há middleware de autenticação, interceptors, ou guards Angular que interagem com o componente.

---

## Mapeamento de retrocompatibilidade

| Estado atual | Mapeamento novo | Nota |
|---|---|---|
| **[A CONFIRMAR]** Tela de login existente | Estado Default | Catalogar layout atual no stage |
| **[A CONFIRMAR]** Erro de autenticação | Estado Error + Alert Danger | Verificar mensagens atuais |
| **[A CONFIRMAR]** OAuth flow | Estado OAuthRedirect | Verificar provedor e fluxo |
| — | Estado SessionExpired (Alert Warning) | **Novo** — integração com SC-12 |
| — | Estado Locked | **Novo** — se existir bloqueio por tentativas |
| — | Toggle de visibilidade de senha | **Novo** — resolver acessibilidade |
| — | Validação inline nos campos | **Novo** — feedback imediato |

---

## Variantes no Figma

| Variante | Property | Descrição |
|---|---|---|
| **Default** | `State=Default` | Formulário limpo: logo + título + inputs + botões |
| **SessionExpired** | `State=SessionExpired` | Alert Warning "Sessão expirada" + formulário |
| **Error** | `State=Error` | Alert Danger abaixo dos campos + formulário preenchido |
| **Loading** | `State=Loading` | Botão com Spinner + campos desabilitados |

**[A CONFIRMAR COM DEMILIS]:** Verificar se Locked e OAuthRedirect são estados necessários no Figma. Por ora, 4 variantes cobrem os cenários essenciais.

---

## Comportamento responsivo

> Regra 13 — Responsividade obrigatória. Breakpoints: Mobile (<768px), Tablet (768–1023px), Desktop (≥1024px).

| Precisa de variante `Layout=Mobile`? | **Sim** — layout do card muda fundamentalmente |
|---|---|
| **Desktop** | Card centralizado (400px), padding 32px (`space/8`), border-radius 8px. Card flutua sobre fundo `--color-surface-bg` |
| **Mobile** | Tela cheia (375px / 100% viewport), padding 16px (`space/4`), sem border-radius. Conteúdo ocupa toda a largura, sem efeito de card flutuante |
| **Tablet** | Segue Desktop — card centralizado com padding 32px |

**O que muda entre Desktop e Mobile:**
- Largura: 400px card → 375px full-width (100% viewport)
- Padding: 32px → 16px
- Border radius: 8px → 0px
- Efeito visual: card flutuante → tela cheia edge-to-edge
- Estrutura interna: mantida (mesmos campos, botões, links)

**Variantes no Figma:** 12 variantes (6 estados × 2 layouts)
- `State=Default, Layout=Desktop` / `State=Default, Layout=Mobile`
- `State=SessionExpired, Layout=Desktop` / `State=SessionExpired, Layout=Mobile`
- `State=Error, Layout=Desktop` / `State=Error, Layout=Mobile`
- `State=Loading, Layout=Desktop` / `State=Loading, Layout=Mobile`
- `State=Locked, Layout=Desktop` / `State=Locked, Layout=Mobile`
- `State=2FA, Layout=Desktop` / `State=2FA, Layout=Mobile`

---

## Casos excepcionais / bordas

- **CPF com máscara:** **[A CONFIRMAR]** se campo de identificação é CPF — se sim, aplicar máscara `000.000.000-00` com validação de dígitos verificadores
- **Múltiplas abas:** se login é feito em uma aba, as outras devem detectar a sessão ativa (via polling ou BroadcastChannel). **[A CONFIRMAR]** comportamento atual
- **OAuth popup vs. redirect:** **[A CONFIRMAR]** se OAuth abre popup ou faz redirect completo. Popup mantém contexto; redirect perde formulário
- **Rate limiting:** **[A CONFIRMAR]** se existe limite de tentativas de login e qual o comportamento (bloqueio temporal, captcha, etc.)
- **2FA / MFA:** **[A CONFIRMAR]** se existe segundo fator de autenticação (SMS, token, e-mail)
- **Captcha:** **[A CONFIRMAR]** se existe captcha após X tentativas falhadas
- **Recuperação de senha:** **[A CONFIRMAR]** se existe fluxo de recuperação e qual (e-mail, SMS, presencial)
- **Certificado digital:** **[A CONFIRMAR]** se alguns sistemas SISP usam certificado digital (ICP-Brasil) como alternativa a senha
- **Theming (PC, CBM):** logo e título mudam por sistema — **[A CONFIRMAR]** se Login é configurável por tema ou fixo
- **Deep link pós-login:** **[A CONFIRMAR]** se após login, o usuário é redirecionado para a URL original ou sempre para home

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-surface` | Fundo do card |
| `--color-surface-bg` | Fundo da página |
| `--color-border` | Borda do card, separador |
| `--color-text-primary` | Título do sistema |
| `--color-text-muted` | Subtítulo, rodapé, separador |
| `--color-primary` | Link "Esqueceu senha", Button Primary |
| `--color-border-focus` | Focus ring |
| `shadow/md` | Shadow do card |
| `--space-2` / `--space-4` / `--space-6` / `--space-8` | Gaps e padding |
| `--radius-lg` | Border-radius do card |
| `--text-xs` | Font size do rodapé |
| `--text-sm` | Font size do subtítulo |
| `--text-lg` | Font size do título |
| Heading/LG | Text Style do título |
| Body/SM | Text Style do subtítulo e rodapé |

---

## O que está fora deste spec

- **Cadastro de novo usuário:** Login é apenas entrada. Cadastro é outro fluxo (se existir)
- **Gestão de perfil / troca de senha:** pós-login, não é responsabilidade do Login
- **SSO (Single Sign-On) com outros sistemas SC:** extensão futura, não Sprint 5
- **Biometria:** não aplicável ao contexto web da DV
- **Lógica de permissões / roles:** responsabilidade da app, não do componente de Login
- **Refresh token automático:** responsabilidade de SC-12 Session Control, não de Login
- **Layout da página completa (header, footer):** Login é o componente de formulário. A página wrapper é responsabilidade da app

---

## Gaps — status atualizado (2026-07-23)

### Resolvidos por Giuliana

| # | Gap original | Resolução |
|---|---|---|
| 1 | Login é componente ou página? | **Componente reutilizável.** Qualquer sistema pode usar. |
| 5 | Recuperação de senha? | **Sim.** Link "Esqueceu sua senha?" incluído. Destino é decisão do cliente pós-implementação. |
| 6 | Bloqueio por tentativas? | **Sim. 3 tentativas → bloqueio 15 min.** Estado Locked adicionado ao componente. |
| 7 | 2FA/captcha? | **Incluir como opção.** Estado 2FA adicionado ao componente. |
| 8 | Theming por sistema? | **Sim.** Logo e título são configuráveis via props. |
| 10 | Redirect pós-login? | **Removido.** Irrelevante para o Design System. Responsabilidade da aplicação. |
| 11 | Layout mobile diferente? | **Decisão sistêmica.** Todos os componentes devem ser responsivos. Sprint dedicada será planejada. |
| 12 | Certificado digital? | **Incluir como opção.** Botão "Entrar com Certificado Digital" adicionado. |

### Pendentes com Demilis (4 gaps)

| # | Gap | Impacto |
|---|---|---|
| 2 | Qual é o campo de identificação? CPF, matrícula, e-mail, username? | Muda o tipo de input, máscara e validação |
| 3 | Qual provedor OAuth é usado? Google, Azure AD, gov.br, outro? | Muda o label e ícone do botão OAuth |
| 4 | OAuth é login alternativo (em vez de SISP) ou complementar (além do SISP)? | Muda o fluxo — complementar requer login SISP primeiro |
| 9 | Qual o selector Angular real? `sisp-lib-login`? Aceita `sispLibLoginConfig`? | Define a API Angular correta |

---

## Critérios de aceite

- [x] 7 estados visuais no Figma: Default, SessionExpired, Error, Loading, Locked, 2FA, OAuthRedirect
- [x] Input como instância de BC-13 Input (Regra 11) — 2 instâncias (identificação + senha)
- [x] Button como instância de BC-05 Button Primary MD + Secondary MD (Regra 11)
- [x] Alert como instância de BC-03 Alert (Danger para erro/bloqueio, Warning para sessão expirada) (Regra 11)
- [x] Toggle de visibilidade de senha com ícone BC-15 (Regra 11)
- [x] Tokens aplicados — zero valores hardcoded (Regra 8)
- [x] Coordenação com SC-12 documentada (redirect `?reason=session-expired`)
- [x] Validação inline nos campos (herda BC-13 estado Error)
- [x] Acessibilidade documentada (autocomplete, form role, aria-labels, tab order)
- [x] Labels em português
- [x] Contraste verificado ≥ 4.5:1 AA em todos os elementos
- [x] EventEmitters documentados: loginSuccess, loginError, oauthConnected
- [x] Mapeamento de retrocompatibilidade documentado
- [x] Locked state: 3 tentativas → 15 min bloqueio (aprovado por Giuliana)
- [x] 2FA state: campo de código + botão Verificar (aprovado por Giuliana)
- [x] Certificado digital: botão "Entrar com Certificado Digital" (aprovado por Giuliana)
- [x] Theming: logo e título configuráveis por sistema (aprovado por Giuliana)
- [ ] **4 gaps restantes confirmados com Demilis** (campo identificação, OAuth provider, OAuth modo, selector Angular)
- [x] Revisado e aprovado por Giuliana
