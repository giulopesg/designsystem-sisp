# Responsividade — DS SISP

> **STATUS: APROVADO — Regra 13 do DS SISP**
> Criado: 2026-07-23 · Aprovado: 2026-07-23
> Contexto: decisão sistêmica identificada na triagem de pendências (D104)
> Implementado em: CLAUDE.md (Regra 13), golden-rules.md (Regra 13), _template.md (seção "Comportamento responsivo")

---

## Problema

Dos 37 Component Sets no Figma, apenas Header e Footer têm variantes Desktop/Mobile. Os outros 35 não têm comportamento responsivo definido. Isso significa que terceiros contratados não sabem como cada componente se adapta a telas menores.

---

## Breakpoints

| Nome | Largura | Uso típico |
|---|---|---|
| **Mobile** | < 768px | Celular em modo retrato |
| **Tablet** | 768px – 1023px | Tablet em retrato, celular em paisagem |
| **Desktop** | ≥ 1024px | Computador, tablet em paisagem |

> Alinhado com Bootstrap (stack atual do SISP). Evita criar breakpoints proprietários.

---

## Regras por tipo de componente

### Componentes que NÃO precisam de variantes responsivas

Componentes auto-contidos que já se adaptam porque usam largura relativa (100% do container) ou são pequenos o suficiente para qualquer tela:

- **BC-03 Alerts** — largura 100% do container, já funciona
- **BC-04 Badges** — inline, tamanho fixo pequeno
- **BC-05 Buttons** — largura configurável, já funciona
- **BC-13 Forms (Input, Select, Textarea, Checkbox, Radio)** — largura 100% do container
- **BC-15 Icons** — tamanho fixo (XS–XL)
- **BC-16 Loaders** — centralizado, tamanho fixo
- **BC-23 Popovers** — posicional, largura fixa
- **BC-27 Toasts** — largura fixa, posição fixa
- **BC-28 Version** — texto simples

> **Regra:** componentes que já usam `width: 100%` ou são menores que 320px não precisam de variante responsiva no Figma.

### Componentes que PRECISAM de variantes responsivas

Componentes com layout complexo que muda conforme a largura da tela:

| Componente | Desktop | Mobile | O que muda |
|---|---|---|---|
| **BC-14 Headers** | ✅ já tem | ✅ já tem | Menu horizontal → hamburger |
| **BC-12 Footers** | ✅ já tem | ✅ já tem | Colunas → stack vertical |
| **BC-25 Tables** | Colunas visíveis | Scroll horizontal ou stack | Colunas demais para tela pequena |
| **BC-06 Cards** | Grid 2-3 colunas | Stack vertical 1 coluna | Container de cards, não o card em si |
| **BC-19 Modals** | Card centralizado | Tela cheia (full-screen) | Largura fixa → 100% viewport |
| **BC-22 Offcanvas** | Painel lateral 320px | Tela cheia (100% viewport) | Largura fixa → 100% |
| **BC-26 Tabs** | Horizontal | Scroll horizontal ou dropdown | Muitas abas não cabem |
| **BC-10 Dropdowns** | Posicional (abaixo do trigger) | Pode virar bottom sheet | Padrão mobile UX |
| **BC-20 Nav Canvas** | Sidebar fixa | Overlay com toggle | Menu lateral esconde |
| **BC-02 Accordions** | Largura do container | Largura do container | Conteúdo interno pode reorganizar |
| **BC-07 Carousels** | Múltiplos itens visíveis | 1 item por vez | Quantidade de itens visíveis |
| **BC-24 Route Selectors** | Horizontal | Stack vertical | Muitas opções não cabem |
| **SC-08 Login** | Card centralizado | Tela cheia | Layout do card |
| **SC-13 Steppers** | Horizontal | Vertical | Passos horizontais não cabem |
| **SC-15 Uploaders** | Área de drop completa | Área reduzida + botão | Espaço de preview |

### Componentes avaliados caso a caso (D116)

- **BC-01 About** — ✅ PRECISA — criado (600→343px, padding 24→16)
- **BC-11 File Previews** — ✅ NÃO PRECISA — já 360px, inline
- **BC-17 Maintenance** — ✅ PRECISA — criado (800→375px, content 480→343)
- **BC-18 Skeleton Layers** — ✅ NÃO PRECISA — primitivos, herdam tamanho do pai
- **BC-21 Objects** — ⏳ BLOQUEADO — 6 perguntas pendentes com Demilis
- **SC-07 Image Captures** — ✅ PRECISA — criado (D115, 480→343px)
- **SC-10 Notificações** — ✅ NÃO PRECISA — já 360px, mobile-friendly
- **SC-12 Session Control** — ✅ PRECISA — criado (800→375px, User Name via FILL)

---

## Padrão de nomenclatura no Figma

Cada Component Set com variantes responsivas adiciona a propriedade `Layout`:

```
Component Set: Button
├── State=Default, Size=MD          ← sem Layout (funciona em qualquer tela)

Component Set: Modal
├── State=Default, Layout=Desktop   ← card centralizado
├── State=Default, Layout=Mobile    ← tela cheia
├── State=Confirmation, Layout=Desktop
├── State=Confirmation, Layout=Mobile
```

> **Convenção:** propriedade `Layout` com valores `Desktop` e `Mobile`. Tablet segue Desktop com ajustes de padding via tokens. Evitar criar variante Tablet no Figma (multiplica exponencialmente).

---

## Tokens responsivos

Adicionar variáveis Figma para padding/gap que mudam por breakpoint:

| Token | Desktop | Mobile | Uso |
|---|---|---|---|
| `--space-page-padding` | 48px | 16px | Padding lateral das páginas |
| `--space-section-gap` | 40px | 24px | Gap entre seções |
| `--space-card-padding` | 24px | 16px | Padding interno de cards |
| `--space-modal-padding` | 32px | 16px | Padding interno de modais |

> Implementação: modo "Mobile" na coleção Spacing do Figma (mesmo padrão que Colors já usa com modos SC/PC).

---

## Sprint proposta

**Sprint R1 — Responsividade** (estimativa: ~15 componentes a atualizar)

Prioridade DV (Regra 10):
1. **SC-08 Login** (Mobile) — tela de login é a primeira interação
2. **BC-25 Tables** (Mobile) — tabelas são o core da DV
3. **BC-19 Modals** (Mobile) — confirmações e formulários
4. **BC-14 Headers / BC-12 Footers** — já feitos ✅
5. **BC-20 Nav Canvas** (Mobile) — navegação lateral
6. **SC-13 Steppers** (Mobile) — fluxo de BO
7. **BC-26 Tabs** (Mobile) — navegação por abas

Após DV:
8. BC-22 Offcanvas, BC-10 Dropdowns, BC-06 Cards, BC-07 Carousels
9. BC-24 Route Selectors, SC-15 Uploaders, SC-07 Image Captures

> **Nota:** esta sprint pode ser encaixada entre os Sprints 6-7 ou como sprint paralela. Não bloqueia os sprints atuais porque os componentes desktop já estão completos.

---

## Regras para os .md do projeto

Quando aprovada, esta regra deve ser incorporada a:

1. **CLAUDE.md** — adicionar como Regra 13: "Responsividade obrigatória"
2. **golden-rules.md** — adicionar Regra 13 com breakpoints e padrão de nomenclatura
3. **_template.md** (component spec template) — adicionar seção "Comportamento responsivo"
4. **Inventário** — adicionar coluna "Responsivo?" ao catálogo de 44 componentes

---

## Critérios de aceite da sprint de responsividade

- [ ] Breakpoints definidos e documentados
- [ ] Tokens responsivos criados na coleção Spacing (modo Mobile)
- [ ] Variantes `Layout=Mobile` criadas para os 15 componentes prioritários
- [ ] Cada variante Mobile usa composição atômica (Regra 11)
- [ ] Cada variante Mobile verifica WCAG AA (Regra 5)
- [ ] Specs atualizadas com seção "Comportamento responsivo"
- [ ] Component Set naming convention: `Layout=Desktop | Mobile`
