# CLAUDE.md — DS SISP

> Contrato do sistema para agentes AI.
> Define quem tu és neste projeto, como o agente deve se comportar, e qual o estado atual do ciclo.

**Sistema:** GiuOS v1.0 — adaptado para DS SISP  
**Última atualização:** 2026-08-04  
**Ciclo atual:** building  
**Fase atual do Diamond:** D2-deliver  

---

## Quem é Giuliana

Giuliana é Lead UX Designer e Design Engineer com mais de 10 anos de experiência. Ela é a **dona do produto**, a **PM**, e a **Lead UX** deste projeto. O agente é seu co-designer — raciocina junto, não no lugar dela.

Ela não quer um agente que execute em silêncio. Quer um agente que verbalize o raciocínio, use o vocabulário metodológico correto, e espere aprovação em cada transição de fase.

---

## Contexto do projeto

O Design System SISP é um redesign completo do UI Kit Angular existente do CiASC/SC. O projeto passou por D1 completo (discovery + pesquisa qualitativa + análises). Estamos em D2, fase de construção.

**O entregável real são componentes Angular (`sisp-lib-[nome]`) documentados.**  
O Figma é a ferramenta de design (via MCP). HTML é usado apenas para entregáveis intermediários ao cliente (sitemap, user journey, wireframes de validação).

**Pipeline de um componente:**
```
component-spec.md (aprovada por Giuliana)
    → Figma (via Figma MCP — criação/redesign do componente)
    → Aprovação visual de Giuliana
    → Angular refactoring (sisp-lib-[nome])
    → Entrega
```

---

## Metodologias ativas

| Metodologia | Quando ativa | Para quê |
|---|---|---|
| **Double Diamond** | Todo o ciclo | Frame macro — estamos em D2-deliver |
| **Shape Up** | D2 — Develop e Deliver | Sprints com apetite definido |
| **Spec-Driven Development** | Antes de qualquer componente no Figma | component-spec.md aprovada antes de criar no Figma |
| **Service Design** | Sitemap + User Journey | Documentação de como o DS é usado como produto |

---

## Regras não negociáveis

1. **Nunca criar componente no Figma sem component-spec aprovada por Giuliana.**
2. **Nunca refatorar Angular sem o componente Figma aprovado.**
3. **Sempre verbalizar o raciocínio antes de agir.**
4. **Nunca inventar dados de análise** — as violações WCAG e Nielsen estão nos docs de análise. Recuperar antes de especificar.
5. **Cada component-spec resolve violações WCAG AA E heurísticas Nielsen** — não só contraste.
6. **Manter session-log/INDEX.md** atualizado com decisões materiais.
7. **Design-to-code via Figma MCP** — para qualquer componente, verificar/criar referência no Figma antes de gerar Angular.
8. **Tokens primeiro** — nenhum componente usa valor hardcoded. Tudo via tokens CSS / variáveis Figma.
9. **Código modular** — arquivos abaixo de 200 linhas onde prático.
10. **DV primeiro** — Delegacia Virtual é o produto-âncora. Componentes da DV têm prioridade.
11. **Composição atômica — nunca recriar o que já existe.** Todo elemento visual dentro de um componente que já exista como componente no DS **deve ser uma instância** desse componente, nunca uma recriação manual. Checkbox dentro de Table → instância do Checkbox. Badge de status dentro de Table → instância do Badge. Botão dentro de Modal → instância do Button. Isso garante: (a) fonte única de verdade — mudança no componente base propaga para todos; (b) consistência visual automática; (c) manutenção centralizada.
12. **Auditoria de componentes antes de criar.** Antes de especificar ou criar qualquer novo componente, **auditar o inventário de componentes existentes** para identificar elementos reutilizáveis — não apenas visuais idênticos, mas padrões funcionais equivalentes. Se um elemento novo tem o mesmo comportamento de um componente existente (ex: nav items no Header = Tabs; lista de opções em Dropdown = Select), usar o componente existente como instância ou base. A pergunta obrigatória é: "este elemento já existe como componente, mesmo que com outro nome ou em outro contexto?" Isso garante escalabilidade do DS e reduz a superfície de manutenção.
13. **Responsividade obrigatória.** Todo componente com layout complexo deve ter variante `Layout=Mobile` no Figma. Breakpoints: Mobile (<768px), Tablet (768–1023px), Desktop (≥1024px) — alinhado com Bootstrap. Componentes auto-contidos (width 100% ou <320px) não precisam de variante responsiva. Nomenclatura: propriedade `Layout` com valores `Desktop` e `Mobile`. Tablet segue Desktop com ajustes de padding via tokens responsivos. Referência completa: `.claude/rules/responsiveness.md`.
14. **Layouts usam apenas instâncias e estilos existentes.** Ao criar layouts de tela (Sprint 6+), cada elemento visual **deve ser uma instância** de um Component Set existente no Figma. Proibido: criar componentes novos ad-hoc, criar text styles novos, criar color styles novos, trocar fontes (Arial/Arimo permanecem como estão nos componentes). Os layouts são composições dos 46 Component Sets — não superfícies de criação de novos elementos. Variáveis Figma existentes (Colors, Typography, Spacing, Border Radius) são as únicas permitidas para binding.
15. **Text Styles obrigatórios.** Todo text node dentro de um Component Set deve ter um text style aplicado (dos 21 existentes: Heading, Overline, Body, Label, Mono). Exceções: ícones decorativos Font Awesome. Após criar/modificar qualquer Component Set, auditar text nodes para 100% compliance. Color fills de text bound a variáveis de Cores Semânticas.

---

## Stack ativa

```yaml
stack-pack: figma-angular-ds
framework: Angular (monorrepo — sisp-lib)
css: Bootstrap (utilitários) + tokens CSS customizados
icons: Font Awesome Free
design-tool: Figma
figma-mcp: disponível
figma-file-key: YUSNqTRVZTK2eV7D3fXypx
component-convention: sisp-lib-[nome]
config-convention: sispLib[Nome]Config
max-layout-width: 1200px
stage-url: sisp-design-system-stage.apps.okd4.ciasc.sc.gov.br
```

---

## Projeto atual

```yaml
project-name: DS SISP — Design System CiASC/SC
client: CiASC / COSEG / Governo de Santa Catarina
problem-statement: >
  O UI Kit do SISP existe mas não tem mandato institucional, não tem
  documentação formal, e falha quando terceiros precisam contribuir.
  O DS precisa se tornar um produto real com governança, tokens,
  componentes documentados e um processo de adoção claro.
core-personas:
  - Demilis (criador/mantenedor — gatekeeper de front-end)
  - Devs CiASC (6 servidores — consumidores)
  - Sommer/Holiwod (POs — visibilidade)
  - Terceiros contratados (sem contexto histórico — risco real)
  - Devs PC (clientes externos com sistemas próprios)
anchor-product: Delegacia Virtual (DV) — 90% migrada para o UI Kit
current-cycle: building
current-sprint: Sprint 7 — SISP Components Consultas + Dados
prd-status: approved
spec-status: complete (47 specs — Sprint 3: 6, Sprint 4: 7, Sprint 4.1: 13, Sprint 5: 6 [5 aprovadas + 1 com gaps aprovada], Sprint 6: 8 DC + 1 SC, Sprint 7: 8 SC aprovadas)
figma-file-key: YUSNqTRVZTK2eV7D3fXypx
figma-component-sets: 56 (Sprint 3: 10, Sprint 4: 8, Sprint 4.1: 13, Sprint 5: 6, Sprint 6: 10 DC + 1 SC, Sprint 7: 8 SC)
figma-pages: 12 (Wireframes, Sitemap, User Journeys, Validação Visual, Fundação, Taxonomia, Componentes, SISP Components, Pendências, Layouts DV, Componentes Portal, Layouts DS Portal)
figma-variables: 108 (4 coleções — Colors, Typography, Spacing, Border Radius + 4 tokens responsivos + 5 tokens D141: text-2xs, 4× dark-mode)
figma-text-styles: 21 (Heading, Overline, Body, Label, Mono — Overline adicionado D142)
figma-bindings: 4366 (Sprint 3–5: 332 fontSize + 1805 spacing + 177 borderRadius + 157 color fills | Sprint 7: 427 textStyles + 490 spacing + 432 borderRadius + 546 color fills)
```

---

## 6 decisões de design — já resolvidas

| # | Decisão | Posição |
|---|---|---|
| 1 | Cor de ação primária | Vermelho SC #C4000B (contraste 5.2:1 ✅ AA) |
| 2 | Tipografia | Montserrat (portal DS) + Arial (componentes Angular) |
| 3 | Aderência ao Guia SC | Adaptada — segue paleta SC, corrige tokens que reprovam WCAG |
| 4 | Conformidade WCAG | AA completo — mandatório |
| 5 | Idioma | Português — variantes PT/EN/ES documentadas onde necessário |
| 6 | Prioridade de componentização | DV primeiro |

> Estas decisões serão comunicadas ao cliente via artefatos visuais, não como documento de opções.

---

## Estrutura do DS SISP como produto (7 seções)

| # | Seção | Conteúdo |
|---|---|---|
| 01 | Sobre o DS | O que é, quem mantém, como usar, como contribuir, changelog |
| 02 | Fundação | Tokens de cor, tipografia, espaçamento, border-radius, iconografia |
| 03 | Acessibilidade | WCAG AA por tipo de componente, contraste por token |
| 04 | Base Components | 27 componentes — props, estados, exemplo Angular, notas de acessibilidade |
| 05 | SISP Components | 16 componentes específicos — mesma estrutura |
| 06 | Temas | Override de tokens por cliente (PC, PM, CBM) |
| 07 | Figma | UI Kit, Dev Mode, mapa tokens Figma ↔ CSS |

---

## Sprint plan atual

```
Sprint 0    ✅ → Decisões visuais + tokens CSS definidos
Sprint 1    ✅ → HTML entregáveis: sitemap + user journey + wireframes-chave
Sprint 2    ✅ → Figma: fundação (tokens + style guide + taxonomia)
Sprint 3    ✅ → Figma: Base Components núcleo (Buttons, Cards, Forms, Alerts, Badges, Toasts) — 10 Component Sets, 113 variantes
Sprint 4    ✅ → Figma: Base Components complemento (Tabs, Tables, Modals, Headers, Loaders, Dropdowns, Icons) — 8 Component Sets
Sprint 4.1  ✅ → Figma: Base Components restantes — 13 componentes
Sprint 5    ✅ → Figma: SISP Components DV — 6 Component Sets, 32 specs, auditoria completa
Sprint R1   ✅ → Responsividade: variantes Layout=Mobile para 18 componentes + tokens responsivos
Sprint 6    ⏳ → Layouts DV Core + DS Portal — telas DV (Login ✅, Manutenção ✅, Layout-Frame ✅) + telas portal (WF-02 Página de Componente ✅ — 3 DC Components + Desktop/Mobile, WF-01 Home do Portal ✅ — 3 DC Components + Desktop/Mobile, WF-03 Página de Fundação ✅ — Desktop 1440×2826 + Mobile 375×2244) + telas DV restantes (Dashboard, Lista BOs, Criação BO, Detalhes BO, Notificações, Sessão)
Sprint 7    ✅ → SISP Components Consultas + Dados — 8 specs aprovadas + 8 Component Sets Figma (SC-01, SC-02, SC-03, SC-04, SC-06, SC-09, SC-11, SC-14 — 30 variantes, 100% bindings auditados). 2 bloqueadas (SC-05 inacessível, SC-16 não existe). Taxonomia atualizada
Sprint 8    ⏳ → Protótipo navegável — flows interativos no Figma (Login → Dashboard → Criar BO → Consultar → Detalhes)
Sprint 9    ⏳ → Testes de usabilidade remotos
Sprint 10   ⏳ → Refatoração Angular (sisp-lib-[nome])
```

---

## Referências críticas — recuperar ao iniciar Figma

- `docs/analyses/wcag-analysis.md` — violações WCAG por componente
- `docs/analyses/nielsen-analysis.md` — violações heurísticas por componente
- `design-tokens/tokens.css` — sistema de tokens definido
- Stage DS SISP: `sisp-design-system-stage.apps.okd4.ciasc.sc.gov.br`
- Guia de Padronização SC v1.4 (Novembro 2023)

> ⚠️ Cada component-spec DEVE referenciar as violações WCAG e Nielsen do componente correspondente. Não criar spec sem consultar os docs de análise.

---

## Inventário de Component Sets no Figma

### Sprint 3 — Base Components núcleo (10 sets, 113 variantes)

| ID | Component Set | Figma Node ID | Variantes |
|---|---|---|---|
| BC-05 | Buttons | 116:1862 | 36 (4 Types × 3 Sizes × 3 States) |
| BC-06 | Cards | 118:2041 | 20 (5 Status × 2 States × 2 Layouts: Desktop/Mobile — Mobile 343px) |
| BC-13 | Input | 118:2232 | 6 estados |
| BC-13 | Select | 118:2304 | 5 estados |
| BC-13 | Textarea | 118:2352 | 6 estados |
| BC-13 | Checkbox | 118:2247 | 4 variantes |
| BC-13 | Radio | 118:2262 | 4 variantes |
| BC-03 | Alerts | 118:2456 | 8 (4 Types × 2 Dismissible) |
| BC-04 | Badges | 124:2690 | 30 (5 Types × 2 Styles × 3 Sizes) |
| BC-27 | Toasts | 124:2862 | 4 (4 Types) |

### Sprint 4 — Base Components complemento (8 sets)

| ID | Component Set | Figma Node ID | Variantes |
|---|---|---|---|
| BC-26 | Tab Item | 129:238 | 6 (2 Styles × 3 States) |
| BC-26 | Tabs | 135:222 | 4 (2 Styles × 2 Layouts: Underline/Contained × Desktop/Mobile) |
| BC-25 | Tables | 141:249 | 8 (4 States × 2 Layouts: Default/Selectable/Empty/Loading × Desktop/Mobile) |
| BC-19 | Modals | 157:384 | 6 (3 Types × 2 Layouts: Default/Confirmation/Confirmation Danger × Desktop/Mobile) |
| BC-14 | Headers | 175:432 | 2 (Desktop, Mobile) |
| BC-16 | Loaders | 177:484 | 5 (Spinner SM/MD/LG, Bar Indeterminate/Determinate) |
| BC-10 | Dropdowns | 188:492 | 4 (2 States × 2 Layouts: Closed/Open × Desktop/Mobile) |
| BC-15 | Icons | 223:516 | 5 (XS, SM, MD, LG, XL) |

### Sprint 4.1 — Base Components restantes — Batch B (5 sets)

| ID | Component Set | Figma Node ID | Variantes |
|---|---|---|---|
| BC-12 | Footer | 264:529 | 2 (Desktop, Mobile) |
| BC-23 | Popover | 266:536 | 2 (Title=Yes, Title=No) |
| BC-24 | Route Selector | 268:552 | 4 (2 Styles × 2 Layouts: Underline/Contained × Desktop/Mobile — Mobile 280px clipsContent) |
| BC-11 | File Preview | 272:573 | 2 (Preview=Image, Preview=Icon) |
| BC-20 | Navigation Canvas | 275:590 | 3 (2 Modes × Desktop + Expanded Mobile: Expanded/Collapsed Desktop + Expanded Mobile drawer) |

### Sprint 4.1 — Base Components restantes — Batch D (6 sets)

| ID | Component Set | Figma Node ID | Variantes |
|---|---|---|---|
| BC-28 | Version | 315:655 | 1 (Layout=Default) |
| BC-01 | About | 315:671 | 2 (State=Filled × 2 Layouts: Desktop/Mobile — Mobile 343px) |
| BC-17 | Maintenance | 315:692 | 2 (State=Active × 2 Layouts: Desktop/Mobile — Mobile 375px) |
| BC-02 | Accordion | 315:747 | 2 (State=Expanded, State=Collapsed) |
| BC-22 | Offcanvas | 315:792 | 4 (2 Positions × 2 Layouts: End/Start × Desktop/Mobile — Mobile 375px full-screen) |
| BC-07 | Carousels | 315:820 | 2 (State=Default × 2 Layouts: Desktop/Mobile — Mobile 343px) |

### Sprint 4.1 — Base Components restantes — Batch E (2 sets)

| ID | Component Set | Figma Node ID | Variantes |
|---|---|---|---|
| BC-18 | Skeleton Layers | 323:829 | 3 (Type=Text, Type=Circle, Type=Rect) |
| BC-21 | Objects | 323:851 | 1 (Layout=Grid) |

### Sprint 5 — SISP Components DV — Batch B (3 sets)

| ID | Component Set | Figma Node ID | Variantes |
|---|---|---|---|
| SC-12 | Session Control | 325:1070 | 10 (5 States × 2 Layouts: Active/Warning/Critical/Expired/OAuth-Inactive × Desktop/Mobile — Mobile 375px) |
| SC-10 | Notificações | 328:1294 | 4 (Populated, Empty, Loading, Error) |
| SC-13 | Steppers | 330:1650 | 7 (6 Desktop: 5 Horizontal + 1 Vertical + 1 Mobile: Vertical 343px) |

### Sprint 5 — SISP Components DV — Batch D (2 sets)

| ID | Component Set | Figma Node ID | Variantes |
|---|---|---|---|
| SC-15 | Uploaders | 362:1179 | 10 (5 States × 2 Layouts: Default/DragOver/WithFiles/Complete/Disabled × Desktop/Mobile — Mobile 343px) |
| SC-07 | Image Captures | 367:1261 | 10 (5 States × 2 Layouts: Default/CameraActive/Preview/PermissionDenied/DeviceError × Desktop/Mobile — Mobile 343px) |

### Sprint 5 — SISP Components DV — Batch E (1 set)

| ID | Component Set | Figma Node ID | Variantes |
|---|---|---|---|
| SC-08 | Login | 373:1422 | 12 (6 States × 2 Layouts: Default/SessionExpired/Error/Loading/Locked/2FA × Desktop/Mobile) — 4 gaps pendentes com Demilis |
| SC-17 | Login SISP | 795:5694 | 4 (2 States × 2 Layouts: Default/Loading × Desktop/Mobile) — OAuth redirect, sem campos de formulário |

### Sprint 7 — SISP Components Consultas + Dados (8 sets)

| ID | Component Set | Figma Node ID | Variantes |
|---|---|---|---|
| SC-02 | Consultar Pessoa | 804:6190 | 4 (2 States × 2 Layouts: Default/Loading × Desktop + Error Desktop) — Query Form pattern, BC-26 Tabs + BC-13 + BC-05 |
| SC-03 | Consultar Registro | 804:6312 | 4 (2 States × 2 Layouts: Default/Loading × Desktop + Error Desktop) — Query Form, auto-suficiente BFF |
| SC-04 | Consultar Veículo | 804:6390 | 3 (State=Default/Loading/Error) — 4 tabs, 1 campo por aba, sem variante Mobile |
| SC-06 | Pesquisa Textual | 804:6561 | 4 (2 States × 2 Layouts: Default/Loading × Desktop + Error Desktop) — sem tabs, checkbox fonetizada |
| SC-09 | Logradouros | 804:6939 | 6 (3 States × 2 Layouts: CEP/Logradouro × Desktop/Mobile + Results + Error) — cascading selects |
| SC-01 | Atualizações Recentes | 804:6991 | 3 (State=Default/Empty/Loading) — tabela 2 colunas, paginação, sem Mobile |
| SC-11 | Resource Trees | 804:7121 | 3 (State=Default/Collapsed × Desktop + Mobile) — grid cards com badges status |
| SC-14 | Timelines | 804:7204 | 3 (State=Default/Collapsed/Empty) — timeline vertical, date-pills BC-04 Badge |

> **Seção Figma:** "SISP Components" (325:1010), sub-seções "CONSULTAS POLICIAIS" (804:6053) e "DADOS & VISUALIZAÇÃO" (804:6054)

### Sprint 6 — Doc Components (10 sets — portal DS only, não viram Angular)

| ID | Component Set | Figma Node ID | Variantes |
|---|---|---|---|
| DC-01 | Breadcrumb | 498:6 | 1 (State=Default) — auto-layout horizontal, gap=space/2, Body/SM |
| DC-02 | Page TOC | 499:17 | 1 (State=Default) — sidebar 200px, item ativo com borda 3px primary/base |
| DC-03 | CodeBlock | 500:9 | 1 (State=Default) — fundo dark (text/primary + dark/surface), Mono/SM, radius/lg |
| DC-04 | Section Card | 519:227 | 1 (State=Default) — card navegação seções, padding=space/4, gap=space/3, instância BC-15 Icons LG |
| DC-05 | Persona Card | 520:229 | 1 (State=Default) — card pathway persona, padding=space/6, gap=space/3, ícone circular 40×40, instância BC-15 Icons LG |
| DC-06 | Component Card | 520:2154 | 1 (State=Default) — card preview componente, gap=space/2, clip content, preview FILL×80, instância BC-04 Badge |
| DC-07 | Header Portal | 547:1305 | 2 (State=Default Desktop 1440×48, State=Mobile 375×48) — barra dark/surface, "DS SISP" Heading/MD + 6 nav links + Search. Mobile: título + hamburger ≡ |
| DC-08 | Footer Portal | 547:1550 | 2 (Layout=Desktop 1440×363, Layout=Mobile 375×686) — Coluna 1: logo badge "DS" + "DS SISP" Heading/MD + descrição Body/XS. Colunas 2-4: Documentação (5 links), Recursos (5 links), Governança (4 links). Bottom: copyright CiASC + v1.0.0. Desktop 22 text nodes, Mobile 21 text nodes, 100% text styles. Reestruturado D149 para paridade com HTML |
| DC-09 | Page Tag | 579:4526 | 2 (Icon=No, Icon=Yes) — tag reutilizável para subpages/pathways, surface/bg-subtle, Body/XS/Regular, text/secondary. Icon=No: radius/sm, padding 2/8. Icon=Yes: radius/full, padding 4/8, instância BC-15 Icons XS |
| DC-10 | Callout Card | 584:5450 | 2 (Layout=Desktop 600×93, Layout=Mobile 343×213) — callout com borda esquerda 4px primary/base, fundo primary/muted, radius/lg. Desktop: horizontal, padding space/6, gap space/5. Mobile: vertical, padding space/4, gap space/3. Instância BC-15 Icons LG em container 40×40 primary/base. Title Heading/MD + Description Body/SM/Regular. 100% variable bindings, 100% text styles |

> **Página Figma:** "Componentes Portal" (496:2), section "COMPONENTES DOCUMENTAIS (DC)" (500:10)
> **Layout WF-01:** "Layouts DS Portal" (501:2), section "WF-01 · HOME DO PORTAL DS" (523:221) — Desktop 1440×1875 (523:222) + Mobile 375×3683 (528:340)
> **Layout WF-02:** "Layouts DS Portal" (501:2), section "WF-02 · PÁGINA DE COMPONENTE" (501:3) — Desktop 1440×1563 (502:2) + Mobile 375×1006 (508:145)
> **Layout WF-03:** "Layouts DS Portal" (501:2), section "WF-03 · PÁGINA DE FUNDAÇÃO" (530:466) — Desktop 1440×2826 (530:467) + Mobile 375×2244 (538:500)

### Variáveis Figma (4 coleções, 99 variáveis)

| Coleção | Variáveis | Collection ID |
|---|---|---|
| Typography | 13 (tamanho/2xs..4xl, peso/regular..bold) — inclui --text-2xs (10px) adicionado D141 | 106:33 |
| Spacing | 19 (space/0..24 primitivos + 4 responsivos: page-padding, section-gap, card-padding, modal-padding) — 2 modos: Desktop, Mobile | 106:52 |
| Border Radius | 6 (radius/none..full) | 106:67 |
| Colors | 26 semânticas + 33 primitivas (2 modos: SC, PC) — inclui 4 dark-mode adicionadas D141 | 106:75 |

### Text Styles (21 estilos — D142)

| Grupo | Família | Estilos |
|---|---|---|
| Heading | Montserrat | 4XL, 3XL, 2XL, XL, LG, MD, SM |
| **Overline** | **Montserrat** | **XS (12px, Semibold, uppercase, letter-spacing 0.1em) — aprovado D142** |
| Body | Arimo (→ swap Arial desktop) | LG, Base, SM, XS (Regular + Bold) |
| Label | Arimo (→ swap Arial desktop) | LG, MD, SM |
| Mono | Fira Code | MD, SM |

### Status de bindings (auditado 2026-07-16)

**Sprint 3+4 (18 Component Sets):**

| Tipo | Total | Compliance |
|---|---|---|
| fontSize | 332/332 | 100% |
| spacing (padding/gap) | 1805/1805 | 100% |
| borderRadius | 177/177 | 100% |
| Text Styles aplicados | 329/332 | 99.1% (3 exceções decorativas) |
| Composição atômica | 7/7 complexos | 100% (45 instâncias) |
| Fontes Arial | 0 | Arimo como proxy — swap manual desktop |

**Sprint 4.1 (13 Component Sets):**

| Tipo | Total | Compliance |
|---|---|---|
| fontSize | 59/70 | 84.3% (11 exceções decorativas: chevrons, setas, ×) |
| spacing (padding/gap) | 168/168 | 100% |
| borderRadius | 60/64 | 93.8% (4 exceções: Logo SC placeholder) |
| Text Styles aplicados | 55/70 | 78.6% (15 exceções decorativas: chevrons, setas, ×, toggles) |
| Correções aplicadas | 2 valores fora do token system: 13px→12px, 6px→8px (Nav Canvas) |

**Sprint 5 (6 Component Sets — auditado 2026-07-22):**

| Tipo | Total | Compliance |
|---|---|---|
| Color fills bound | 157/157 | 100% (SC-10: 35, SC-13: 120, SC-07: 2. SC-12/SC-08/SC-15: já limpos) |
| Text Styles aplicados | 174/198 | 88% (24 exceções: ícones Font Awesome — decorativos. Cobertura textual efetiva: 100%) |
| Composição atômica | 6/6 complexos | 100% (68 instâncias de BC) |
| Fontes corretas | 198/198 | 100% (60 Inter→Arimo corrigidos em SC-10) |
| Naming CS | 6/6 | 100% (sem prefixo SC-XX — padrão BC) |
| Estrutura página | 12 sections | 100% (6 pares Componente + Uso e Teste em 4 categorias) |
| Correções aplicadas | Button Login Loading stretched (78→40px), strokes CameraActive/Preview removidos, BC-15 Icons movido para página correta |

**Sprint 7 (8 Component Sets — auditado 2026-08-04):**

| Tipo | Total | Compliance |
|---|---|---|
| Text Styles aplicados | 427/427 | 100% |
| Spacing bound | 490/490 | 100% (correções: 6px→8px em SC-11, 1px→space/px em SC-09, 48px→space/12 em SC-14) |
| Border Radius bound | 432/432 | 100% (Circle indicators SC-14: radius/full) |
| Color fills bound | 546/546 | 100% (Icon text nodes bound a primary/base: SC-01×3, SC-11×24, SC-14×7, SC-06 Box→surface/base×4) |
| Composição atômica | 8/8 complexos | 100% (237 instâncias de BC: Tabs, Input, Select, Button, Alert, Badge, Icons, Spinner, Checkbox, Skeleton) |
| Variantes | 30 | 8 Component Sets × média 3.75 variantes |

---

## Estrutura de arquivos

```
ds-sisp/
├── CLAUDE.md                        ← este arquivo
├── START-HERE.md
├── WIZARD.md
│
├── .claude/
│   ├── rules/
│   │   ├── golden-rules.md
│   │   ├── methodology-map.md
│   │   ├── co-authorship.md
│   │   ├── design-to-code.md
│   │   └── documentation.md
│   ├── agents/
│   │   ├── component-spec-agent.md
│   │   └── html-builder-agent.md
│   └── commands/
│       ├── new-component.md
│       └── sprint-close.md
│
├── docs/
│   ├── DISCOVERY-SYNTHESIS.md
│   ├── PRD.md
│   ├── analyses/
│   │   ├── wcag-analysis.md
│   │   ├── nielsen-analysis.md
│   │   └── inventory.md
│   └── component-specs/
│       ├── _template.md
│       └── [BC-XX-nome.md por sprint]
│
├── design-tokens/
│   └── tokens.css
│
├── deliverables/
│   ├── sitemap.html
│   ├── user-journey.html
│   └── wireframes/
│
├── session-log/
│   └── INDEX.md
│
├── stack-packs/
│   └── figma-angular-ds/
│       └── README.md
│
└── src/
    └── [Angular — só após spec + Figma aprovados]
```
