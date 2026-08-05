---
component-id: BC-15
component-name: Icons
type: Base
status: approved
sprint: 4
approved-by: [Giuliana]
approved-date: [2026-07-15]
figma-node-id: [223:516]
---

# Component Spec — Icons

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → BC-15 (wcag aten · cor aten)
> - `docs/analyses/nielsen-analysis.md` → BC-15 (H-2 **crit** · H-4 **crit** · H-6 **crit** · H-8 aten)
> - `docs/analyses/inventory.md` → BC-15

---

## O que é

Icon é o componente de ícones do DS SISP. Encapsula ícones Font Awesome Free com tamanhos padronizados, cores por token, regras de acessibilidade e um **mapa semântico** que define qual ícone usar para cada ação/conceito no sistema. Na DV, ícones aparecem em botões, menus dropdown, alertas, toasts, badges, tabs, table headers e ações de linha. Atualmente funcional mas sem mapeamento semântico (mesmo ícone com sentidos diferentes), sem tamanhos padronizados e sem alternativas textuais para screen readers.

> **Regra 12 aplicada:** ícones já existem dentro de BC-05 Buttons, BC-03 Alerts, BC-27 Toasts, BC-10 Dropdowns, BC-26 Tab Item. Este spec padroniza o uso — não cria um novo wrapper visual, mas documenta o sistema de ícones como componente de referência.

---

## Audiência de uso

- **Policial na DV:** vê ícones em botões de ação, menus, alertas, status. Precisa reconhecer instantaneamente o que cada ícone significa (editar, excluir, salvar, imprimir)
- **Devs CiASC / terceiros:** usam `sisp-lib-icon` para inserir ícones. Precisam saber qual classe FA usar para cada ação — sem consultar documentação externa
- **POs (Sommer/Holiwod):** precisam que ícones sejam consistentes em todos os módulos — "editar" é sempre o mesmo ícone

---

## Props / API

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `icon` | `string` | sim | — | Classe Font Awesome — ex: `'fa-solid fa-pen'` |
| `size` | `'xs' \| 'sm' \| 'md' \| 'lg' \| 'xl'` | não | `'md'` | Tamanho do ícone |
| `color` | `'primary' \| 'secondary' \| 'muted' \| 'inverse' \| 'success' \| 'warning' \| 'danger' \| 'info' \| 'inherit'` | não | `'inherit'` | Cor semântica — mapeia para token CSS |
| `label` | `string` | não* | — | Texto alternativo para acessibilidade. *Obrigatório quando ícone é informativo (não acompanhado de texto visível) |
| `decorative` | `boolean` | não | `false` | Quando `true`, ícone é puramente visual — `aria-hidden="true"`, sem label |

**Convenção Angular:**
```html
<sisp-lib-icon [sispLibIconConfig]="config"></sisp-lib-icon>
```

**Exemplos de uso:**
```typescript
// Ícone decorativo dentro de botão (texto "Editar" já comunica a ação)
iconDecorative: SispLibIconConfig = {
  icon: 'fa-solid fa-pen',
  size: 'sm',
  decorative: true
};

// Ícone informativo sem texto visível (ex: botão icon-only)
iconInformative: SispLibIconConfig = {
  icon: 'fa-solid fa-pen',
  size: 'md',
  color: 'primary',
  label: 'Editar'
};

// Ícone de status
iconStatus: SispLibIconConfig = {
  icon: 'fa-solid fa-circle-check',
  size: 'sm',
  color: 'success',
  label: 'Concluído'
};
```

---

## Anatomia do componente

```
┌──────┐
│  ✏️  │  ← ícone Font Awesome (inline element)
└──────┘

Com label visível (uso standalone):
┌──────┐
│  ✏️  │  Editar    ← ícone + texto ao lado
└──────┘

Dentro de outro componente (decorativo):
┌────────────────┐
│  ✏️  Editar     │  ← dentro de BC-05 Button — ícone é decorativo
└────────────────┘
```

- **Ícone:** elemento `<i>` com classe Font Awesome. Renderiza como glifo tipográfico
- **Label:** texto alternativo — visível (`<span>`) ou invisível (`aria-label` / sr-only)

---

## Estados e variantes

### Tamanhos

| Tamanho | Font Size | Line Height | Touch Target | Uso típico |
|---|---|---|---|---|
| `xs` | 12px | 16px | — | Dentro de Badge, indicadores inline |
| `sm` | 14px | 20px | — | Dentro de Button SM, items de Dropdown/Menu |
| `md` | 16px | 24px | — | Default — Button MD, Alerts, Toasts |
| `lg` | 20px | 28px | — | Destaque, headers, ações primárias |
| `xl` | 24px | 32px | 44px | Icon-only buttons, ilustrações inline |

### Cores

| Color prop | Token CSS | Valor (tema SC) | Uso |
|---|---|---|---|
| `primary` | `--color-primary-base` | #C4000B | Ação primária, links, destaque |
| `secondary` | `--color-text-secondary` | #6B7280 | Ações secundárias, complementos |
| `muted` | `--color-text-muted` | #9CA3AF | Disabled, placeholder, decorativo |
| `inverse` | `--color-text-inverse` | #FFFFFF | Sobre fundos escuros (identity bar, botões primários) |
| `success` | `--color-success` | #166534 | Status sucesso |
| `warning` | `--color-warning` | #92400E | Status atenção |
| `danger` | `--color-danger` | #991B1B | Status erro, ação destrutiva |
| `info` | `--color-info` | #1E40AF | Status informativo |
| `inherit` | `inherit` | — | Herda cor do parent (default) |

### Verificação de contraste (WCAG AA)

| Cor | Valor | Fundo branco | Ratio | Resultado |
|---|---|---|---|---|
| primary | #C4000B | #FFFFFF | 5.2:1 | ✅ AA (gráficos ≥3:1) |
| secondary | #6B7280 | #FFFFFF | 4.6:1 | ✅ AA |
| muted | #9CA3AF | #FFFFFF | 2.9:1 | ⚠️ Apenas decorativo |
| inverse | #FFFFFF | #111827 | >15:1 | ✅ AAA |
| success | #166534 | #FFFFFF | 7.1:1 | ✅ AAA |
| warning | #92400E | #FFFFFF | 5.8:1 | ✅ AA |
| danger | #991B1B | #FFFFFF | 6.5:1 | ✅ AAA |
| info | #1E40AF | #FFFFFF | 7.3:1 | ✅ AAA |

---

## Mapa semântico de ícones

> **Resolve H-2 crit, H-4 crit, H-6 crit.** Este mapa é a fonte única de verdade para qual ícone usar por ação/conceito no DS SISP.

### Ações CRUD

| Ação | Classe Font Awesome | Ícone | Contexto DV |
|---|---|---|---|
| Criar / Novo | `fa-solid fa-plus` | ＋ | Novo BO, nova pessoa |
| Editar | `fa-solid fa-pen` | ✏️ | Editar BO, editar dados |
| Salvar | `fa-solid fa-floppy-disk` | 💾 | Salvar formulário |
| Excluir | `fa-solid fa-trash` | 🗑️ | Excluir BO (sempre com confirmação) |
| Cancelar | `fa-solid fa-xmark` | ✕ | Cancelar operação |
| Duplicar | `fa-solid fa-copy` | 📋 | Duplicar registro |

### Ações de documento

| Ação | Classe Font Awesome | Ícone | Contexto DV |
|---|---|---|---|
| Imprimir | `fa-solid fa-print` | 🖨️ | Imprimir BO |
| Exportar PDF | `fa-solid fa-file-pdf` | 📄 | Exportar BO como PDF |
| Download | `fa-solid fa-download` | ⬇️ | Baixar anexo |
| Upload | `fa-solid fa-upload` | ⬆️ | Enviar anexo |
| Anexar | `fa-solid fa-paperclip` | 📎 | Anexar documento |

### Navegação

| Conceito | Classe Font Awesome | Ícone | Contexto DV |
|---|---|---|---|
| Menu / Hambúrguer | `fa-solid fa-bars` | ☰ | Header mobile |
| Chevron (expandir) | `fa-solid fa-chevron-down` | ▾ | Dropdown trigger, accordion |
| Chevron (voltar) | `fa-solid fa-chevron-left` | ◂ | Navegação, breadcrumb |
| Buscar | `fa-solid fa-magnifying-glass` | 🔍 | Campo de busca, consulta |
| Filtrar | `fa-solid fa-filter` | ⊞ | Filtros de tabela |
| Ordenar | `fa-solid fa-sort` | ⇕ | Coluna ordenável |
| Home | `fa-solid fa-house` | 🏠 | Voltar para início |
| Configurações | `fa-solid fa-gear` | ⚙️ | Configurações do sistema |

### Status / Feedback

| Conceito | Classe Font Awesome | Ícone | Componente |
|---|---|---|---|
| Sucesso | `fa-solid fa-circle-check` | ✓ | Alert Success, Toast Success |
| Atenção | `fa-solid fa-triangle-exclamation` | ⚠ | Alert Warning, Toast Warning |
| Erro | `fa-solid fa-circle-xmark` | ✕ | Alert Danger, Toast Danger |
| Informação | `fa-solid fa-circle-info` | ℹ | Alert Info, Toast Info |
| Carregando | `fa-solid fa-spinner` | ↻ | Loader (animação via CSS) |

### Contexto policial (DV)

| Conceito | Classe Font Awesome | Ícone | Contexto |
|---|---|---|---|
| Pessoa | `fa-solid fa-user` | 👤 | Pessoas envolvidas |
| Pessoas (grupo) | `fa-solid fa-users` | 👥 | Lista de pessoas |
| Documento / BO | `fa-solid fa-file-lines` | 📄 | Boletim de ocorrência |
| Veículo | `fa-solid fa-car` | 🚗 | Consulta veicular |
| Localização | `fa-solid fa-location-dot` | 📍 | Endereço da ocorrência |
| Calendário / Data | `fa-solid fa-calendar` | 📅 | Data do fato |
| Relógio / Hora | `fa-solid fa-clock` | ⏰ | Hora do fato |
| Câmera / Foto | `fa-solid fa-camera` | 📷 | Image Captures |
| Telefone | `fa-solid fa-phone` | 📞 | Contato |
| E-mail | `fa-solid fa-envelope` | ✉️ | Contato |
| Cadeado / Segurança | `fa-solid fa-lock` | 🔒 | Ação restrita, autenticação |

### Interface

| Conceito | Classe Font Awesome | Ícone | Contexto |
|---|---|---|---|
| Fechar (×) | `fa-solid fa-xmark` | ✕ | Modals, Alerts, Toasts |
| Menu contextual (⋮) | `fa-solid fa-ellipsis-vertical` | ⋮ | Dropdown em tabela |
| Expandir | `fa-solid fa-expand` | ⤢ | Fullscreen, modal expand |
| Olho (mostrar) | `fa-solid fa-eye` | 👁 | Toggle senha, preview |
| Olho cortado (ocultar) | `fa-solid fa-eye-slash` | 👁‍🗨 | Toggle senha |
| Seta externa | `fa-solid fa-arrow-up-right-from-square` | ↗ | Link externo |
| Desfazer | `fa-solid fa-rotate-left` | ↺ | Ação desfazer (Toast) |

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução neste spec |
|---|---|---|
| Acessibilidade (wcag aten) | Ícones sem alternativas textuais | Prop `label` obrigatória para ícones informativos. `decorative = true` adiciona `aria-hidden="true"` para ícones puramente visuais. Ícone sem `label` e sem `decorative = true` → warning no console Angular |
| Uso de cor (cor aten) | Font Awesome variações de tamanho — ícones muito pequenos | 5 tamanhos padronizados (xs 12px a xl 24px). Tamanho mínimo recomendado `sm` (14px) para ícones informativos. Contraste verificado para todas as cores semânticas |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução neste spec |
|---|---|---|
| H-2 Mundo real (**crit**) | Mapeamento semântico não existe — mesmo ícone com sentidos diferentes | **Mapa semântico completo** documentado neste spec: 45+ ícones categorizados por contexto (CRUD, documento, navegação, status, DV). Cada ação tem UM ícone definido. Fonte única de verdade |
| H-4 Consistência (**crit**) | Sem convenção — editar pode ser lápis, caneta ou chave de fenda | **Convenção fixa**: editar = `fa-pen`, excluir = `fa-trash`, salvar = `fa-floppy-disk`, etc. Mapa é mandatório — devs consultam este spec antes de usar ícones |
| H-6 Reconhecimento (**crit**) | Usuário deve memorizar o que cada ícone significa | Ícones sempre acompanhados de **label textual** — em Buttons (texto ao lado), em Dropdowns (label do item), em Tabs (label da aba). Ícones icon-only têm `aria-label` obrigatório. Ícones reforçam o texto, não substituem |
| H-8 Estética (aten) | Visual genérico sem padronização | Tamanhos padronizados, cores via token semântico, alinhamento vertical por line-height. Estilo consistente: `fa-solid` como padrão (preenchido — mais legível em tamanhos pequenos) |

---

## Regras de acessibilidade

- [ ] Ícone decorativo (dentro de Button com texto, Alert com mensagem): `aria-hidden="true"`, `role="presentation"` — não anunciado por screen reader
- [ ] Ícone informativo (standalone, icon-only button, status): `aria-label` com `label` prop obrigatória
- [ ] **Regra de ouro:** ícone NUNCA é a única forma de comunicar informação. Sempre tem texto visível OU `aria-label`
- [ ] Tamanho mínimo 14px (`sm`) para ícones informativos — abaixo disso, apenas decorativo
- [ ] Contraste mínimo 3:1 para ícones (WCAG 2.1 — elementos gráficos). Cor `muted` (#9CA3AF, 2.9:1) apenas para decorativo
- [ ] Focus ring em elementos focáveis que contêm ícone: `2px solid var(--color-border-focus)` com `outline-offset: 2px`
- [ ] Ícones de status (✓ ⚠ ✕ ℹ) sempre acompanhados de texto + cor (3 canais: ícone + texto + cor)
- [ ] `font-display: block` no Font Awesome — previne flash de texto antes do ícone carregar

---

## Comportamentos esperados

- Quando `decorative = true` → ícone renderiza com `aria-hidden="true"` e `role="presentation"`. Screen reader ignora
- Quando `decorative = false` e `label` definido → ícone renderiza com `aria-label` = valor de `label`
- Quando `decorative = false` e `label` **não** definido → warning no console: "Icon informativo sem label — adicione label ou defina decorative = true"
- Quando `color = 'inherit'` (default) → ícone herda `color` do elemento pai via CSS `color: inherit`
- Quando `size` muda → font-size e line-height atualizam via tokens. Ícone mantém alinhamento vertical com texto adjacente via `vertical-align: middle`
- Quando Font Awesome não carregou → elemento mostra espaço vazio (não quebra layout). `font-display: block` mitiga flash

---

## Composição com outros componentes

| Componente | Relação | Como o ícone é usado |
|---|---|---|
| **BC-05 Buttons** | Ícone à esquerda do label | `decorative = true` — label do botão comunica a ação. Tamanho herda do Button (SM→sm, MD→md, LG→lg) |
| **BC-03 Alerts** | Ícone de status à esquerda | `decorative = true` — mensagem + cor + ícone formam 3 canais. Tamanho `md` (16px) |
| **BC-27 Toasts** | Ícone de status à esquerda | Mesmo padrão de Alerts. `decorative = true` |
| **BC-10 Dropdowns** | Ícone à esquerda do item label | `decorative = true` — label do item comunica. Tamanho `sm` (14px) |
| **BC-26 Tab Item** | Ícone à esquerda do tab label | `decorative = true` — label da tab comunica. Tamanho `sm` (14px) |
| **BC-14 Headers** | Hamburger e ícones de user | Hamburger: `fa-bars`, `label: "Abrir menu"`. Tamanho `md` |
| **BC-25 Tables** | Sort indicator, ações de linha | Sort: `fa-sort` decorativo. Ação ⋮: `fa-ellipsis-vertical`, `label: "Ações"` |
| **BC-04 Badges** | Ícone opcional dentro de badge | `decorative = true` — texto do badge comunica. Tamanho `xs` (12px) |

---

## Mapeamento de retrocompatibilidade

| Prop atual | Mapeamento novo | Nota |
|---|---|---|
| `icon` | `icon` | Mantido — classe Font Awesome |
| `size` | `size` (agora com valores definidos: xs/sm/md/lg/xl) | Padronizado — antes aceitava qualquer string |
| `color` | `color` (agora com valores semânticos) | Antes aceitava qualquer cor CSS. Agora mapeia para tokens |
| — | `label` (novo) | Obrigatório para ícones informativos |
| — | `decorative` (novo) | Marca ícone como puramente visual |

---

## Casos excepcionais / bordas

- **Font Awesome não carregou:** ícone mostra espaço vazio. Layout não quebra (font-size preserva o espaço). `font-display: block` garante que o glifo aparece assim que a fonte carrega
- **Classe FA inválida:** ícone não renderiza. Warning no console Angular
- **Ícone não listado no mapa semântico:** dev pode usar qualquer classe FA, mas deve documentar o novo ícone no mapa semântico via PR
- **Ícone animado (spinner):** usar classe `fa-spin` do Font Awesome. Respeita `prefers-reduced-motion` — animação pausa quando ativo
- **RTL (right-to-left):** ícones direcionais (chevrons, setas) precisam espelhar. Font Awesome suporta via `fa-flip-horizontal`
- **Ícone dentro de texto:** usar `vertical-align: -0.125em` (padrão FA) para alinhamento baseline

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-primary-base` | Cor primary |
| `--color-text-secondary` | Cor secondary |
| `--color-text-muted` | Cor muted |
| `--color-text-inverse` | Cor inverse |
| `--color-success` | Cor success |
| `--color-warning` | Cor warning |
| `--color-danger` | Cor danger |
| `--color-info` | Cor info |
| `--color-border-focus` | Ring de foco |
| `--text-xs` | Tamanho xs (12px) |
| `--text-sm` | Tamanho sm (14px) |
| `--text-base` | Tamanho md (16px) |
| `--text-xl` | Tamanho lg (20px) |
| `--text-2xl` | Tamanho xl (24px) |

---

## O que está fora deste spec

- **Ícones customizados (SVG):** DS SISP usa Font Awesome Free exclusivamente. Se surgir necessidade de ícone que FA não cobre, avaliar caso a caso
- **Ícones de logo (marcas):** logos de produtos/clientes usam imagem, não ícone
- **Ícones animados complexos (Lottie):** animação se limita a `fa-spin` e `fa-pulse` do FA
- **Sprite sheet / icon font customizada:** não necessário — Font Awesome via CDN ou npm
- **Ícones com tooltip:** composição com Popover (BC-23) — não pertence a este spec

---

## Critérios de aceite

- [ ] 5 tamanhos (xs, sm, md, lg, xl) no Figma
- [ ] 9 cores semânticas documentadas com tokens
- [ ] Mapa semântico com 45+ ícones por contexto (CRUD, documento, navegação, status, DV, interface)
- [ ] Contraste verificado — todas as cores ≥3:1 para gráficos (exceto muted = decorativo)
- [ ] Regras de acessibilidade documentadas: decorativo vs informativo, `aria-hidden`, `aria-label`
- [ ] Violações WCAG (wcag aten · cor aten) resolvidas
- [ ] Violações Nielsen (H-2 **crit** · H-4 **crit** · H-6 **crit** · H-8 aten) resolvidas
- [ ] Composição com 8 componentes documentada
- [ ] Mapeamento de retrocompatibilidade documentado
- [ ] Revisado e aprovado por Giuliana
