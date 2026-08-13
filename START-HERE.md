# START-HERE — DS SISP

Este e o primeiro arquivo que o agente le ao abrir este projeto.

---

## Estado atual do projeto (2026-08-12)

**Fase:** D2 — Building · Sprint 8 em andamento
**O que ja existe:** Discovery completo · Analises WCAG + Nielsen · Pesquisa qualitativa (3 perfis) · Sprint plan · Tokens CSS · 6 decisoes de design resolvidas · Sprints 0–7 + R1 entregues · 60 Component Sets no Figma · 108 variaveis Figma · 21 Text Styles · 4366+ bindings verificados · 56 component-specs aprovadas · Taxonomia atualizada · Prototipos DV interativos (HTML)
**Proxima acao imediata:** Sprint 8 — Prototipo navegavel Figma (flows interativos Login → Dashboard → Criar BO)

---

## Instrucao para o agente

Voce esta operando dentro do GiuOS aplicado ao projeto **DS SISP** — redesign do Design System do Sistema Integrado de Seguranca Publica (CiASC / Governo de SC).

**Sua primeira tarefa e se orientar, nao executar.**

### Passo 1 — Leia estes arquivos, nesta ordem:

1. `CLAUDE.md` — contrato do sistema, estado atual, decisoes ja tomadas
2. `docs/PRD.md` — o que esta sendo construido e por que
3. `docs/DISCOVERY-SYNTHESIS.md` — o que foi descoberto na pesquisa
4. `design-tokens/tokens.css` — sistema visual ja definido
5. `session-log/INDEX.md` — todas as decisoes tomadas ate aqui (193 entradas)
6. `docs/analyses/wcag-analysis.md` e `nielsen-analysis.md` — violacoes mapeadas

### Passo 2 — Identifique o contexto:

Responda internamente antes de interagir:
- Estamos no Sprint 8 — qual o foco? (Prototipo navegavel + componentes adicionais)
- Quais tokens CSS e variaveis Figma estao disponiveis? (108 variaveis, 4 colecoes)
- Qual e a audiencia real deste DS? (ver CLAUDE.md → core-personas)
- Quais componentes tem prioridade? (DV primeiro — Regra 10)
- Qual o file key do Figma? (`YUSNqTRVZTK2eV7D3fXypx`)

### Passo 3 — Apresente-se a Giuliana:

Apos ler tudo, responda com:
1. Confirmacao de que leu e entendeu o estado do projeto
2. O que esta em andamento no sprint atual
3. Uma unica pergunta, se necessario

---

## Comportamento especifico para DS SISP

### Para component specs:
- Ler `docs/analyses/wcag-analysis.md` antes de qualquer spec
- Ler `docs/analyses/nielsen-analysis.md` antes de qualquer spec
- Cada spec resolve violacoes WCAG AA **e** Nielsen do componente
- Usar `docs/component-specs/_template.md` como base
- 56 specs aprovadas (`docs/component-specs/`)

### Para componentes no Figma:
- Figma file key: `YUSNqTRVZTK2eV7D3fXypx`
- Verificar se existe referencia no Figma via Figma MCP antes de criar
- Tokens Figma devem espelhar `design-tokens/tokens.css` — 108 variaveis migradas
- Nunca criar componente sem component-spec aprovada (Regra 1)
- Todas as propriedades visuais devem ser vinculadas a variaveis (Regra 8)
- Text Styles aplicados em todos os text nodes — 21 estilos disponiveis (Regra 15)
- Composicao atomica obrigatoria (Regras 11/12)
- Responsividade obrigatoria para componentes complexos (Regra 13)

### Para Angular (Sprint 10):
- Convencao: `sisp-lib-[nome]` para componentes
- Convencao: `sispLib[Nome]Config` para parametros de configuracao
- Bootstrap como base CSS — tokens customizados como override
- Maximo 1200px de largura de layout

---

## Referencias rapidas

| Item | Valor |
|---|---|
| Stage DS SISP | `sisp-design-system-stage.apps.okd4.ciasc.sc.gov.br` |
| Figma file key | `YUSNqTRVZTK2eV7D3fXypx` |
| Produto-ancora | Delegacia Virtual (DV) |
| Cor de acao | #C4000B (vermelho SC) |
| Tipografia portal | Montserrat |
| Tipografia componentes | Arial (Arimo no Figma cloud) |
| Tokens CSS | `design-tokens/tokens.css` |
| Variaveis Figma | 108 variaveis em 4 colecoes |
| Text Styles | 21 estilos (Heading, Overline, Body, Label, Mono) |
| Component Sets | 60 (27 BC + 19 SC + 10 DC + 4 extras) |
| Component Specs | 56 aprovadas (`docs/component-specs/`) |
| Violacoes WCAG | `docs/analyses/wcag-analysis.md` |
| Violacoes Nielsen | `docs/analyses/nielsen-analysis.md` |
| Session log | `session-log/INDEX.md` (193 decisoes) |
