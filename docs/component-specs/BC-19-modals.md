---
component-id: BC-19
component-name: Modals
type: Base
status: approved
sprint: 4
approved-by: [Giuliana]
approved-date: [2026-07-14]
figma-node-id: [157:384]
---

# Component Spec — Modals

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → BC-19 (vis **crit**) · BC-09 (cor aten · vis aten)
> - `docs/analyses/nielsen-analysis.md` → BC-19 (H-3 **crit** · H-4 aten · H-8 aten) · BC-09 (H-5 **crit** · H-1 aten · H-2 aten · H-3 aten · H-4 aten · H-8 aten · H-9 aten)
> - `docs/analyses/inventory.md` → BC-19 · BC-09

---

## O que é

Modal é o componente de diálogo sobreposto do DS SISP. Exibe conteúdo em camada superior à página, bloqueando interação com o fundo. Na DV, modais são usados para formulários de edição rápida, detalhes de registro, e confirmação de ações destrutivas. BC-09 Confirmation Modals é incorporado como variante `type: confirmation`. Atualmente 100% Bootstrap, sem trap de foco, sem documentação de teclado, ação destrutiva sem destaque visual.

---

## Audiência de uso

- **Policial na DV:** confirma exclusão de BO, edita dados em modal sem sair da tela de listagem, visualiza detalhes de pessoa envolvida
- **Devs CiASC / terceiros:** usam modais para formulários rápidos, confirmações, exibição de detalhes sem navegação
- **POs (Sommer/Holiwod):** Demilis quer reduzir uso de modais full-screen na DV — modais devem ser focados e objetivos

---

## Props / API

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `title` | `string` | sim | — | Título exibido no header da modal |
| `type` | `'default' \| 'confirmation'` | não | `'default'` | Tipo — default (conteúdo livre) ou confirmation (ação com botões Confirmar/Cancelar) |
| `size` | `'sm' \| 'md' \| 'lg'` | não | `'md'` | Largura da modal |
| `confirmLabel` | `string` | não | `'Confirmar'` | Texto do botão de confirmação (só `type: confirmation`) |
| `cancelLabel` | `string` | não | `'Cancelar'` | Texto do botão de cancelamento (só `type: confirmation`) |
| `danger` | `boolean` | não | `false` | Quando `true`, botão de confirmação usa estilo Danger (ação destrutiva) |
| `footer` | `boolean` | não | `true` | Exibe ou oculta a barra de rodapé com botões |
| `onClose` | `Function` | não | — | Callback ao fechar (×, Escape, ou backdrop) |
| `onConfirm` | `Function` | não | — | Callback ao confirmar (só `type: confirmation`) |

**Convenção Angular:**
```html
<!-- Modal padrão -->
<sisp-lib-modal [sispLibModalConfig]="config">
  <ng-template #modalBody>
    <!-- conteúdo livre -->
  </ng-template>
</sisp-lib-modal>

<!-- Confirmation modal -->
<sisp-lib-modal [sispLibModalConfig]="confirmConfig"></sisp-lib-modal>
```

**Exemplo de uso:**
```typescript
// Modal padrão
config: SispLibModalConfig = {
  title: 'Detalhes da Ocorrência',
  size: 'md',
  footer: true
};

// Confirmation modal (ação destrutiva)
confirmConfig: SispLibModalConfig = {
  title: 'Excluir Boletim',
  type: 'confirmation',
  confirmLabel: 'Excluir',
  cancelLabel: 'Cancelar',
  danger: true,
  onConfirm: () => this.deleteBO()
};
```

---

## Anatomia do componente

```
┌──────────────── Backdrop (overlay escuro) ─────────────────┐
│                                                             │
│   ┌───────────────────────────────────────────────┐         │
│   │  Título                                  [×]  │ Header  │
│   ├───────────────────────────────────────────────┤         │
│   │                                               │         │
│   │  Conteúdo do body                             │ Body    │
│   │                                               │         │
│   ├───────────────────────────────────────────────┤         │
│   │              [Cancelar]  [Confirmar]          │ Footer  │
│   └───────────────────────────────────────────────┘         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

- **Backdrop:** overlay `rgba(0, 0, 0, 0.5)` cobrindo toda a viewport. Clique fecha a modal
- **Container:** card branco centralizado vertical e horizontalmente. `role="dialog"`, `aria-modal="true"`, `aria-labelledby` vinculado ao título
- **Header:** título + botão fechar (×). Border bottom `1px solid --color-border-base`
- **Body:** conteúdo livre (formulário, texto, componentes). Scroll vertical se necessário
- **Footer:** barra de ações. Border top `1px solid --color-border-base`. Botões alinhados à direita

> **Regra 11 — Composição atômica:** botões no footer são instâncias de BC-05 Button. Botão × de fechar usa token de ícone, não recriação manual.

---

## Estados e variantes

### Tipos

| Tipo | Descrição | Footer |
|---|---|---|
| **Default** | Conteúdo livre no body — formulário, detalhes, informação | Opcional (prop `footer`) |
| **Confirmation** | Mensagem + botões Cancelar/Confirmar | Sempre visível |

### Tamanhos

| Tamanho | Largura | Uso típico |
|---|---|---|
| `sm` | 400px | Confirmações simples, alertas curtos |
| `md` | 560px | Formulários padrão, detalhes |
| `lg` | 720px | Formulários complexos, tabelas internas |

### Estados visuais

| Estado | Descrição | Tokens |
|---|---|---|
| **Open** | Modal visível com backdrop | `bg: --color-surface-bg` · `shadow: 0 20px 60px rgba(0,0,0,0.3)` |
| **Close button hover** | × escurece | `text: --color-text-primary` |
| **Close button focus** | × com ring de foco | `outline: 2px solid --color-border-focus` |
| **Danger confirmation** | Botão confirmar em vermelho | Instância de BC-05 Button `Type=Danger` |

### Verificação de contraste (WCAG AA)

| Elemento | Texto | Fundo | Ratio | Resultado |
|---|---|---|---|---|
| Título | #111827 | #FFFFFF | ~18.1:1 | ✅ AAA |
| Body text | #111827 | #FFFFFF | ~18.1:1 | ✅ AAA |
| Close × | #6B7280 | #FFFFFF | ~4.6:1 | ✅ AA |
| Backdrop overlay | — | rgba(0,0,0,0.5) | — | Decorativo |

### Dimensões

| Propriedade | Valor | Token |
|---|---|---|
| Border radius (container) | 8px | `--radius-lg` |
| Header padding | 16px 24px | `--space-4` / `--space-6` |
| Body padding | 24px | `--space-6` |
| Footer padding | 16px 24px | `--space-4` / `--space-6` |
| Gap entre botões (footer) | 12px | `--space-3` |
| Close button size | 24px × 24px | — |
| Font size (título) | 18px | `--text-lg` |
| Font weight (título) | 600 | `--font-semibold` |
| Font size (body) | 14px | `--text-sm` |
| Shadow | `0 20px 60px rgba(0,0,0,0.3)` | — |
| Max height body | `calc(80vh - header - footer)` | — |

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução neste spec |
|---|---|---|
| Visual (vis **crit**) | Trap de foco não documentado — teclado pode sair da modal | **Focus trap completo:** foco preso dentro da modal enquanto aberta. Tab cicla entre elementos focáveis. Shift+Tab cicla reverso. Foco inicial vai para o primeiro elemento interativo. Foco retorna ao trigger após fechar |
| Uso de cor (cor aten — BC-09) | Ação destrutiva sem destaque visual | Variante `danger = true` — botão Confirmar usa BC-05 Button `Type=Danger` (vermelho). Texto do botão é explícito ("Excluir", "Remover"), não genérico |
| Visual (vis aten — BC-09) | Visual de confirmação não diferenciado | Confirmation modal tem layout específico: mensagem centralizada + 2 botões com hierarquia clara (Cancelar secundário + Confirmar primário/danger) |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução neste spec |
|---|---|---|
| H-3 Controle do usuário (CRIT) | Trap de foco não documentado — usuário pode "escapar" sem querer | 3 formas de fechar: botão ×, tecla Escape, clique no backdrop. Focus trap impede saída acidental por Tab. `aria-modal="true"` bloqueia leitura do fundo por screen reader |
| H-5 Prevenção de erros (CRIT — BC-09) | Ação destrutiva sem confirmação adequada | Confirmation modal obriga 2 cliques: abrir → confirmar. Ação destrutiva usa `danger = true` com botão vermelho + label explícito. Botão Cancelar sempre presente e visualmente mais neutro |
| H-1 Visibilidade (aten — BC-09) | Estado da ação não claro | Título da confirmation modal descreve a ação ("Excluir Boletim"), mensagem detalha consequência ("Esta ação não pode ser desfeita") |
| H-4 Consistência (aten) | Sem padrão de quando usar modal vs. drawer vs. navegação | Guia de uso: modal para ações que exigem atenção imediata e bloqueio do contexto. Drawer para consultas rápidas sem bloqueio. Navegação para fluxos complexos |
| H-8 Estética (aten) | Visual Bootstrap genérico | Container com shadow profunda, border-radius por token, espaçamento por tokens, backdrop com opacidade controlada |
| H-9 Recuperação de erros (aten — BC-09) | Sem orientação após erro | Confirmation modal permite cancelar. Foco retorna ao trigger após fechar (usuário volta ao estado anterior) |

---

## Regras de acessibilidade

- [ ] Container com `role="dialog"` e `aria-modal="true"`
- [ ] `aria-labelledby` no container referenciando o `id` do título
- [ ] **Focus trap:** foco preso dentro da modal enquanto aberta
  - Tab move entre elementos focáveis dentro da modal (cicla no último → primeiro)
  - Shift+Tab cicla reverso (primeiro → último)
  - Foco não sai para o backdrop ou conteúdo atrás
- [ ] **Foco inicial:** primeiro elemento interativo dentro da modal (input, botão Cancelar, ou botão ×)
- [ ] **Foco de retorno:** ao fechar, foco retorna ao elemento que abriu a modal
- [ ] **Escape:** fecha a modal (mesmo comportamento do botão ×)
- [ ] Botão × com `aria-label="Fechar"`
- [ ] Backdrop com `aria-hidden="true"`
- [ ] Conteúdo atrás com `aria-hidden="true"` e `inert` enquanto modal aberta
- [ ] Focus ring visível em todos os elementos interativos: `2px solid var(--color-border-focus)`
- [ ] Contraste verificado — mínimo 4.5:1 AA para todos os textos
- [ ] Botões do footer são instâncias de BC-05 Button (Regra 11)

---

## Comportamentos esperados

- Quando modal abre → backdrop aparece, container centraliza, foco move para primeiro elemento interativo, scroll do body da página é bloqueado (`overflow: hidden`)
- Quando clica no botão × → modal fecha, foco retorna ao trigger, backdrop desaparece
- Quando pressiona Escape → mesmo que botão ×
- Quando clica no backdrop → modal fecha (se `type: default`). Em `type: confirmation`, clique no backdrop **não fecha** (força decisão explícita)
- Quando `type: confirmation` e clica Confirmar → `onConfirm` é chamado, modal fecha
- Quando `type: confirmation` e clica Cancelar → modal fecha sem `onConfirm`
- Quando `danger = true` → botão Confirmar usa estilo Danger (instância BC-05 Button Danger)
- Quando conteúdo do body excede altura disponível → body ganha scroll vertical. Header e footer permanecem fixos
- Quando tamanho da viewport < largura da modal → modal ocupa 100% da largura com margens laterais de `--space-4` (16px)

---

## Composição com outros componentes

> **Regra 11 — Composição atômica:** todo elemento que já exista como componente no DS deve ser instância.

| Componente | Relação | Composição no Figma |
|---|---|---|
| BC-05 Buttons | Botões no footer (Confirmar, Cancelar, ações) | **Instância de Button** — Primary para confirmar, Secondary para cancelar, Danger para ações destrutivas |
| BC-13 Forms | Formulários dentro do body da modal | **Instâncias de Input, Select, Textarea** |
| BC-03 Alerts | Mensagens de erro/sucesso dentro do body | **Instância de Alert** se necessário |
| BC-25 Tables | Tabelas de seleção dentro de modal grande | Composição externa |
| BC-26 Tabs | Tabs dentro do body para organizar conteúdo | Composição externa |

---

## Mapeamento de retrocompatibilidade

| Prop atual (BC-19) | Mapeamento novo | Nota |
|---|---|---|
| `title` | `title` | Mantido |
| `size: "sm"\|"md"\|"lg"\|"xl"` | `size: "sm"\|"md"\|"lg"` | XL removido — reduzir modais full-screen (decisão Demilis) |
| `footer` | `footer` | Mantido |
| `onClose` | `onClose` | Mantido |

| Prop atual (BC-09) | Mapeamento novo | Nota |
|---|---|---|
| `title` | `title` | Mantido |
| `message` | Conteúdo do body | Texto via template, não prop |
| `confirmLabel` | `confirmLabel` | Mantido |
| `cancelLabel` | `cancelLabel` | Mantido |
| `onConfirm` | `onConfirm` | Mantido |
| `type: SispLibStyleType` | `danger: boolean` | Simplificado — só success ou danger fazem sentido em confirmação |

---

## Comportamento responsivo

> Regra 13 — Responsividade obrigatória. Breakpoints: Mobile (<768px), Tablet (768–1023px), Desktop (≥1024px).

| Precisa de variante `Layout=Mobile`? | **Sim** — modal centralizado não funciona em viewport estreito |
|---|---|
| **Desktop** | Card centralizado com backdrop. Default: 560px, Confirmation: 400px. Radius 8px, padding horizontal 24px (`space/6`) |
| **Mobile** | Tela cheia (375px / 100% viewport). Sem radius, padding 16px (`space/4`) uniforme. Sem backdrop (ocupa tela inteira) |
| **Tablet** | Segue Desktop — viewport suficiente para card centralizado |

**O que muda entre Desktop e Mobile:**
- Largura: 560/400px card → 375px full-screen
- Border radius: 8px → 0px
- Padding horizontal (Header/Body/Footer): 24px → 16px
- Backdrop: presente → ausente (modal ocupa 100% da tela)
- Estrutura interna: mantida (Header + Divider + Body + Divider + Footer)
- Composição atômica: mantida (Buttons são instâncias BC-05)

**Variantes no Figma:** 6 variantes (3 tipos × 2 layouts)
- `Type=Default, Layout=Desktop` / `Type=Default, Layout=Mobile`
- `Type=Confirmation, Layout=Desktop` / `Type=Confirmation, Layout=Mobile`
- `Type=Confirmation Danger, Layout=Desktop` / `Type=Confirmation Danger, Layout=Mobile`

---

## Casos excepcionais / bordas

- **Modal sobre modal (stacking):** evitar. Se necessário, cada modal tem seu backdrop e z-index incremental. Máximo 2 níveis
- **Modal com formulário não salvo:** ao tentar fechar, exibir confirmation modal ("Descartar alterações?") antes de fechar
- **Mobile (< 640px):** modal ocupa tela cheia (width: 100%, height: 100%, sem border-radius, sem backdrop). Botão × no canto superior direito
- **Título muito longo (> 50 caracteres):** trunca com `text-overflow: ellipsis`. Recomendação: máximo 5 palavras
- **Body vazio:** modal renderiza com height mínima de `--space-8` (32px) no body
- **Múltiplos botões no footer:** máximo 3 botões. Alinhados à direita com gap `--space-3`

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-surface-bg` | Fundo do container |
| `--color-text-primary` | Título, texto do body |
| `--color-text-secondary` | Botão × (default) |
| `--color-text-muted` | Texto auxiliar |
| `--color-border-base` | Separadores header/footer |
| `--color-border-focus` | Ring de foco |
| `--color-danger` | Botão confirmar destrutivo (via instância Button) |
| `--radius-lg` | Border radius container (8px) |
| `--font-body` | Família tipográfica |
| `--text-lg` | Font size título (18px) |
| `--text-sm` | Font size body (14px) |
| `--font-semibold` | Peso do título (600) |
| `--space-3 / --space-4 / --space-6` | Paddings e gaps |

---

## O que está fora deste spec

- **Drawer (painel lateral):** alternativa sugerida por Demilis. Especificar como componente separado se necessário
- **Modal com stepper:** composição com SC-13 Steppers dentro do body — não do componente base
- **Modal full-screen:** removido intencionalmente — usar navegação de página
- **Animação de entrada/saída:** fade-in/scale pode ser adicionado. Não especificado neste sprint
- **Modal não-blocking (modeless):** fora do padrão da DV. Sempre modal (blocking)

---

## Critérios de aceite

- [ ] 2 tipos (Default, Confirmation) no Figma
- [ ] 3 tamanhos (SM, MD, LG) documentados
- [ ] Variante `danger` com botão Confirmar em estilo Danger
- [ ] Focus trap documentado: Tab, Shift+Tab, Escape, foco inicial, foco de retorno
- [ ] `role="dialog"`, `aria-modal="true"`, `aria-labelledby` documentados
- [ ] **Composição atômica (Regra 11):** botões no footer são instâncias de BC-05 Button, não elementos manuais
- [ ] Backdrop com click-to-close (default) e sem click-to-close (confirmation)
- [ ] Contraste verificado — mínimo 4.5:1 AA
- [ ] Violação WCAG AA (vis **crit** · cor aten · vis aten) resolvida
- [ ] Violações Nielsen (H-3 **crit** · H-5 **crit** · H-1 aten · H-4 aten · H-8 aten · H-9 aten) resolvidas
- [ ] Mapeamento de retrocompatibilidade documentado (BC-19 + BC-09)
- [ ] Revisado e aprovado por Giuliana
