---
component-id: SC-01
component-name: Atualizações Recentes
type: SISP
status: approved
sprint: 7
approved-by: [Giuliana]
approved-date: [2026-08-04]
figma-node-id: [804:6991]
---

# Component Spec — Atualizações Recentes

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → SC-01 (cor aten · vis aten)
> - `docs/analyses/nielsen-analysis.md` → SC-01 (H-1 aten · H-4 aten · H-6 aten · H-8 aten — sem críticos)
> - `docs/analyses/inventory.md` → SC-01
> - Screenshot de produção: `uikit-screencapture/...atualizacoes-recentes...2026-06-02-19_40_51.png`

---

## O que é

Atualizações Recentes é o componente que exibe o histórico de atualizações do sistema SISP. Funciona como um feed cronológico das mudanças do portal (ex: migração de versão do framework, correções, novos componentes). Auto-suficiente via BFF — não recebe configuração externa.

---

## Audiência de uso

- **Policial na DV:** visualiza atualizações do sistema para entender mudanças recentes na interface
- **Devs CiASC / terceiros:** adicionam na tela sem configuração. Componente consome dados diretamente do BFF
- **POs (Sommer/Holiwod):** canal de comunicação institucional sobre atualizações do DS

---

## Props / API

> **Padrão de API:** Auto-suficiente via BFF (sem props).

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| — | — | — | — | Nenhuma prop — auto-suficiente |

> ⚠️ **Inventário:** doc diz "expandir/recolher" mas preview mostra paginação (3 itens/página). Esclarecer com Demilis qual é o comportamento real.

**Convenção Angular (auto-suficiente):**
```html
<sisp-lib-recent-updates></sisp-lib-recent-updates>
```

---

## Anatomia do componente

```
┌──────────────────────────────────────────────────────────────┐
│  🔄 Atualizações Recentes                                   │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  Data/Hora              Descrição                            │
│  ─────────────────────  ───────────────────────────────────  │
│  10/09/2023, 21:00      Atualização do portal para...        │
│  10/09/2023, 21:00      Atualização do portal para...        │
│  10/08/2023, 21:00      Atualização do portal para...        │
│                                                              │
│  « 1 [2] »                            3 itens por página ▾   │
│  (paginação)                          (BC-13 Select)         │
└──────────────────────────────────────────────────────────────┘
```

### Composição atômica (Regra 11)

| Elemento | Componente DS | Instância |
|---|---|---|
| Título do card | Heading + ícone | Heading/MD + BC-15 Icons SM |
| Tabela de atualizações | BC-25 Table (simplificada) | 1× — 2 colunas (Data/Hora, Descrição) |
| Paginação | Buttons + Select | BC-05 Button SM (« 1 2 ») + BC-13 Select SM ("3 itens por página") |
| Container | Card | Frame com tokens (surface, border, radius) |

> **Regra 12 — Auditoria:** Tabela usa padrão BC-25 Table (2 colunas, sem seleção, sem checkbox). Paginação pode ser pattern Bootstrap nativo — confirmar se existe como componente no DS ou se é padrão manual.

---

## Estados e variantes

| Estado | Descrição visual | Composição |
|---|---|---|
| **Preenchido** | Tabela com N itens, paginação visível (se > pageSize) | Header + tabela + paginação |
| **Vazio** | Nenhuma atualização registrada | Mensagem "Nenhuma atualização recente" centralizada |
| **Loading** | Carregando dados do BFF | Skeleton (BC-18) ou Spinner (BC-16) no container |
| **Erro** | Falha no BFF | Alert Danger (BC-03) inline |

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução neste spec |
|---|---|---|
| Uso de cor (cor aten) | Sem indicação — componente simples | **Não depende de cor** para transmitir informação. Data/Hora é texto + Descrição é texto. Sem uso de cor como canal de informação |
| Visual (vis aten) | Estados Loading/Erro não documentados | **3 estados não-default explícitos:** Loading (Skeleton/Spinner), Vazio (mensagem), Erro (Alert). Focus ring em links de paginação |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução neste spec |
|---|---|---|
| H-1 Visibilidade (aten) | Sem feedback durante carregamento | **Skeleton** (BC-18 Text) ou **Spinner** (BC-16) enquanto BFF responde. Contador total: "Mostrando 1-3 de 12 atualizações" |
| H-4 Consistência (aten) | Auto-suficiente (vs. config object) | OK — padrão documentado. Componente informativo não precisa de config |
| H-6 Reconhecimento (aten) | Sem indicação do total de atualizações | **Contador** "Mostrando X-Y de N" + paginação. Último update com destaque visual sutil (font-weight bold ou badge "Novo") |
| H-8 Estética (aten) | Layout Bootstrap básico | Tokens DS SISP: `--color-surface`, `--font-body`, `--space-*`. Ícone no título com BC-15 Icons |

---

## Regras de acessibilidade

- [ ] Tabela com `<caption>` "Atualizações recentes do sistema"
- [ ] Colunas com `<th scope="col">` ("Data/Hora", "Descrição")
- [ ] Paginação com `role="navigation"` e `aria-label="Paginação de atualizações"`
- [ ] Botão de página atual com `aria-current="page"`
- [ ] Select "itens por página" com `aria-label="Quantidade de itens por página"`
- [ ] `aria-live="polite"` no container da tabela (anuncia mudança de página)
- [ ] Focus ring visível nos botões de paginação
- [ ] Contraste 4.5:1 AA
- [ ] Labels em português

---

## Comportamentos esperados

### Carregamento
- Quando componente inicia → Loading: Skeleton ou Spinner enquanto BFF responde
- Quando BFF retorna dados → Preenchido: tabela com N itens, paginação se > pageSize
- Quando BFF retorna vazio → Vazio: "Nenhuma atualização recente"
- Quando BFF retorna erro → Erro: Alert Danger com "Não foi possível carregar as atualizações"

### Paginação
- Quando clica em número de página → carrega itens da página correspondente. Scroll para topo da tabela
- Quando muda "itens por página" → recarrega com novo pageSize. Volta para página 1
- Quando na última página → botão "»" desabilitado
- Quando na primeira página → botão "«" desabilitado

---

## Comportamento responsivo

| Precisa de variante `Layout=Mobile`? | **Não** — tabela de 2 colunas simples, adapta com width 100% |
|---|---|
| **Desktop** | Tabela full-width com 2 colunas. Paginação na esquerda, selector de itens na direita |
| **Mobile** | Mesma estrutura. Coluna "Data/Hora" pode truncar. Paginação empilha se necessário |
| **Tablet** | Segue Desktop |

> Componente com 2 colunas textuais — auto-contido e responsivo por natureza.

---

## Variantes no Figma

| Variante | Property | Descrição |
|---|---|---|
| **Default** | `State=Default` | Tabela com 3 itens + paginação |
| **Empty** | `State=Empty` | Mensagem "Nenhuma atualização recente" |
| **Loading** | `State=Loading` | Skeleton na área da tabela |

> 3 variantes. Sem variante Mobile — tabela 2 colunas é auto-contida.

---

## Tokens utilizados

| Token | Uso |
|---|---|
| `--color-surface` | Fundo do card |
| `--color-border` | Borda do card, linhas da tabela |
| `--color-text-primary` | Texto da descrição, cabeçalhos |
| `--color-text-secondary` | Data/Hora |
| `--color-primary` | Botão de página ativa |
| `--color-border-focus` | Focus ring |
| `--font-body` | Textos |
| `--text-sm` | Data/Hora, paginação |
| `--text-base` | Descrição |
| `--space-4` | Padding do card |
| `--space-3` | Gap entre linhas |
| `--radius-lg` | Card |
| `--shadow-sm` | Sombra do card |

---

## Casos excepcionais / bordas

- **Sem atualizações:** estado Vazio com mensagem amigável
- **Muitas atualizações:** paginação server-side. BFF controla total e páginas
- **Texto muito longo na descrição:** truncar com `...` após 2 linhas. Tooltip com texto completo ao hover
- **Data muito antiga:** formato dd/mm/yyyy, HH:mm. Sem "X dias atrás" — manter formato absoluto
- **Inventário: expandir/recolher vs. paginação:** screenshot mostra paginação (« 1 2 »). Manter paginação como padrão. Se "expandir/recolher" existir, documentar como variante futura

---

## O que está fora deste spec

- **Push notifications:** atualizações em tempo real via WebSocket — funcionalidade futura
- **Filtro por tipo de atualização:** não existe no componente atual
- **Detalhe da atualização:** não há tela de detalhe — cada item é uma linha na tabela
- **Padrão de paginação reutilizável:** candidato a micro-componente futuro (usado por SC-01 e potencialmente outros)

---

## Critérios de aceite

- [ ] Tabela com 2 colunas: Data/Hora e Descrição
- [ ] Paginação com seletor "itens por página"
- [ ] Estados: Preenchido, Vazio, Loading, Erro
- [ ] Instâncias de BC-25 Table (simplificada), BC-05 Button, BC-13 Select, BC-03 Alert, BC-16/18 Loader/Skeleton (Regra 11)
- [ ] Tokens aplicados (Regra 8)
- [ ] WCAG (cor aten · vis aten) resolvidas
- [ ] Labels em português
- [ ] Revisado e aprovado por Giuliana
