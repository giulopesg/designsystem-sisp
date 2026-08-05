---
component-id: BC-17
component-name: Maintenance
type: Base
status: approved
sprint: 4.1
approved-by: [Giuliana]
approved-date: [2026-07-16]
figma-node-id: [315:692]
---

# Component Spec — Maintenance

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → BC-17 (ok — funcional)
> - `docs/analyses/nielsen-analysis.md` → BC-17 (H-1 aten · H-2 aten · H-4 aten · H-9 aten)
> - `docs/analyses/inventory.md` → BC-17

---

## O que e

Maintenance e o componente de tela de manutencao do DS SISP. Quando ativado, bloqueia a interface com uma mensagem informando que o sistema esta em manutencao. Usado durante deploys, atualizacoes de banco, ou indisponibilidades planejadas. Na DV, aparece quando o sistema SISP esta fora do ar para manutencao — o policial ve a tela e sabe que deve aguardar.

---

## Audiencia de uso

- **Policial na DV:** ve a tela de manutencao quando o sistema esta indisponivel. Precisa entender que e temporario e nao um erro do seu lado
- **Devs CiASC / terceiros:** ativam/desativam o modo manutencao via config. Precisam customizar a mensagem por produto
- **POs (Sommer/Holiwod):** precisam que a comunicacao de indisponibilidade seja clara e profissional

---

## Props / API

| Prop | Tipo | Obrigatorio | Padrao | Descricao |
|---|---|---|---|---|
| `enabled` | `boolean` | sim | `false` | Quando true, exibe a tela de manutencao bloqueando a interface |
| `message` | `string` | nao | `'Sistema em manutencao. Tente novamente em alguns minutos.'` | Mensagem principal |
| `title` | `string` | nao | `'Manutencao Programada'` | Titulo da tela |
| `showRetry` | `boolean` | nao | `true` | Exibe botao "Tentar Novamente" |
| `retryUrl` | `string` | nao | `'/'` | URL de redirecionamento ao clicar em retry |
| `estimatedReturn` | `string` | nao | — | Previsao de retorno (ex: "15:00") |

**Convencao Angular:**
```html
<sisp-lib-maintenance [sispLibMaintenanceConfig]="config"></sisp-lib-maintenance>
```

**Exemplo de uso:**
```typescript
config: SispLibMaintenanceConfig = {
  enabled: true,
  title: 'Manutencao Programada',
  message: 'O sistema esta sendo atualizado. Previsao de retorno: 15:00.',
  showRetry: true,
  estimatedReturn: '15:00'
};
```

---

## Anatomia do componente

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│                                                             │
│                        🔧                                   │  ← icone de manutencao
│                                                             │
│               Manutencao Programada                         │  ← titulo (Heading/XL)
│                                                             │
│      O sistema esta sendo atualizado.                       │  ← mensagem (Body/MD)
│      Previsao de retorno: 15:00.                           │
│                                                             │
│              [ Tentar Novamente ]                            │  ← botao retry (BC-05 Secondary MD)
│                                                             │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

- **Container:** fullscreen (100vw × 100vh), centralizado vertical e horizontal, fundo --color-surface
- **Icone:** icone de manutencao (fa-solid fa-wrench ou fa-solid fa-gear) tamanho LG, cor muted
- **Titulo:** Heading/XL, centralizado
- **Mensagem:** Body/MD, centralizada, cor secundaria
- **Previsao:** destaque dentro da mensagem (bold) quando estimatedReturn fornecido
- **Botao retry:** instancia de BC-05 Button Secondary MD

---

## Estados e variantes

| Estado | Descricao visual | Tokens |
|---|---|---|
| **Ativo** | Tela de manutencao visivel, interface bloqueada | `bg: --color-surface` · `text: --color-text-primary` |
| **Inativo** | Componente nao renderiza | — |
| **Com previsao** | Mensagem inclui horario de retorno | Horario em bold (--font-semibold) |
| **Sem retry** | Sem botao — apenas informacao | showRetry=false |

### Cores

| Elemento | Token | Valor |
|---|---|---|
| Fundo | `--color-surface` | #FFFFFF |
| Icone | `--color-text-muted` | #9CA3AF |
| Titulo | `--color-text-primary` | #08060F |
| Mensagem | `--color-text-secondary` | #4B5563 |
| Previsao (bold) | `--color-text-primary` | #08060F |
| Botao retry | BC-05 Secondary MD tokens | — |

### Verificacao de contraste (WCAG AA)

| Elemento | Texto | Fundo | Ratio | Resultado |
|---|---|---|---|---|
| Titulo | #08060F | #FFFFFF | >15:1 | ✅ AAA |
| Mensagem | #4B5563 | #FFFFFF | ~7.2:1 | ✅ AAA |
| Icone | #9CA3AF | #FFFFFF | ~2.8:1 | ✅ (decorativo) |

### Dimensoes

| Propriedade | Valor | Token |
|---|---|---|
| Container | 100vw × 100vh | — |
| Content max-width | 480px | — |
| Content padding | 24px | `--space-6` |
| Icone size | 48px | BC-15 Icons XL |
| Gap icone → titulo | 16px | `--space-4` |
| Gap titulo → mensagem | 12px | `--space-3` |
| Gap mensagem → botao | 24px | `--space-6` |
| Titulo font size | 24px | Heading/XL |
| Titulo font weight | 700 | `--font-bold` |
| Mensagem font size | 16px | `--text-base` |
| Mensagem font weight | 400 | `--font-regular` |
| Mensagem line-height | 1.5 | — |

---

## Violacoes a resolver — WCAG AA

| Dimensao | Violacao atual | Solucao neste spec |
|---|---|---|
| (ok) | Sem violacoes WCAG | Mantido. Contraste verificado. Tokens tipograficos aplicados. Botao retry acessivel via BC-05 |

---

## Violacoes a resolver — Heuristicas Nielsen

| Heuristica | Violacao atual | Solucao neste spec |
|---|---|---|
| H-1 Visibilidade (aten) | Estado de manutencao nao claramente comunicado | Tela fullscreen dedicada — impossivel ignorar. Icone de manutencao (🔧) reforça visualmente. Titulo em Heading/XL proeminente. Nao e um toast ou banner — e uma tela inteira |
| H-2 Mundo real (aten) | Mensagem nao usa linguagem do usuario | Mensagem padrao em portugues, linguagem simples: "Sistema em manutencao. Tente novamente em alguns minutos." Previsao de retorno quando disponivel ("Previsao de retorno: 15:00") — informacao acionavel |
| H-4 Consistencia (aten) | Sem padrao documentado | Maintenance padronizado com config object. Mesmo layout e visual em todos os produtos. Icone + titulo + mensagem + botao como padrao |
| H-9 Recuperacao (aten) | Sem caminho de retorno documentado | Botao "Tentar Novamente" (BC-05 Secondary MD) permite ao usuario tentar recarregar. retryUrl configuravel. Previsao de retorno da expectativa temporal ao usuario |

---

## Regras de acessibilidade

- [ ] Container com `role="alert"` e `aria-live="assertive"` (quando ativado)
- [ ] Titulo com `<h1>` (pagina inteira)
- [ ] Icone com `aria-hidden="true"` (decorativo)
- [ ] Botao retry com texto claro ("Tentar Novamente")
- [ ] Focus automatico no botao retry ao ativar (unico elemento interativo)
- [ ] Contraste minimo 4.5:1 — verificado
- [ ] Labels em portugues

---

## Comportamentos esperados

- Quando `enabled = true` → tela de manutencao exibida, interface bloqueada
- Quando `enabled = false` → componente nao renderiza, interface normal
- Quando `showRetry = true` → botao "Tentar Novamente" visivel
- Quando `showRetry = false` → sem botao, apenas informacao
- Quando usuario clica em "Tentar Novamente" → navega para retryUrl (default: '/')
- Quando `estimatedReturn` fornecido → exibe "Previsao de retorno: {hora}" na mensagem
- Quando `message` customizado → substitui mensagem padrao
- Quando `title` customizado → substitui titulo padrao

---

## Composicao com outros componentes

| Componente | Relacao | Composicao no Figma (Regra 11/12) |
|---|---|---|
| **BC-05 Buttons** | Botao "Tentar Novamente" — instancia de Secondary MD | **Instancia direta** (Regra 11) |
| **BC-15 Icons** | Icone de manutencao (fa-wrench) — instancia XL | Font Awesome decorativo |

> **Regra 12 aplicada:** botao retry e instancia de BC-05 Button Secondary MD. Icone via BC-15 Icons XL.

---

## Mapeamento de retrocompatibilidade

| Prop atual | Mapeamento novo | Nota |
|---|---|---|
| `enabled` | `enabled` | Mantido |
| `message` | `message` | Mantido |
| — | `title` (novo) | Titulo customizavel |
| — | `showRetry` (novo) | Controla botao de retry |
| — | `retryUrl` (novo) | URL de retry |
| — | `estimatedReturn` (novo) | Previsao de retorno |

---

## Comportamento responsivo

> Regra 13 — Responsividade obrigatória. Breakpoints: Mobile (<768px), Tablet (768–1023px), Desktop (≥1024px).

| Precisa de variante `Layout=Mobile`? | **Sim** — tela de manutenção 800px precisa de versão mobile |
|---|---|
| **Desktop** | Frame 800px, content area 480px centralizada |
| **Mobile** | Frame 375px, content area 343px centralizada. Layout centralizado mantido |
| **Tablet** | Segue Desktop |

**Variantes no Figma:** 2 (1 estado × 2 layouts)

---

## Casos excepcionais / bordas

- **Maintenance ativado com formulario em andamento:** dados nao salvos sao perdidos. Nao e responsabilidade do componente — logica de negocio deve salvar rascunhos antes
- **Maintenance desativado durante visualizacao:** tela some, interface retorna. Sem transicao especial
- **Sem mensagem e sem titulo:** renderiza com defaults ("Manutencao Programada" + mensagem padrao)
- **estimatedReturn ja passou:** componente nao valida horario — exibe como texto
- **Mobile:** layout identico — centralizado, responsivo. Content area com padding adequado

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-surface` | Fundo |
| `--color-text-primary` | Titulo, previsao bold |
| `--color-text-secondary` | Mensagem |
| `--color-text-muted` | Icone |
| `--space-6` | Padding container, gap mensagem→botao |
| `--space-4` | Gap icone→titulo |
| `--space-3` | Gap titulo→mensagem |
| `--text-base` | Font size mensagem |
| `--font-bold` | Peso titulo |
| `--font-regular` | Peso mensagem |

---

## O que esta fora deste spec

- **Maintenance com countdown:** extensao futura — requer logica de tempo real
- **Maintenance com status check automatico:** polling de health check nao e responsabilidade do componente visual
- **Maintenance parcial (features especificas):** usar BC-03 Alert inline, nao tela fullscreen
- **Maintenance com login:** tela de login separada — nao combinar com manutencao

---

## Criterios de aceite

- [ ] 1 variante no Figma (tela fullscreen com icone + titulo + mensagem + botao)
- [ ] Botao retry como instancia de BC-05 Button Secondary MD (Regra 11)
- [ ] Icone de manutencao via BC-15 Icons
- [ ] Todos os textos com Text Styles aplicados
- [ ] Contraste verificado
- [ ] Violacoes Nielsen (H-1 · H-2 · H-4 · H-9 aten) resolvidas
- [ ] Mapeamento de retrocompatibilidade documentado
- [ ] Revisado e aprovado por Giuliana
