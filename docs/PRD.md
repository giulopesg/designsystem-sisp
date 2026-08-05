---
status: approved
approved-by: Giuliana Lopes Galvão
approved-date: 2026-06-22
cycle: D2 — building
sprint: 1
---

# PRD — DS SISP

**Projeto:** Design System SISP  
**Cliente:** CiASC / COSEG / Governo de Santa Catarina  
**Contrato gerido por:** Daniel San Martin Pascal Filho  
**Consultora responsável:** Giuliana Lopes Galvão · Meloon

---

## Problema

O CiASC mantém um ecossistema de sistemas de segurança pública (SISP) há ~20 anos. Um desenvolvedor (Leonardo Demilis) criou um UI Kit Angular por iniciativa própria para padronizar os produtos. O artefato existe mas:

- Não tem mandato institucional — "quando for batido o martelo" (Holiwod Borges, PO técnico)
- Não tem documentação formal — o padrão vive na cabeça dos desenvolvedores veteranos
- Falha com terceiros — caso comprovado: integração IPEM por terceiro criou front-end fora do padrão → meses de atraso
- Não tem governança — Demilis é o único guardião do front-end, o que é um risco crítico (Sommer: "delegar a uma única pessoa em um sistema tão crítico é um risco")
- Não tem tokens — cores extraídas visualmente, sem sistema formal
- Não tem temas — múltiplos clientes (PC, PM, CBM) com identidades próprias não têm como customizar sem quebrar o sistema

O DS precisa se tornar um **produto real**: com governança, tokens, componentes documentados, e um processo de adoção que funcione para devs internos, POs, e terceiros contratados.

---

## Personas afetadas

| Persona | Papel | Necessidade |
|---|---|---|
| Leonardo Demilis | Criador/mantenedor · gatekeeper front-end | Especificação formal que ele possa validar e implementar |
| 6 devs CiASC | Consumidores diretos | Consulta rápida: "como usar este componente" |
| Felipe Sommer / Holiwod Borges | POs | Visibilidade do que existe e está padronizado — para tomar decisões |
| Terceiros contratados | Sem contexto histórico | Documentação clara o suficiente para não criar fora do padrão |
| Devs da Polícia Civil | Clientes externos com sistemas próprios | Theming e componentes reutilizáveis sem depender do CiASC |

---

## Apetite

**Ciclo completo de produto** — entrega incremental por sprints com validação ao final de cada fase:

- Sprint 1: entregáveis estratégicos em HTML (sitemap, user journey, wireframes)
- Sprints 2–7: componentes no Figma (fundação + 44 componentes)
- Sprints 8–10: layouts, protótipo, testes
- Sprint 11–12: testes de usabilidade + refatoração HTML/CSS Angular

Produto-âncora: **Delegacia Virtual** — DV primeiro em toda decisão de prioridade.

---

## Solução esboçada

Um Design System completo com:

**Portal de documentação** com 7 seções:
1. Sobre o DS (governança, changelog, como contribuir)
2. Fundação (tokens de cor, tipografia, espaçamento)
3. Acessibilidade (WCAG AA por componente)
4. Base Components (28 componentes documentados)
5. SISP Components (16 componentes específicos)
6. Temas (override por cliente — PC, PM, CBM)
7. Figma (UI Kit, Dev Mode, mapa tokens)

**Sistema de tokens** em dois níveis:
- Primitivos: valores brutos da paleta
- Semânticos: intenção que os componentes consomem (permite theming por cliente)

**Figma UI Kit** com todos os componentes, estados, variantes e tokens vinculados.

**Refatoração Angular** dos componentes existentes com correções de WCAG AA e heurísticas Nielsen.

---

## Rabbit holes

- **Temas por cliente** podem virar um projeto separado — escopo: documentar o sistema de tokens e criar um exemplo de override (PC). Não construir todos os temas.
- **Refatoração Angular completa** pode ultrapassar o apetite — priorizar DV + Base Components críticos.
- **BC-08 Charts e SC-16 Relatório de Consultas** precisam de decisão com Demilis sobre biblioteca antes de entrar no Figma — não bloquear outros sprints esperando essa decisão.
- **SC-05 Pesquisa de Objetos e SC-08 Login** estavam inacessíveis no inventário — catalogar antes de especificar.

---

## No-gos

- **Redesign dos produtos** (DV, Conecta, BO Integrado) — este projeto é o DS, não os produtos que o usam
- **Benchmark analysis** — não contratado, não entra
- **Estimativas de esforço de implementação** — fora do escopo da consultoria
- **Cidadão como usuário do DS** — o DS é para desenvolvedores e designers, não para o usuário final dos produtos SISP

---

## Critérios de sucesso

1. Todos os 44 componentes documentados no Figma com props, estados e notas de acessibilidade
2. Sistema de tokens CSS implementado — zero valores hardcoded nos componentes
3. Cada componente resolve as violações WCAG AA e Nielsen documentadas nas análises
4. Portal de documentação navegável e entregue
5. Processo de contribuição documentado — um terceiro pode usar o DS sem precisar falar com Demilis

---

## Contexto de pesquisa (D1 completo)

**Entrevistados:**
- Perfil A: Leonardo Demilis (CiASC) — criador do UI Kit
- Perfil B: Felipe Sommer + Holiwod Borges (CiASC) — POs
- Perfil C: Diego Coradini + Fábio Xavier (Polícia Civil de SC) — usuários/clientes

**Achados que definem este PRD:**
- UI Kit existe mas não é usado formalmente
- Padrão tácito = risco: funciona com time estável, falha com terceiros
- PC não sabia o que era DS mas reagiu positivamente ao conceito
- Rastreabilidade inexistente — todas as decisões de produto são 100% qualitativas
- Múltiplos clientes com identidades próprias precisam de theming
- DV confirmada como produto-âncora pelos três perfis

**Documentos de referência:**
- `docs/DISCOVERY-SYNTHESIS.md`
- `docs/analyses/wcag-analysis.md`
- `docs/analyses/nielsen-analysis.md`
