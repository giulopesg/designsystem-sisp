---
component-id: BC-16
component-name: Loaders
type: Base
status: approved
sprint: 4
approved-by: [Giuliana]
approved-date: [2026-07-14]
figma-node-id: [177:484]
---

# Component Spec — Loaders

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → BC-16 (spinner sem label)
> - `docs/analyses/nielsen-analysis.md` → BC-16 (H-1 **crit** · H-4 aten · H-6 aten · H-8 aten · H-9 aten)
> - `docs/analyses/inventory.md` → BC-16

---

## O que é

Loader é o componente de indicação de carregamento do DS SISP. Comunica ao usuário que uma operação está em andamento — consulta ao servidor, envio de formulário, carregamento de dados. Na DV, loaders aparecem ao consultar boletins, salvar ocorrências, carregar listas e processar uploads. Atualmente com 2 tipos (spinner e bar), mas spinner sem label — inacessível para screen readers e sem feedback textual para o usuário.

---

## Audiência de uso

- **Policial na DV:** vê loaders ao consultar BOs, salvar dados, aguardar respostas do servidor. Precisa saber que a operação está em andamento e que não houve erro
- **Devs CiASC / terceiros:** usam loaders para qualquer operação assíncrona. Precisam de API simples com tipo, tamanho e label configuráveis
- **POs (Sommer/Holiwod):** precisam que o sistema comunique claramente "estou processando" — sem silêncio que gere dúvida

---

## Props / API

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `type` | `'spinner' \| 'bar'` | não | `'spinner'` | Tipo visual — spinner (circular) ou bar (barra linear) |
| `size` | `'sm' \| 'md' \| 'lg'` | não | `'md'` | Tamanho do loader |
| `label` | `string` | sim | — | Texto descritivo — ex: "Carregando boletins...", "Processando..." |
| `showLabel` | `boolean` | não | `true` | Exibe o label visualmente. Quando `false`, label fica apenas como `aria-label` (acessível, mas invisível) |
| `progress` | `number` | não | — | Valor de progresso (0–100). Quando definido, bar mostra progresso determinado. Quando indefinido, animação indeterminada |
| `overlay` | `boolean` | não | `false` | Quando `true`, loader aparece sobre um overlay semitransparente que bloqueia interação com o conteúdo abaixo |

**Convenção Angular:**
```html
<sisp-lib-loader [sispLibLoaderConfig]="config"></sisp-lib-loader>
```

**Exemplo de uso:**
```typescript
// Spinner simples
spinnerConfig: SispLibLoaderConfig = {
  type: 'spinner',
  size: 'md',
  label: 'Carregando boletins...'
};

// Barra de progresso determinada
barConfig: SispLibLoaderConfig = {
  type: 'bar',
  size: 'md',
  label: 'Enviando anexos...',
  progress: 45
};

// Spinner em overlay (bloqueia tela)
overlayConfig: SispLibLoaderConfig = {
  type: 'spinner',
  size: 'lg',
  label: 'Processando consulta...',
  overlay: true
};
```

---

## Anatomia do componente

### Spinner
```
       ╭───╮
      ╱  ●  ╲       ← arco circular animado (3/4 do círculo)
     │       │
      ╲     ╱
       ╰───╯
  Carregando...      ← label textual (abaixo)
```

### Bar (indeterminado)
```
┌─────────────────────────────────┐
│  ████████░░░░░░░░░░░░░░░░░░░░░ │  ← barra com animação de varredura
└─────────────────────────────────┘
Carregando...                        ← label textual (abaixo)
```

### Bar (determinado, progress=45)
```
┌─────────────────────────────────┐
│  ██████████████░░░░░░░░░░░░░░░ │  ← barra preenchida até 45%
└─────────────────────────────────┘
Enviando anexos... 45%               ← label + percentual
```

---

## Estados e variantes

### Tipos

| Tipo | Descrição | Quando usar |
|---|---|---|
| **Spinner** | Indicador circular rotativo | Operações sem progresso mensurável — consultas, carregamento de página, aguardando resposta |
| **Bar** | Barra horizontal linear | Operações com progresso mensurável (upload, processamento em lotes) ou indeterminado (sem `progress`) |

### Tamanhos

| Tamanho | Spinner (diâmetro) | Bar (altura) | Font size label | Token |
|---|---|---|---|---|
| `sm` | 20px | 4px | `--text-xs` (12px) | — |
| `md` | 32px | 8px | `--text-sm` (14px) | — |
| `lg` | 48px | 12px | `--text-base` (16px) | — |

### Cores

| Elemento | Token | Valor |
|---|---|---|
| Spinner arco ativo | `--color-primary-base` | #C4000B |
| Spinner arco fundo | `--color-border-base` | #E5E7EB |
| Bar track (fundo) | `--color-bg-muted` | #F3F4F6 |
| Bar fill (progresso) | `--color-primary-base` | #C4000B |
| Label texto | `--color-text-secondary` | #6B7280 |
| Overlay fundo | `rgba(0, 0, 0, 0.4)` | — |

### Verificação de contraste (WCAG AA)

| Elemento | Cor | Fundo | Ratio | Resultado |
|---|---|---|---|---|
| Spinner arco sobre branco | #C4000B | #FFFFFF | ~5.2:1 | ✅ AA |
| Label texto | #6B7280 | #FFFFFF | ~4.6:1 | ✅ AA |
| Spinner arco sobre overlay | #C4000B | rgba(0,0,0,0.4) | verificar por composição | ✅ (arco é sobre fundo escurecido) |

### Dimensões

| Propriedade | SM | MD | LG | Token |
|---|---|---|---|---|
| Spinner diâmetro | 20px | 32px | 48px | — |
| Spinner stroke width | 2px | 3px | 4px | — |
| Bar track height | 4px | 8px | 12px | — |
| Bar track border-radius | `--radius-full` | `--radius-full` | `--radius-full` | — |
| Bar width | 100% (fill container) | 100% | 100% | — |
| Gap spinner/bar → label | 8px | 8px | 12px | `--space-2` / `--space-3` |
| Label font weight | 400 | 400 | 400 | `--font-regular` |

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução neste spec |
|---|---|---|
| Acessibilidade (wcag) | Spinner sem label — sem texto alternativo para screen readers | `label` é prop **obrigatória**. Sempre presente como `aria-label` no elemento. `showLabel = true` por padrão exibe o texto visualmente. Quando `showLabel = false`, label fica como `aria-label` invisível (sr-only) |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução neste spec |
|---|---|---|
| H-1 Visibilidade (**crit**) | Spinner sem label — usuário não sabe o que está carregando | Label textual obrigatório e visível por padrão. Exemplos: "Carregando boletins...", "Processando consulta...", "Enviando anexos... 45%". Label contextualiza a espera |
| H-4 Consistência (aten) | Sem padrão documentado de quando usar spinner vs. bar | Guia de uso documentado: spinner para operações sem progresso mensurável, bar para operações com progresso (upload, batch). Padrão consistente em todos os componentes do DS |
| H-6 Reconhecimento (aten) | Sem feedback do tipo de operação em andamento | Label descreve a operação ("Carregando...", "Enviando..."). Para bar determinada, percentual visível (ex: "45%"). Usuário reconhece o que está acontecendo |
| H-8 Estética (aten) | Visual genérico | Spinner com arco `--color-primary-base`, bar com fill suave, border-radius pill, espaçamento por tokens. Animação suave (rotation 1s linear infinite para spinner, sweep 1.5s ease-in-out infinite para bar indeterminada) |
| H-9 Recuperação (aten) | Sem timeout documentado — loading pode durar infinitamente | Recomendação de uso: implementar timeout no código (15s sugestão). Após timeout, substituir loader por mensagem de erro (usar BC-03 Alert tipo Danger). Loader em si não implementa timeout — é responsabilidade do código consumidor |

---

## Regras de acessibilidade

- [ ] Spinner/bar com `role="progressbar"`
- [ ] `aria-label` com o texto do label — **sempre presente**, mesmo quando `showLabel = false`
- [ ] Quando `progress` definido: `aria-valuenow`, `aria-valuemin="0"`, `aria-valuemax="100"`
- [ ] Quando indeterminado: sem `aria-valuenow` (screen reader anuncia como indeterminado)
- [ ] Container com `aria-busy="true"` enquanto loader ativo — marca a região como carregando
- [ ] `aria-live="polite"` no label para anunciar mudanças de texto (ex: percentual atualizado)
- [ ] Animação respeita `prefers-reduced-motion` — quando ativado, spinner não gira (exibe arco estático + label), bar não anima (exibe fill estático)
- [ ] Contraste mínimo 3:1 para elementos gráficos (spinner arco: 5.2:1 ✅)
- [ ] Label com contraste mínimo 4.5:1 (6B7280 sobre FFFFFF: 4.6:1 ✅)

---

## Comportamentos esperados

- Quando `type = 'spinner'` → arco circular gira com `animation: rotate 1s linear infinite`. Arco ocupa 270° (3/4 do círculo)
- Quando `type = 'bar'` sem `progress` → barra com animação de varredura indeterminada (sweep left-to-right, 1.5s ease-in-out infinite)
- Quando `type = 'bar'` com `progress` → barra preenchida até o percentual indicado. Transição suave ao atualizar (`transition: width 300ms ease`)
- Quando `showLabel = true` → label visível abaixo do spinner/bar
- Quando `showLabel = false` → label invisível visualmente, presente como `aria-label`
- Quando `overlay = true` → loader renderiza centralizado sobre overlay semitransparente que cobre o container pai. Interação bloqueada (pointer-events: none no conteúdo abaixo). Focus trap no loader
- Quando `progress` muda → bar atualiza suavemente + label com percentual atualiza + `aria-valuenow` atualiza
- Quando `prefers-reduced-motion` ativo → animação pausa, exibe indicador estático + label

---

## Composição com outros componentes

| Componente | Relação | Composição no Figma (Regra 11) |
|---|---|---|
| BC-05 Buttons | Buttons têm estado Loading — spinner SM inline substituindo o label | Spinner SM pode ser usado dentro de Button loading state |
| BC-06 Cards | Loader dentro de card enquanto conteúdo carrega | Composição de uso |
| BC-25 Tables | Variante Loading do Table já usa skeleton — loader bar pode complementar | Composição de uso |
| BC-19 Modals | Loader pode aparecer dentro de modal durante processamento | Composição de uso |
| SC-02/03/04 Consultas | Consultas policiais usam loader durante busca | Composição de uso |

---

## Mapeamento de retrocompatibilidade

| Prop atual | Mapeamento novo | Nota |
|---|---|---|
| `type: "spinner"\|"bar"` | `type` | Mantido |
| `size` | `size` (agora com valores definidos: sm/md/lg) | Padronizado |
| `label` | `label` (agora **obrigatório**) | Breaking: label passa a ser obrigatório. Sem label = inacessível |
| — | `showLabel` (novo) | Permite ocultar label visualmente mantendo acessibilidade |
| — | `progress` (novo) | Barra de progresso determinada |
| — | `overlay` (novo) | Modo overlay com bloqueio de interação |

---

## Casos excepcionais / bordas

- **Sem label (label vazio):** componente não renderiza (validação Angular). Label é obrigatório
- **Progress > 100 ou < 0:** clampado para 0–100
- **Progress = 100:** bar completa. Recomendação: remover loader e exibir feedback de sucesso (Toast ou Alert)
- **Overlay em mobile:** overlay ocupa 100% da viewport. Touch bloqueado. Spinner LG centralizado
- **Múltiplos loaders simultâneos:** apenas 1 overlay por vez. Loaders inline podem coexistir
- **Loader dentro de Button:** usar tamanho SM, `showLabel = false`, cor branca (invertida sobre fundo primário)
- **Label longo:** trunca com ellipsis se exceder largura do container. `title` com texto completo

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-primary-base` | Arco do spinner, fill da bar |
| `--color-border-base` | Arco fundo do spinner |
| `--color-bg-muted` | Track da bar |
| `--color-text-secondary` | Texto do label |
| `--radius-full` | Border radius da bar (pill) |
| `--font-body` | Família tipográfica |
| `--text-xs / --text-sm / --text-base` | Font size por tamanho |
| `--font-regular` | Peso do label (400) |
| `--space-2 / --space-3` | Gap entre indicador e label |

---

## O que está fora deste spec

- **Skeleton loaders:** BC-18 Skeleton Layers é componente separado (placeholder de conteúdo, não indicador de operação)
- **Loading state de Button:** pertence ao spec de BC-05 Buttons — usa spinner SM como composição
- **Pull-to-refresh:** padrão mobile específico, não do componente base
- **Loading com cancelamento (botão Cancelar):** composição de uso — combinar loader com BC-05 Button Tertiary
- **Animação de sucesso (checkmark):** transição pós-loading — pode ser adicionada como variante futura

---

## Critérios de aceite

- [ ] 2 tipos (Spinner, Bar) no Figma
- [ ] 3 tamanhos (SM, MD, LG) documentados
- [ ] Label obrigatório — resolve H-1 crit (spinner sem feedback)
- [ ] Bar com progresso determinado (preenchimento parcial) e indeterminado (varredura animada) documentados
- [ ] Variante overlay documentada
- [ ] ARIA documentado: `role="progressbar"`, `aria-label`, `aria-valuenow`, `aria-busy`
- [ ] `prefers-reduced-motion` documentado
- [ ] Contraste verificado — spinner 5.2:1 AA, label 4.6:1 AA
- [ ] Violação WCAG (spinner sem label) resolvida
- [ ] Violações Nielsen (H-1 **crit** · H-4 aten · H-6 aten · H-8 aten · H-9 aten) resolvidas
- [ ] Mapeamento de retrocompatibilidade documentado
- [ ] Revisado e aprovado por Giuliana
