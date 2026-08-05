# Golden Rules — DS SISP

Regras não negociáveis. Aplicam-se a toda operação do agente neste projeto.

---

## Regra 1 — Spec antes de Figma
Nunca criar componente no Figma sem component-spec aprovada por Giuliana.

## Regra 2 — Figma antes de Angular
Nunca refatorar Angular sem o componente Figma aprovado.

## Regra 3 — Verbalizar raciocínio
Sempre verbalizar o raciocínio antes de agir.

## Regra 4 — Dados reais
Nunca inventar dados de análise — as violações WCAG e Nielsen estão nos docs de análise. Recuperar antes de especificar.

## Regra 5 — WCAG + Nielsen
Cada component-spec resolve violações WCAG AA E heurísticas Nielsen — não só contraste.

## Regra 6 — Session log
Manter session-log/INDEX.md atualizado com decisões materiais.

## Regra 7 — Figma MCP
Design-to-code via Figma MCP — para qualquer componente, verificar/criar referência no Figma antes de gerar Angular.

## Regra 8 — Tokens primeiro
Nenhum componente usa valor hardcoded. Tudo via tokens CSS / variáveis Figma. Isso inclui:
- **Variáveis Figma** para cor, espaçamento e border-radius em todo fill, padding, gap e radius
- **Text Styles** aplicados a todo text node (dos 21 existentes: Heading, Overline, Body, Label, Mono)
- **Color fills** bound a variáveis de Cores Semânticas (não valores hex hardcoded)

## Regra 9 — Código modular
Arquivos abaixo de 200 linhas onde prático.

## Regra 10 — DV primeiro
Delegacia Virtual é o produto-âncora. Componentes da DV têm prioridade.

## Regra 11 — Composição atômica
**Nunca recriar o que já existe.** Todo elemento visual dentro de um componente que já exista como componente no DS **deve ser uma instância** desse componente, nunca uma recriação manual.

Exemplos obrigatórios:
- Checkbox dentro de Table → instância do BC-13 Checkbox
- Badge de status dentro de Table → instância do BC-04 Badge
- Botão dentro de Modal → instância do BC-05 Button
- Trigger de Dropdown → instância do BC-05 Button
- Nav items no Header → instância do BC-26 Tabs
- Action no Toast → instância do BC-05 Button Tertiary SM

Isso garante:
- (a) fonte única de verdade — mudança no componente base propaga para todos
- (b) consistência visual automática
- (c) manutenção centralizada

### Mapa de composição atual (auditado 2026-07-22):

| Componente complexo | Usa instâncias de |
|---|---|
| BC-06 Cards | BC-05 Button (Primary SM + Tertiary SM) |
| BC-25 Tables | BC-13 Checkbox + BC-04 Badge |
| BC-19 Modals | BC-05 Button (Secondary + Primary + Danger) |
| BC-14 Headers | BC-26 Tabs (com Tab Items) |
| BC-10 Dropdowns | BC-05 Button (Secondary MD) |
| BC-26 Tabs | BC-26 Tab Item |
| BC-27 Toasts | BC-05 Button (Tertiary SM — action "Desfazer") |
| BC-24 Route Selectors | BC-26 Tab Item (4 instâncias por variante) |
| BC-11 File Previews | BC-05 Button (Tertiary SM) + BC-15 Icons (LG) |
| BC-20 Navigation Canvas | BC-04 Badge (Neutral Filled SM) |
| SC-12 Session Control | BC-04 Badge + BC-05 Button + BC-15 Icons |
| SC-10 Notificações | BC-26 Tab Item + BC-05 Button (Tertiary SM) + BC-18 Skeleton + BC-15 Icons |
| SC-13 Steppers | BC-05 Button (Secondary/Primary/Danger SM) + BC-03 Alert (Danger) |
| SC-15 Uploaders | BC-05 Button (Tertiary SM) + BC-15 Icons (LG) + BC-16 Loader (padrão visual — progress bar) |
| SC-07 Image Captures | BC-05 Button (Primary/Secondary/Danger SM) + BC-15 Icons (MD) |
| SC-08 Login | BC-13 Input (2×) + BC-05 Button (Primary MD + Secondary MD) + BC-03 Alert (Warning + Danger) + BC-16 Loader (Spinner SM) |
| DC-04 Section Card | BC-15 Icons (LG) + DC-09 Page Tag (Icon=No, 5×) |
| DC-05 Persona Card | BC-15 Icons (LG) + DC-09 Page Tag (Icon=Yes, 3×) |
| DC-06 Component Card | BC-04 Badge (Neutral Subtle SM) |
| DC-09 Page Tag (Icon=Yes) | BC-15 Icons (XS) |
| DC-10 Callout Card | BC-15 Icons (LG) |
| SC-17 Login SISP | BC-05 Button (Primary MD, 2×) + BC-15 Icons (XS, 4×) |

### Padrão aceito — Close Buttons:
Close Buttons (×) em Alerts, Toasts e Modals são frames manuais 24×24. Aceito porque são menores que Button SM (32px). Candidato a micro-componente futuro.

## Regra 12 — Auditoria pré-criação
**Antes de especificar ou criar qualquer novo componente**, auditar o inventário de componentes existentes para identificar elementos reutilizáveis — não apenas visuais idênticos, mas padrões funcionais equivalentes.

A pergunta obrigatória: **"este elemento já existe como componente, mesmo que com outro nome ou contexto?"**

Exemplo: nav items no Header = Tabs. Lista de opções em Dropdown = Select (padrão funcional, mas uso diferente).

## Regra 13 — Responsividade obrigatória
**Todo componente com layout complexo deve ter variante `Layout=Mobile` no Figma.** Componentes auto-contidos (largura 100% do container ou menores que 320px) não precisam de variante responsiva.

### Breakpoints (alinhado com Bootstrap):

| Nome | Largura | Uso típico |
|---|---|---|
| **Mobile** | < 768px | Celular em modo retrato |
| **Tablet** | 768px – 1023px | Tablet em retrato, celular em paisagem |
| **Desktop** | ≥ 1024px | Computador, tablet em paisagem |

### Nomenclatura no Figma:
- Propriedade: `Layout`
- Valores: `Desktop` e `Mobile`
- Tablet segue Desktop com ajustes de padding via tokens responsivos
- Não criar variante Tablet no Figma (multiplica exponencialmente)

### Tokens responsivos:

| Token | Desktop | Mobile |
|---|---|---|
| `--space-page-padding` | 48px | 16px |
| `--space-section-gap` | 40px | 24px |
| `--space-card-padding` | 24px | 16px |
| `--space-modal-padding` | 32px | 16px |

### Componentes que NÃO precisam de variante responsiva:
BC-03 Alerts, BC-04 Badges, BC-05 Buttons, BC-13 Forms, BC-15 Icons, BC-16 Loaders, BC-23 Popovers, BC-27 Toasts, BC-28 Version.

### Componentes que PRECISAM de variante `Layout=Mobile`:
BC-25 Tables, BC-06 Cards, BC-19 Modals, BC-22 Offcanvas, BC-26 Tabs, BC-10 Dropdowns, BC-20 Nav Canvas, BC-02 Accordions, BC-07 Carousels, BC-24 Route Selectors, SC-08 Login, SC-13 Steppers, SC-15 Uploaders. (BC-14 Headers e BC-12 Footers já têm.)

> Referência completa: `.claude/rules/responsiveness.md`

## Regra 14 — Layouts usam apenas instâncias e estilos existentes

**Ao criar layouts de tela (Sprint 6+), cada elemento visual deve ser uma instância de um Component Set existente no Figma.** Layouts são composições — não superfícies de criação.

### Proibido em layouts:
- Criar componentes novos ad-hoc (frames manuais que replicam componentes existentes)
- Criar text styles novos (usar os 21 text styles existentes: Heading, Overline, Body, Label, Mono)
- Criar color styles novos (usar as 99 variáveis existentes nas 4 coleções)
- Trocar fontes (Arial/Arimo permanecem como estão nos componentes — não substituir)
- Valores hardcoded de cor, espaçamento ou tipografia

### Permitido em layouts:
- Instâncias de qualquer um dos 46 Component Sets existentes
- Overrides de conteúdo nas instâncias (texto, ícone, estado visível)
- Frames de layout (containers para organizar instâncias) com binding a variáveis de Spacing
- Background usando variáveis de Colors existentes (ex: `--color-surface`, `--color-bg-subtle`)

### Checklist pré-layout:
A pergunta obrigatória antes de cada elemento: **"este elemento já existe como Component Set? Se sim, usar instância."**

## Regra 15 — Text Styles obrigatórios

**Todo text node dentro de um Component Set deve ter um text style aplicado** (dos 21 existentes: Heading, Overline, Body, Label, Mono).

### Exceções aceitas:
- Ícones decorativos Font Awesome (text nodes com caracteres como ✏, ×, →, ≡ que usam fontes de ícone)
- Esses ícones são candidatos a substituição por instâncias de BC-15 Icons onde possível

### Verificação:
Após criar ou modificar qualquer Component Set, auditar text nodes:
```javascript
const textNodes = component.findAll(n => n.type === 'TEXT');
const withStyle = textNodes.filter(n => n.textStyleId && n.textStyleId !== '');
// Meta: 100% (exceto exceções decorativas documentadas)
```

### Critério de aprovação:
- 100% dos text nodes textuais com text style aplicado
- Exceções decorativas explicitamente documentadas na auditoria do componente
- Color fills de text nodes bound a variáveis de Cores Semânticas (text/primary, text/secondary, text/muted, text/inverse)

> Aprovada D148 — identificada pela auditoria de compliance dos DC Components.
