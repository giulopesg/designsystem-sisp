---
source: ds-sisp-inventario.html
last-updated: 2026-06-22
total: 44
base-components: 28
sisp-components: 16
---

# Inventário de Componentes — DS SISP

44 componentes · 28 Base Components · 16 SISP Components  
Referência para criação de component specs na fase Figma.

**Recomendações:**
- **Manter** — funcional, aplicar tokens visuais
- **Refinar** — redesenhar resolvendo violações WCAG + Nielsen
- **Recriar** — não existe ou precisa ser construído do zero
- **A confirmar** — inacessível no inventário, catalogar antes de especificar

---

## Tabela de referência rápida

| ID | Nome | Tipo | Rec. | WCAG | Nielsen | Prioridade DV |
|---|---|---|---|---|---|---|
| BC-01 | About | Base | Manter | aten | aten | — |
| BC-02 | Accordions | Base | Refinar | aten | aten | — |
| BC-03 | Alerts | Base | Refinar | **crit** | **crit** | ✅ |
| BC-04 | Badges | Base | Refinar | **crit** | aten | ✅ |
| BC-05 | Buttons | Base | Refinar | aten | **crit** | ✅ |
| BC-06 | Cards | Base | Refinar | aten | aten | ✅ |
| BC-07 | Carousels | Base | Refinar | aten | **crit** | — |
| BC-08 | Charts | Base | Recriar | n/a | n/a | — |
| BC-09 | Confirmation Modals | Base | Refinar | aten | **crit** | ✅ |
| BC-10 | Dropdowns | Base | Manter | aten | aten | ✅ |
| BC-11 | File Previews | Base | Refinar | aten | aten | ✅ |
| BC-12 | Footers | Base | Refinar | ok | aten | ✅ |
| BC-13 | Forms | Base | Refinar | **crit** | **crit** | ✅ |
| BC-14 | Headers | Base | Refinar | aten | **crit** | ✅ |
| BC-15 | Icons | Base | Refinar | aten | **crit** | ✅ |
| BC-16 | Loaders | Base | Manter | aten | **crit** | ✅ |
| BC-17 | Maintenance | Base | Manter | ok | aten | — |
| BC-18 | Skeleton Layers | Base | A confirmar | n/a | n/a | — |
| BC-19 | Modals | Base | Refinar | aten | **crit** | ✅ |
| BC-20 | Navigation Canvas | Base | Refinar | aten | aten | ✅ |
| BC-21 | Objects | Base | A confirmar | ok | aten | — |
| BC-22 | Offcanvas | Base | Refinar | aten | **crit** | — |
| BC-23 | Popovers | Base | Manter | aten | aten | ✅ |
| BC-24 | Route Selectors | Base | Manter | aten | aten | ✅ |
| BC-25 | Tables | Base | Refinar | aten | **crit** | ✅ |
| BC-26 | Tabs | Base | Manter | **crit** | **crit** | ✅ |
| BC-27 | Toasts | Base | Manter | **crit** | **crit** | ✅ |
| BC-28 | Version | Base | Manter | ok | aten | — |
| SC-01 | Atualizações Recentes | SISP | Refinar | aten | aten | ✅ |
| SC-02 | Consultar Pessoa | SISP | Refinar | aten | **crit** | ✅ |
| SC-03 | Consultar Registro | SISP | Refinar | aten | **crit** | ✅ |
| SC-04 | Consultar Veículo | SISP | Refinar | aten | **crit** | ✅ |
| SC-05 | Pesquisa de Objetos | SISP | A confirmar | n/a | n/a | ✅ |
| SC-06 | Pesquisa Textual | SISP | Refinar | aten | aten | ✅ |
| SC-07 | Image Captures | SISP | Refinar | aten | aten | ✅ |
| SC-08 | Login | SISP | A confirmar | n/a | n/a | ✅ |
| SC-09 | Logradouros | SISP | Refinar | ok | **crit** | ✅ |
| SC-10 | Notificações | SISP | Refinar | **crit** | **crit** | ✅ |
| SC-11 | Resource Trees | SISP | Refinar | aten | aten | — |
| SC-12 | Session Control | SISP | Refinar | aten | **crit** | ✅ |
| SC-13 | Steppers | SISP | Refinar | **crit** | **crit** | ✅ |
| SC-14 | Timelines | SISP | Refinar | aten | aten | — |
| SC-15 | Uploaders | SISP | Refinar | aten | **crit** | ✅ |
| SC-16 | Relatório de Consultas | SISP | Recriar | n/a | n/a | ✅ |

---

## Base Components — detalhamento

### BC-01 · About
**Componente Angular:** `<sisp-lib-about [sispLibAboutConfig]="config">`  
**Recomendação:** Manter  
**Props:** `title` (string, opcional) · `htmlContent` (HTML string, opcional)  
**Estados:** Preenchido · Vazio → delega a `sisp-lib-not-found`  
**Notas:** Renderiza HTML via `[innerHTML]`. Fallback via `sisp-lib-not-found`. Lista interna "Principais Componentes" provavelmente desatualizada. Aplicar tokens de tipografia na refatoração.

---

### BC-02 · Accordions
**Componente Angular:** `<sisp-lib-accordion [sispLibAccordionConfig]="config">`  
**Recomendação:** Refinar  
**Props:** `content: [{title, body}]` · `closeOthers: boolean (def: false)` · `destroyOnHide: boolean (def: true)`  
**Estados:** Expandido · Recolhido · Desabilitado (a confirmar se oficial)  
**Notas:** Ícone ≡ esquerdo — visual ou drag? A confirmar com Demilis. Estado disabled não documentado formalmente.

---

### BC-03 · Alerts
**Componente Angular:** `<sisp-lib-alert [sispLibAlertConfig]="config">`  
**Recomendação:** Refinar  
**Props:** `type: SispLibStyleType` · `message: string` · `dismissible?: boolean`  
**Estados:** Primary · Secondary · Success · Warning · Danger · Dark · Info · Light  
**Notas:** `role="alert"` não documentado. Variante Light com contraste a verificar. 8 variantes diferenciadas só por cor.  
**⚠️ SispLibStyleType** compartilhado com BC-04, BC-05, BC-27 — qualquer mudança no enum afeta todos.

---

### BC-04 · Badges
**Componente Angular:** `<sisp-lib-badge [sispLibBadgeConfig]="config">`  
**Recomendação:** Refinar  
**Props:** `type: SispLibStyleType` · `label: string`  
**Estados:** 8 variantes de cor (mesmo enum de Alerts/Buttons/Toasts)  
**Notas:** Tamanhos (sm/md/lg) não documentados. Uso com ícone não documentado. Criar guia de uso semântico.

---

### BC-05 · Buttons
**Componente Angular:** `<sisp-lib-button [sispLibButtonConfig]="config">`  
**Recomendação:** Refinar  
**Props:** `type: SispLibStyleType` · `label: string` · `icon?: string (FA class)` · `disabled?: boolean` · `size?: string`  
**Estados:** Default · Hover · Active · Disabled · Loading (a confirmar)  
**Notas:** Componente mais crítico do DS. Visual 100% Bootstrap sem identidade SISP. Estado loading não documentado. Hierarquia primário/secundário/terciário não estabelecida.  
**⚠️ SispLibStyleType** compartilhado.

---

### BC-06 · Cards
**Componente Angular:** `<sisp-lib-card [sispLibCardConfig]="config">`  
**Recomendação:** Refinar  
**Props:** `title` · `subtitle` · `icon` · `closeButton: boolean` · `collapsible: boolean` · `footerButtons: []` · `customClass (CSS override)`  
**Estados:** Expandido · Colapsado · Com footer · Sem footer  
**Notas:** Componente mais importante para identidade visual. Header sempre h3 — sem hierarquia configurável. `customClass` abre espaço para inconsistências.

---

### BC-07 · Carousels
**Componente Angular:** `<sisp-lib-carousel [sispLibCarouselConfig]="config">`  
**Recomendação:** Refinar  
**Props:** `items: []` · `autoPlay?: boolean` · `interval?: number`  
**Estados:** Slide ativo · Transição · Indicadores de posição  
**Notas:** Keyboard nav e ARIA live region não mapeados. Comportamento touch/swipe não documentado.

---

### BC-08 · Charts
**Componente Angular:** a definir  
**Recomendação:** Recriar  
**Notas:** Componente não existe — marcado TODO. Biblioteca não escolhida (Chart.js? D3? Highcharts?). Definir com Demilis antes de especificar. **Não bloquear outros sprints.**

---

### BC-09 · Confirmation Modals
**Componente Angular:** `<sisp-lib-confirmation-modal [sispLibConfirmationModalConfig]="config">`  
**Recomendação:** Refinar  
**Props:** `title` · `message` · `confirmLabel` · `cancelLabel` · `onConfirm: Function` · `type?: SispLibStyleType`  
**Estados:** Aberto · Fechado  
**Notas:** Ação destrutiva sem destaque visual explícito. Demilis quer reduzir uso de modals full-screen na DV.

---

### BC-10 · Dropdowns
**Componente Angular:** `<sisp-lib-dropdown [sispLibDropdownConfig]="config">`  
**Recomendação:** Manter  
**Props:** `label` · `items: [{label, action}]` · `placement?`  
**Estados:** Fechado · Aberto · Item hover  
**Notas:** Funcional. Aplicar tokens na refatoração geral.

---

### BC-11 · File Previews
**Componente Angular:** `<sisp-lib-file-preview [sispLibFilePreviewConfig]="config">`  
**Recomendação:** Refinar  
**Props:** `file: {name, url, type, size}` · `downloadable?: boolean`  
**Estados:** Preview disponível · Preview indisponível (fallback ícone)  
**Notas:** Integração com SC-15 Uploaders não documentada. Tipos de arquivo suportados não listados. Criar spec do par Upload→Preview.

---

### BC-12 · Footers
**Componente Angular:** `<sisp-lib-footer [sispLibFooterConfig]="config">`  
**Recomendação:** Refinar  
**Props:** `links?: []` · `showLogo?: boolean`  
**Estados:** Padrão  
**Notas:** Identidade SC gov. Presente em 100% dos produtos. Responsividade não documentada.

---

### BC-13 · Forms ⚠️
**Componente Angular:** `<sisp-lib-form [sispLibFormConfig]="config">` (ou submódulo `sisp-lib-form` — confirmar)  
**Recomendação:** Refinar  
**Props:** `fields: [{type, label, name, required, mask?}]` · `onSubmit: Function` · `validation?`  
**Estados:** Default · Preenchido · Erro de validação · Enviando · Sucesso  
**Notas:** Referenciado por SC-03 e SC-06 como `sisp-lib-form` — confirmar se é BC-13 ou submódulo separado. Estados de validação não mapeados. Campos com máscara precisam spec própria.

---

### BC-14 · Headers
**Componente Angular:** `<sisp-lib-header [sispLibHeaderConfig]="config">`  
**Recomendação:** Refinar  
**Props:** `title?` · `showNav?: boolean`  
**Estados:** Desktop · Mobile (hamburger?)  
**Notas:** Alta visibilidade. Logo SISP não alinhado com padrão SC gov. Breakpoints não documentados.

---

### BC-15 · Icons
**Componente Angular:** `<sisp-lib-icon [sispLibIconConfig]="config">`  
**Recomendação:** Refinar  
**Props:** `icon: string (FA class)` · `size?: string` · `color?: string`  
**Estados:** Default · Colorido  
**Notas:** Mapeamento semântico não existe. Font Awesome Free limita variedade. Criar guia de ícones por contexto.

---

### BC-16 · Loaders
**Componente Angular:** `<sisp-lib-loader [sispLibLoaderConfig]="config">`  
**Recomendação:** Manter  
**Props:** `type?: "spinner"|"bar"` · `size?` · `label?`  
**Estados:** Ativo · Inativo  
**Notas:** Spinner sem label = inacessível. Verificar integração com estados loading dos SISP Components auto-suficientes.

---

### BC-17 · Maintenance
**Componente Angular:** `<sisp-lib-maintenance [sispLibMaintenanceConfig]="config">`  
**Recomendação:** Manter  
**Props:** `enabled: boolean` · `message?: string`  
**Estados:** Ativo (bloqueia) · Inativo  
**Notas:** Utilitário específico do SISP. Funcional.

---

### BC-18 · Skeleton Layers
**Recomendação:** A confirmar  
**Notas:** Inacessível durante o inventário. Demilis confirmou ser um estado, não componente em manutenção. Classificação real a confirmar. Acesso ao repositório pode ser necessário.

---

### BC-19 · Modals
**Componente Angular:** `<sisp-lib-modal [sispLibModalConfig]="config">`  
**Recomendação:** Refinar  
**Props:** `title` · `size?: "sm"|"md"|"lg"|"xl"` · `footer?: boolean` · `onClose: Function`  
**Estados:** Aberto · Fechado · Com backdrop  
**Notas:** Demilis quer reduzir uso de modals full-screen na DV. Foco e teclado não documentados. Criar alternativa de drawer.

---

### BC-20 · Navigation Canvas
**Componente Angular:** `<sisp-lib-navigation-canvas [sispLibNavigationCanvasConfig]="config">`  
**Recomendação:** Refinar  
**Props:** `items: []` · `collapsed?: boolean`  
**Estados:** Expandido · Colapsado · Item ativo · Item com submenu  
**Notas:** Alto impacto visual. Estrutura da DV será revisada por Demilis. Definir comportamento colapsado/expandido.

---

### BC-21 · Objects
**Recomendação:** A confirmar  
**Notas:** Não catalogado em detalhe. Acesso ao portal ou repositório necessário antes do Figma.

---

### BC-22 · Offcanvas
**Componente Angular:** `<sisp-lib-offcanvas [sispLibOffcanvasConfig]="config">`  
**Recomendação:** Refinar  
**Props:** `position?: "start"|"end"|"top"|"bottom"` · `title?` · `backdrop?`  
**Estados:** Aberto · Fechado  
**Notas:** Mobile/touch não documentado. Diferenciar casos de uso vs. Navigation Canvas.

---

### BC-23 · Popovers
**Componente Angular:** `<sisp-lib-popover [sispLibPopoverConfig]="config">`  
**Recomendação:** Manter  
**Props:** `content` · `trigger?: "hover"|"click"` · `placement?`  
**Estados:** Visível · Oculto  
**Notas:** Documentar integração com BC-13 Forms (campos com ℹ️).

---

### BC-24 · Route Selectors
**Componente Angular:** `<sisp-lib-route-selector [sispLibRouteSelectorConfig]="config">`  
**Recomendação:** Manter  
**Props:** `routes: []` · `activeRoute?: string`  
**Estados:** Rota ativa · Rota inativa  
**Notas:** Integração com BC-26 Tabs via `useRouteSelector`. Estável.

---

### BC-25 · Tables
**Componente Angular:** `<sisp-lib-table [sispLibTableConfig]="config">`  
**Recomendação:** Refinar  
**Props:** `columns: SortColumn[]` · `data: []` · `pagination?: object` · `selectable?: boolean`  
**Estados:** Default · Ordenado ASC/DESC · Linha selecionada · Vazio · Loading  
**Notas:** `SortColumn` é tipo específico, não string livre. Acessibilidade de tabela (scope, ARIA) não documentada. Responsividade mobile não documentada.

---

### BC-26 · Tabs
**Componente Angular:** `<sisp-lib-tabs [sispLibTabsConfig]="config">`  
**Recomendação:** Manter  
**Props:** `tabs: [{label, content}]` · `useRouteSelector?: boolean` · `justified?: boolean`  
**Estados:** Aba ativa · Aba inativa · Aba desabilitada  
**Notas:** Padrão verde SISP consolidado. ARIA `role="tablist"` etc. não documentados.

---

### BC-27 · Toasts
**Componente Angular:** `<sisp-lib-toast [sispLibToastConfig]="config">`  
**Recomendação:** Manter  
**Props:** `type: SispLibStyleType` · `message: string` · `duration?: number` · `dismissible?: boolean`  
**Estados:** Visível · Saindo (animate out) · Danger (confirmado em produção)  
**Notas:** Validado em produção — SC-10 dispara 2 toasts Danger em erro de BFF.  
**⚠️ SispLibStyleType** compartilhado.

---

### BC-28 · Version
**Componente Angular:** `<sisp-lib-version>`  
**Recomendação:** Manter  
**Notas:** Exibe `v1.0.0 | build 28/05/2026 | Homologação`. Utilitário. Manter como está.

---

## SISP Components — detalhamento

### SC-01 · Atualizações Recentes
**Componente Angular:** `<sisp-lib-recent-updates>` (sem props — auto-suficiente)  
**Recomendação:** Refinar  
**Props:** Nenhuma — dados 100% via BFF  
**Estados:** Preenchido · Vazio · Erro de BFF · Loading (não documentado)  
**Notas:** INCONSISTÊNCIA: doc diz "expandir/recolher" mas preview mostra paginação (3 itens/página). Esclarecer com Demilis.

---

### SC-02 · Consultar Pessoa
**Componente Angular:** `<sisp-lib-consult-person [sispLibConsultPersonConfig]="config">`  
**Recomendação:** Refinar  
**Props:** `camposVisiveis: string[]` · `onConsultar: Function` · outras não documentadas  
**Estados:** Aba Documento · Aba Nome · Aba Outros · Resultado (não documentado) · Loading · Erro  
**Notas:** Relação entre `camposVisiveis` array e abas visíveis não documentada. Props adicionais "entre outros" não detalhadas. Layout do resultado não documentado.

---

### SC-03 · Consultar Registro
**Componente Angular:** `<sisp-lib-consult-record>` (auto-suficiente via BFF + sisp-lib-form)  
**Recomendação:** Refinar  
**Props:** Nenhuma — usa `sisp-lib-form` internamente  
**Estados:** Aba Nº Registro · Aba Período (5 campos) · Aba Base Nacional · Resultado · Loading · Erro  
**Notas:** Campo Unidade tem input composto incomum. Aba "Base Nacional" = integração federal não documentada. Confirmar se `sisp-lib-form` = BC-13 ou submódulo.

---

### SC-04 · Consultar Veículo
**Componente Angular:** `<sisp-lib-consult-vehicle [sispLibConsultVehicleConfig]="config">`  
**Recomendação:** Refinar  
**Props:** `camposVisiveis: string[]` · `onConsultar: Function` · outras não documentadas  
**Estados:** Aba Placa (máscara) · Aba Chassi · Aba Renavam · Aba Fragmento  
**Notas:** Máscara de placa: formato antigo (AAA-0000) ou Mercosul (AAA-0A00)? Confirmar. "Busca por fragmento" = substring — feature policial específica.

---

### SC-05 · Pesquisa de Objetos
**Recomendação:** A confirmar  
**Notas:** Inacessível durante o inventário. Catalogar no stage antes de especificar.

---

### SC-06 · Pesquisa Textual
**Componente Angular:** `<sisp-lib-text-search>` (auto-suficiente via BFF)  
**Recomendação:** Refinar  
**Props:** Nenhuma — auto-suficiente  
**Estados:** Formulário preenchido · Pesquisa fonetizada ativada · Resultado · Loading · Erro  
**Notas:** Pesquisa fonetizada é diferencial importante para contexto policial. Tooltip ℹ️ não visível no inventário. "Data (abertura)" = filtro por fase do ciclo de vida do BO.

---

### SC-07 · Image Captures
**Componente Angular:** `<sisp-lib-image [sispLibImageConfig]="config">` ⚠️ fora da convenção  
**Recomendação:** Refinar  
**Props:** `actionAfterCapture: Function (OBRIGATÓRIO)`  
**Estados:** Default (botão) · Câmera aberta · Imagem capturada · Permissão negada · Erro de dispositivo  
**Notas:** Label "Open Camera" em inglês — traduzir para "Abrir câmera". Config `sispLibImageConfig` fora da convenção (deveria ser `sispLibImageCaptureConfig`). Sem preview ou confirmação pós-captura documentados.

---

### SC-08 · Login
**Recomendação:** A confirmar  
**Notas:** Inacessível durante o inventário. Crítico — autenticação dual SISP + OAuth. Catalogar antes de especificar. Ver SC-12 Session Control.

---

### SC-09 · Logradouros
**Componente Angular:** `<sisp-lib-address [apenasSantaCatarina]="bool" (selecionaLogradouro)="handler">` ⚠️ padrão diferente  
**Recomendação:** Refinar  
**Props:** `[apenasSantaCatarina]: boolean (def: false)` · `(selecionaLogradouro): EventEmitter`  
**Estados:** Aba Buscar por CEP (máscara) · Aba Buscar por Logradouro · Lista de resultados · Vazio  
**Notas:** Único componente com padrão Angular nativo (@Input/@Output) — difere de todos os outros SISP. Botão usa "Consultar" — outros usam "Pesquisar" (inconsistência de vocabulário).

---

### SC-10 · Notificações
**Componente Angular:** `<sisp-lib-notifications>` (auto-suficiente via BFF)  
**Recomendação:** Refinar  
**Props:** Nenhuma — auto-suficiente  
**Estados:** Caixa de entrada · Arquivo · Erro de BFF (documentado em produção) · Vazio · Loading  
**Notas:** Erro de BFF aciona mensagem inline + 2 toasts Danger simultâneos — confirmado ao vivo durante inventário. Diferenciação lida/não lida não documentada.

---

### SC-11 · Resource Trees
**Componente Angular:** `<sisp-lib-resource-tree [sispLibResourceTreeConfig]="config" (selectionChange)="handler">`  
**Recomendação:** Refinar  
**Props:** `nodes: TreeNode[] (OBRIGATÓRIO)` · `(selectionChange): EventEmitter (opcional)`  
**Estados:** Completed (verde) · Required (vermelho) · Optional (âmbar) · Card expandido · Card colapsado  
**Notas:** Color coding sem enum documentado. Ícones ℹ️ e ✏️ sem função documentada. Propriedades do TreeNode inferidas mas não documentadas. Enum de status provavelmente compartilhado com SC-13.

---

### SC-12 · Session Control ⚠️ Maior risco operacional
**Componente Angular:** embarcado no layout global do `sisp-lib`  
**Recomendação:** Refinar  
**Props:** Nenhuma — lê estado interno do sisp-lib  
**Estados:** Sessão SISP ativa (barra verde + countdown) · OAuth inativo ("Login sem OAuth") · Sessão expirando (visual não documentado) · Sessão expirada (não documentado)  
**Notas:** Componente estava embarcado no portal durante todo o inventário sem ser reconhecido como componente separado. Feedback visual progressivo (verde→âmbar→vermelho) não implementado. Fluxo OAuth não documentado. Em operação policial = risco real de perda de dados.

---

### SC-13 · Steppers
**Componente Angular:** `<sisp-lib-stepper [sispLibStepperConfig]="config" (stepChange)="handler">`  
**Recomendação:** Refinar  
**Props:** `steps: Step[]` · `currentStep: number` · `justified?: boolean` · `useSeparator?: boolean` · `finishButtonLabel?: string` · `finishButtonAction?: Function` · `(stepChange): EventEmitter`  
**Estados:** Horizontal numerado · Horizontal justified · Vertical · Vertical com título no conteúdo  
**Notas:** Componente mais bem documentado do DS — serve de referência de qualidade. `title` do step inferido mas não no exemplo. Color coding de steps provavelmente compartilha enum com SC-11.

---

### SC-14 · Timelines
**Componente Angular:** `<sisp-lib-timeline [sispLibTimelineConfig]="config">`  
**Recomendação:** Refinar  
**Props:** `events: TimelineEvent[]` · `orientation?: string` · `color?: string`  
**Estados:** Agrupado por data (pills coloridas) · Card expandido · Card colapsado  
**Notas:** `orientation` sem valores válidos documentados. `color` sem escopo definido. 1º grupo com pill vermelha, demais azuis — lógica não documentada.

---

### SC-15 · Uploaders
**Componente Angular:** `<sisp-lib-uploader [sispLibUploaderConfig]="config">`  
**Recomendação:** Refinar  
**Props:** `onUpload: Function (OBRIGATÓRIO)` · `accept?: string (ex: ".pdf,.jpg")` · `multiple?: boolean`  
**Estados:** Default (botão) · Dialog aberto · Upload em andamento · Concluído · Arquivo rejeitado · Erro  
**Notas:** Label "Upload File" em inglês — traduzir para "Enviar arquivo". Drag-and-drop não documentado. Integração com BC-11 File Previews não documentada.

---

### SC-16 · Relatório de Consultas
**Recomendação:** Recriar  
**Notas:** Componente não existe — TODO no portal. Parte do subgrupo Consultas Policiais (item 2.6). Levantar requisitos com Sommer: quais dados, filtros, exportação, periodicidade.

---

## Padrões técnicos globais

| Padrão | Detalhe |
|---|---|
| Stack | Angular (monorrepo) |
| Convenção de componente | `sisp-lib-[nome]` |
| Convenção de config | `[sispLib[Nome]Config]` |
| Componente de fallback | `sisp-lib-not-found` |
| Largura máxima de layout | 1200px |
| CSS base | Bootstrap (utilitários) |
| Ícones | Font Awesome Free |
| Segurança HTML | Sanitização automática Angular |

## Enums compartilhados (atenção ao alterar)

| Enum | Usado por | Risco |
|---|---|---|
| `SispLibStyleType` | BC-03 · BC-04 · BC-05 · BC-27 | Qualquer mudança afeta 4 componentes |
| Enum de status (Completed/Required/Optional) | SC-11 · SC-13 | Provavelmente compartilhado — confirmar com Demilis |

## Componentes que precisam catalogação antes do Figma

| ID | Nome | Ação necessária |
|---|---|---|
| BC-18 | Skeleton Layers | Acessar repositório com Demilis |
| BC-21 | Objects | Acessar portal ou repositório |
| SC-05 | Pesquisa de Objetos | Acessar portal stage |
| SC-08 | Login | Acessar portal stage |
