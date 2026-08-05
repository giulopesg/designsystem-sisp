# START-HERE — DS SISP

Este é o primeiro arquivo que o agente lê ao abrir este projeto.

---

## Estado atual do projeto (2026-07-16)

**Fase:** D2 — Building · Sprint 4 completo · Sprint 4.1 planejado · Sprint 5 próximo
**O que já existe:** Discovery completo · Análises WCAG + Nielsen · Pesquisa qualitativa (3 perfis) · Sprint plan · Tokens CSS · 6 decisões de design resolvidas · Sprints 0–4 entregues · 18 Component Sets no Figma (113+ variantes) · 99 variáveis Figma · 20 Text Styles · 2.314 bindings verificados · 13 component-specs aprovadas · Taxonomia atualizada (13 "Figma ✅", 13 "Sprint 4.1", 1 "Sprint 7")
**Próxima ação imediata:** Sprint 4.1 — 13 Base Components restantes (specs + Figma) OU Sprint 5 — SISP Components DV

---

## Instrução para o agente

Você está operando dentro do GiuOS aplicado ao projeto **DS SISP** — redesign do Design System do Sistema Integrado de Segurança Pública (CiASC / Governo de SC).

**Sua primeira tarefa é se orientar, não executar.**

### Passo 1 — Leia estes arquivos, nesta ordem:

1. `CLAUDE.md` — contrato do sistema, estado atual, decisões já tomadas
2. `docs/PRD.md` — o que está sendo construído e por quê
3. `docs/DISCOVERY-SYNTHESIS.md` — o que foi descoberto na pesquisa
4. `design-tokens/tokens.css` — sistema visual já definido
5. `session-log/INDEX.md` — todas as decisões tomadas até aqui
6. `docs/analyses/wcag-analysis.md` e `nielsen-analysis.md` — violações mapeadas (recuperar do Google Drive se não estiverem aqui)

### Passo 2 — Identifique o contexto:

Responda internamente antes de interagir:
- Sprint 4.1 tem 13 Base Components pendentes — qual a prioridade? (DV primeiro)
- Estamos em transição Sprint 4→4.1→5 — quais componentes entram agora?
- Quais tokens CSS e variáveis Figma estão disponíveis?
- Qual é a audiência real deste DS? (ver CLAUDE.md → core-personas)
- Quais componentes têm prioridade? (DV primeiro — ver sprint plan)
- Qual o file key do Figma? (ver CLAUDE.md → `figma-file-key`)

### Passo 3 — Apresente-se a Giuliana:

Após ler tudo, responda com:
1. Confirmação de que leu e entendeu o estado do projeto
2. O que está em andamento no sprint atual
3. Uma única pergunta, se necessário

---

## Comportamento específico para DS SISP

### Para entregáveis HTML (Sprint 1 — concluído):
- Usar tokens de `design-tokens/tokens.css` — sem valores hardcoded
- Audiência: cliente CiASC / Valmor / Vincent — linguagem limpa, sem jargão técnico
- O sitemap e user journey são do DS como produto — não da DV nem do Conecta

### Para component specs:
- Ler `docs/analyses/wcag-analysis.md` antes de qualquer spec
- Ler `docs/analyses/nielsen-analysis.md` antes de qualquer spec
- Cada spec resolve violações WCAG AA **e** Nielsen do componente
- Usar `docs/component-specs/_template.md` como base
- 13 specs aprovadas (Sprint 3: 6, Sprint 4: 7)

### Para componentes no Figma:
- Figma file key: `YUSNqTRVZTK2eV7D3fXypx`
- Verificar se existe referência no Figma via Figma MCP antes de criar
- Tokens Figma devem espelhar `design-tokens/tokens.css` — 99 variáveis migradas
- Nunca criar componente sem component-spec aprovada
- Todas as propriedades visuais devem ser vinculadas a variáveis (Regra 8)
- Text Styles aplicados em todos os text nodes (20 estilos disponíveis)
- Composição atômica obrigatória (Regras 11/12)

### Para Angular (Sprint 12):
- Convenção: `sisp-lib-[nome]` para componentes
- Convenção: `sispLib[Nome]Config` para parâmetros de configuração
- Bootstrap como base CSS — tokens customizados como override
- Máximo 1200px de largura de layout

---

## Referências rápidas

| Item | Valor |
|---|---|
| Stage DS SISP | `sisp-design-system-stage.apps.okd4.ciasc.sc.gov.br` |
| Figma file key | `YUSNqTRVZTK2eV7D3fXypx` |
| Produto-âncora | Delegacia Virtual (DV) |
| Cor de ação | #C4000B (vermelho SC) |
| Tipografia portal | Montserrat |
| Tipografia componentes | Arial (Arimo no Figma cloud) |
| Tokens CSS | `design-tokens/tokens.css` |
| Variáveis Figma | 99 variáveis em 4 coleções |
| Text Styles | 20 estilos (Heading, Body, Label, Mono) |
| Component Sets | 18 (Sprint 3: 10, Sprint 4: 8) |
| Component Specs | 13 aprovadas (`docs/component-specs/`) |
| Violações WCAG | `docs/analyses/wcag-analysis.md` |
| Violações Nielsen | `docs/analyses/nielsen-analysis.md` |
