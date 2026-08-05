---
component-id: BC-03
component-name: Alerts
type: Base
status: approved
sprint: 3
approved-by: [Giuliana]
approved-date: [2026-07-13]
figma-node-id: [118:2456]
---

# Component Spec — Alerts

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → BC-03 (cor **crit** · wcag aten)
> - `docs/analyses/nielsen-analysis.md` → BC-03 (H-1 **crit** · H-3 aten · H-4 aten · H-6 aten)
> - `docs/analyses/inventory.md` → BC-03

---

## O que é

Alert é o componente de feedback contextual do DS SISP. Comunica informações importantes ao usuário dentro do fluxo da página — confirmações, avisos, erros, informações. Diferente do Toast (BC-27), o alert é **inline** e permanece visível até ser dispensado ou até a condição mudar. Atualmente 100% Bootstrap, com 8 variantes diferenciadas apenas por cor — sem ícone, sem rótulo semântico.

---

## Audiência de uso

- **Policial na DV:** vê alerts de validação ao preencher BO (erro de CPF, campos obrigatórios), confirmação ao salvar, avisos de dados incompletos
- **Devs CiASC / terceiros:** usam alerts para feedback de operações CRUD, validação de formulários, mensagens de sistema
- **Cidadão (DV externa):** vê alerts de confirmação de registro, erros de preenchimento

---

## Tipos semânticos — 4 níveis

> **Decisão de design:** substituir as 8 variantes Bootstrap por 4 tipos semânticos alinhados ao sistema de status do DS (mesmos tokens de Cards, Badges, Toasts). O componente Angular mantém retrocompatibilidade com `SispLibStyleType`, mas documentação e Figma orientam exclusivamente os 4 tipos abaixo.

| Tipo | Ícone padrão | Quando usar | Urgência |
|---|---|---|---|
| **Success** | `fa-solid fa-circle-check` | Ação concluída, dado salvo, operação confirmada | Baixa — confirmação |
| **Warning** | `fa-solid fa-triangle-exclamation` | Atenção necessária, dados incompletos, ação reversível | Média — atenção |
| **Danger** | `fa-solid fa-circle-xmark` | Erro, falha, dado inválido, ação bloqueada | Alta — erro |
| **Info** | `fa-solid fa-circle-info` | Informação contextual, dica, instrução de sistema | Baixa — informativo |

---

## Props / API

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `type` | `'success' \| 'warning' \| 'danger' \| 'info'` | sim | — | Tipo semântico do alert — define cor, ícone e role ARIA |
| `message` | `string` | sim | — | Texto principal do alert |
| `description` | `string` | não | — | Texto secundário com detalhes ou instruções |
| `dismissible` | `boolean` | não | `false` | Exibe botão de fechar (×) para dispensar o alert |
| `onDismiss` | `Function` | não | — | Callback disparado ao fechar. O alert **não se remove do DOM sozinho** — o consumidor controla |
| `icon` | `string` | não | (ícone do tipo) | Classe Font Awesome — override do ícone padrão do tipo |
| `showIcon` | `boolean` | não | `true` | Exibe ou oculta o ícone. Deve ser mantido `true` por acessibilidade |

**Convenção Angular:**
```html
<sisp-lib-alert [sispLibAlertConfig]="config"></sisp-lib-alert>
```

**Exemplo de uso:**
```typescript
config: SispLibAlertConfig = {
  type: 'danger',
  message: 'CPF inválido',
  description: 'Verifique os 11 dígitos e tente novamente.',
  dismissible: true
};
```

---

## Anatomia do alert

```
┌──────────────────────────────────────────────────┐
│  [icon]  Message text                       [×]  │
│          Description text (optional)             │
└──────────────────────────────────────────────────┘
```

- **Ícone:** sempre presente (padrão por tipo, override via prop). Alinhado ao topo da primeira linha do message
- **Message:** texto principal, semibold, cor do tipo
- **Description:** texto secundário, regular, cor do tipo (levemente mais claro)
- **Close button (×):** só aparece se `dismissible = true`. Alinhado ao canto superior direito
- **Fundo:** cor de fundo sutil do tipo (success-bg, warning-bg, etc.)
- **Borda esquerda:** 4px sólida na cor forte do tipo — reforço visual além da cor de fundo

---

## Estados e variantes

### Por tipo

| Tipo | Fundo | Borda esquerda | Ícone/Texto | Tokens |
|---|---|---|---|---|
| Success | Verde sutil | Verde forte 4px | Verde escuro | `bg: --color-success-bg` · `border-left: --color-success` · `text: --color-success` · `icon: --color-success` |
| Warning | Amarelo sutil | Laranja forte 4px | Marrom escuro | `bg: --color-warning-bg` · `border-left: --color-warning` · `text: --color-warning` · `icon: --color-warning` |
| Danger | Vermelho sutil | Vermelho forte 4px | Vermelho escuro | `bg: --color-danger-bg` · `border-left: --color-danger` · `text: --color-danger` · `icon: --color-danger` |
| Info | Azul sutil | Azul forte 4px | Azul escuro | `bg: --color-info-bg` · `border-left: --color-info` · `text: --color-info` · `icon: --color-info` |

### Verificação de contraste (WCAG AA)

| Tipo | Texto | Fundo | Ratio | Resultado |
|---|---|---|---|---|
| Success | #166534 | #DCFCE7 | ~7.1:1 | ✅ AA + AAA |
| Warning | #92400E | #FEF3C7 | ~6.8:1 | ✅ AA + AAA |
| Danger | #991B1B | #FEE2E2 | ~6.5:1 | ✅ AA + AAA |
| Info | #1E3A8A | #DBEAFE | ~6.2:1 | ✅ AA + AAA |

### Close button (×)

| Elemento | Token |
|---|---|
| Cor | Mesma cor do texto do tipo (--color-[tipo]) |
| Hover | Opacidade 0.7 |
| Focus | Ring 2px `--color-border-focus` |
| Tamanho | 20×20px, target area mínimo 24×24px |

### Dimensões

| Propriedade | Valor | Token |
|---|---|---|
| Padding | 16px | `--space-4` |
| Gap ícone → texto | 12px | `--space-3` |
| Gap message → description | 4px | `--space-1` |
| Border radius | 6px | `--radius-md` |
| Borda esquerda | 4px solid | — |
| Font size (message) | 14px | `--text-sm` |
| Font weight (message) | 600 | `--font-semibold` |
| Font size (description) | 14px | `--text-sm` |
| Font weight (description) | 400 | `--font-regular` |
| Ícone | 16px | — |

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução neste spec |
|---|---|---|
| Contraste (wcag aten) | Variante Light com contraste potencialmente insuficiente | Variante Light removida. 4 tipos semânticos verificados: todos ≥ 6.2:1 ✅ AAA |
| Uso de cor (cor **crit**) | 8 variantes diferenciadas só por cor — sem ícone, sem rótulo semântico | Cada tipo tem: **ícone semântico** (✓ ⚠ ✕ ℹ) + **cor** + **borda esquerda** + **`role="alert"`** ou **`role="status"`**. Três canais redundantes |
| Visual (vis) | ok | — |
| Tipografia (tip) | ok | Message semibold 14px, description regular 14px — hierarquia clara |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução neste spec |
|---|---|---|
| H-1 Visibilidade (CRIT) | Sem indicação de quão urgente/importante é o alerta | 4 tipos com urgência definida: Success (confirmação), Info (informativo), Warning (atenção), Danger (erro). Ícone semântico reforça a urgência. `role="alert"` para Danger/Warning, `role="status"` para Success/Info |
| H-3 Controle do usuário (aten) | Dismissible não é padrão — usuário não pode controlar sempre | Prop `dismissible` com padrão `false`. Alerts de erro (Danger) devem permanecer visíveis até resolução — não são dismissíveis por padrão. Alerts de sucesso/info podem ser dismissíveis |
| H-4 Consistência (aten) | 8 variantes sem guia semântico de quando usar cada uma | 4 tipos semânticos com guia de uso documentado. Alinhados aos tokens de status do DS (mesmos de Cards BC-06, Toasts BC-27, Badges BC-04) |
| H-6 Reconhecimento (aten) | Usuário precisa memorizar qual cor = qual tipo | Ícone semântico por tipo elimina necessidade de memorização: ✓ = sucesso, ⚠ = atenção, ✕ = erro, ℹ = info. Reconhecimento visual instantâneo |

---

## Regras de acessibilidade

- [ ] `role="alert"` para tipos Danger e Warning (interrompe screen reader — urgente)
- [ ] `role="status"` para tipos Success e Info (não interrompe — informativo)
- [ ] `aria-live="assertive"` implícito em `role="alert"`
- [ ] `aria-live="polite"` implícito em `role="status"`
- [ ] Ícone semântico **sempre presente** — `showIcon` desligado só em casos excepcionais
- [ ] Ícone com `aria-hidden="true"` (decorativo — a informação é transmitida pelo texto + role)
- [ ] Botão fechar com `aria-label="Fechar alerta"` — nunca apenas o caractere ×
- [ ] Focus ring visível no botão de fechar: `2px solid var(--color-border-focus)`
- [ ] Não depende apenas de cor: ícone + cor + borda esquerda = 3 canais
- [ ] Texto do message descreve a situação, não apenas o tipo ("CPF inválido", não "Erro")
- [ ] Contraste verificado para os 4 tipos: todos ≥ 6.2:1 (AAA)

---

## Comportamentos esperados

- Quando alert é renderizado com `type = 'danger'` ou `'warning'` → `role="alert"` interrompe screen reader para anunciar
- Quando alert é renderizado com `type = 'success'` ou `'info'` → `role="status"` anuncia na próxima pausa do screen reader
- Quando `dismissible = true` e usuário clica × → dispara `onDismiss` callback. O alert **não se remove do DOM sozinho** — o consumidor controla a remoção
- Quando `dismissible = false` → botão × não renderiza. Alert permanece até a condição mudar
- Quando `description` está vazio → alert renderiza apenas message (single-line, mais compacto)
- Quando alert aparece dinamicamente (inserido no DOM após ação) → animação de entrada: `opacity 0→1` + `translateY -8px→0` com `--transition-normal` (200ms)
- Quando alert é dispensado → animação de saída: `opacity 1→0` + `height collapse` com `--transition-normal` (200ms)

---

## Composição com outros componentes

| Componente | Relação |
|---|---|
| BC-06 Cards | Alerts dentro de cards — acima ou abaixo do conteúdo, com padding do card |
| BC-13 Forms | Alert de erro acima do formulário para erro geral. Erros de campo usam BC-13 inline error, não alert |
| BC-27 Toasts | Toast é temporário e flutuante. Alert é inline e persistente. Não misturar: se a mensagem é contextual e deve ficar visível, usar Alert |
| SC-02/03/04 Consultas | Alerts de resultado: "Nenhum registro encontrado" (Info), "Consulta realizada" (Success) |

---

## Mapeamento de retrocompatibilidade

| SispLibStyleType antigo | Mapeamento novo | Nota |
|---|---|---|
| `success` | **Success** | Direto |
| `warning` | **Warning** | Direto |
| `danger` | **Danger** | Direto |
| `info` | **Info** | Direto |
| `primary` | **Info** | Primary em alerts não é semântico — mapeado para Info |
| `secondary` | **Info** | Sem uso real em alerts |
| `dark` | **Info** | Visual escuro não pertence a alerts |
| `light` | **Info** | Contraste insuficiente — removido, mapeado para Info |

---

## Casos excepcionais / bordas

- **Message muito longo:** texto quebra em múltiplas linhas. Ícone alinhado à primeira linha. Sem truncamento — alerts devem ser legíveis por completo
- **Múltiplos alerts:** stack vertical com gap de `--space-3` (12px). Ordem: Danger primeiro (topo), depois Warning, depois Info/Success
- **Mobile (< 640px):** alert ocupa 100% da largura. Layout mantido (ícone + texto + ×)
- **Alert dentro de card colapsado:** quando o card é expandido e contém alert Danger, o alert deve ser o primeiro item visível
- **Alert sem ícone (`showIcon = false`):** acessibilidade degradada — `role` e texto compensam, mas documentar como exceção

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-success` | Ícone, texto, borda esquerda (Success) |
| `--color-success-bg` | Fundo (Success) |
| `--color-warning` | Ícone, texto, borda esquerda (Warning) |
| `--color-warning-bg` | Fundo (Warning) |
| `--color-danger` | Ícone, texto, borda esquerda (Danger) |
| `--color-danger-bg` | Fundo (Danger) |
| `--color-info` | Ícone, texto, borda esquerda (Info) |
| `--color-info-bg` | Fundo (Info) |
| `--color-border-focus` | Ring de foco no botão fechar |
| `--radius-md` | Border radius (6px) |
| `--font-body` | Família tipográfica |
| `--text-sm` | Font size message e description (14px) |
| `--font-semibold` | Peso do message (600) |
| `--font-regular` | Peso do description (400) |
| `--space-1` | Gap message → description (4px) |
| `--space-3` | Gap ícone → texto (12px), gap entre alerts (12px) |
| `--space-4` | Padding interno (16px) |
| `--transition-normal` | Animação entrada/saída (200ms) |

---

## O que está fora deste spec

- **Alert com ação (botão inline):** não identificado no inventário. Se surgir necessidade (ex: "Desfazer" em alert de sucesso), especificar como extensão
- **Alert banner (full-width no topo da página):** pode ser especificado como variante de layout, não como componente separado
- **Alert com countdown (auto-dismiss):** comportamento de Toast (BC-27), não de Alert. Alert é persistente
- **Alert com lista de erros:** composição de múltiplos alerts ou seção de erros dentro de um card — responsabilidade do produto

---

## Critérios de aceite

- [ ] 4 tipos semânticos (Success, Warning, Danger, Info) existem no Figma com tokens
- [ ] Cada tipo com ícone semântico, cor de fundo sutil, borda esquerda colorida
- [ ] Verificação de contraste: todos ≥ 4.5:1 (AA) documentado
- [ ] Variante dismissible com botão × e `aria-label`
- [ ] Variante com description (message + description)
- [ ] `role="alert"` para Danger/Warning, `role="status"` para Success/Info
- [ ] Violação WCAG AA (cor **crit** · wcag aten) resolvida
- [ ] Violações Nielsen (H-1 **crit** · H-3 aten · H-4 aten · H-6 aten) resolvidas
- [ ] Mapeamento de retrocompatibilidade com `SispLibStyleType` documentado
- [ ] Revisado e aprovado por Giuliana
