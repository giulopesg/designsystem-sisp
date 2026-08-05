---
status: complete
phase: D1 — Discover + Define
last-updated: 2026-06-22
profiles-interviewed: 3
hypotheses-total: 15
hypotheses-confirmed: 11
hypotheses-refuted: 1
hypotheses-open: 3
---

# Discovery Synthesis — DS SISP

---

## Contexto do Discovery

O CiASC mantém o SISP há ~20 anos. Um desenvolvedor criou um UI Kit Angular por iniciativa própria. A Meloon foi contratada para redesenhá-lo. Antes de qualquer trabalho de design, conduzimos:

1. Inventário completo de 44 componentes (28 Base + 16 SISP)
2. Análise WCAG AA por componente
3. Análise heurística Nielsen por componente
4. Pesquisa qualitativa com 3 perfis

---

## Perfis entrevistados

### Perfil A — Leonardo Demilis (CiASC)
Criador do UI Kit. Referência técnica principal. Gatekeeper de front-end.

**Achados principais:**
- UI Kit foi iniciativa pessoal, não demanda institucional
- Não existe arquivo Figma — nunca existiu
- Não existem tokens formais — cores extraídas visualmente do sc.gov.br
- O kit foi apresentado ao time mas não tem mandato de uso obrigatório
- A DV está ~90% migrada para o UI Kit (não em produção ainda)
- Stack: Angular (monorrepo) + Bootstrap + Font Awesome Free
- Convenção: `sisp-lib-[nome]` para componentes, `sispLib[Nome]Config` para parâmetros

### Perfil B — Felipe Sommer + Holiwod Borges (CiASC)
POs. Sommer: gestão de produto, DV + IPEM/Conecta. Holiwod: PO técnico, integrações externas.

**Achados principais:**
- Nenhum dos dois usa o UI Kit. "Não uso para nada." (Sommer)
- "Quando for batido o martelo" — sem mandato, sem adoção (Holiwod)
- A documentação do sistema é a cabeça dos desenvolvedores veteranos
- **Caso concreto de falha:** integração IPEM por terceiro → criou front-end fora do padrão → meses de atraso. Prova real da necessidade de documentação.
- Sommer: "delegar a uma única pessoa num sistema tão crítico é um risco" — referindo-se à dependência de Demilis
- DV confirmada como produto-âncora: "a nossa vitrine pro cidadão catarinense"
- Analytics nunca implementado — todas as decisões são 100% qualitativas
- DV tem 3 idiomas: PT, EN, ES — componentes precisam suportar isso
- Time: 6 devs servidores de carreira + terceiros contratados

**Ciclo dos sistemas satélites (documentado por Holiwod):**
Instituições criam sistemas próprios → abandonam por falta de capacidade técnica → retornam ao CiASC.
- IPD (Polícia Civil — inquérito policial digital)
- RG / emissão de identidade (Polícia Científica)
- Sistema Bravo / LPR (Polícia Militar — reconhecimento de placa) — "aos frangalhos" atualmente

### Perfil C — Diego Coradini + Fábio Xavier (Polícia Civil de SC)
Coradini: inteligência, Delegacia Geral. Fábio Xavier: suporte/analista, bridge PC↔CiASC.

**Achados principais:**
- **Não sabiam o que era DS** — Giuliana teve que explicar com exemplos (Gov.br DS). Reação positiva.
- Coradini: "fantástico — já temos normativa interna de identidade visual"
- Processo de demanda: GLPI (helpdesk interno PC) → email → JIRA no CiASC. Fábio filtra e triages.
- PC tem devs próprios e sistemas próprios (com integração ao CISP)
- Comissão formal criada para modernização da DV — não foi demanda informal
- **Tripé de prioridades da PC para a DV:** validação de identidade + automatização de fatos não-criminais + agilização da homologação
- **Caso crítico:** medidas protetivas de urgência (violência doméstica) perdidas na fila geral → risco real
- UX do cidadão reconhecida como ruim: "a usabilidade da DV é ruim. É uma pena." — mas não é prioridade atual
- PC e Fábio trabalham em unidades administrativas, não em campo — perguntas sobre operação de campo não se aplicam a eles
- BO Integrado = subsistema separado, acessível apenas por VPN/intranet

---

## Mapa de achados críticos

### 1. O produto não tem mandato — o artefato não se sustenta sozinho
**Tipo:** DADO REAL  
O UI Kit foi iniciativa pessoal do Demilis. Sem decisão institucional de uso obrigatório, nenhum dev é obrigado a usá-lo. O DS precisa de governança.

### 2. O padrão tácito é um risco invisível
**Tipo:** DADO REAL  
O "padrão" existe na cabeça de 2-3 devs veteranos. Funciona enquanto o time é estável. Falha com terceiros. Caso comprovado com IPEM.

### 3. Demilis é gatekeeper e isso é um risco
**Tipo:** DADO REAL  
Front-end = Demilis. O que ele aprova, o time aceita. Se ele sair, o conhecimento vai junto. O DS como produto precisa distribuir esse conhecimento.

### 4. DV é o produto-âncora por todos os ângulos
**Tipo:** DADO REAL  
Confirmado pelos três perfis: é a vitrine do cidadão, está mais migrada, tem demanda ativa da PC, tem documento de requisitos em execução.

### 5. Múltiplos clientes com identidades próprias
**Tipo:** DADO REAL  
PC tem dourado, normativa interna de identidade visual. PM tem a sua. O DS precisa de sistema de temas para não forçar todos a usar a mesma identidade visual.

### 6. A PC quer autonomia mas não tem capacidade técnica
**Tipo:** DADO REAL  
PC tem devs. Criou IPD. Mas os sistemas satélites eventualmente voltam para o CiASC por falta de manutenção. DS com theming pode dar autonomia controlada.

### 7. Rastreabilidade inexistente
**Tipo:** DADO REAL  
Sem analytics em nenhum produto SISP. Todas as decisões de produto são qualitativas.

### 8. UX do cidadão é dor reconhecida, não prioridade
**Tipo:** DADO REAL  
A PC priorizou automatização operacional. UX do cidadão é segunda fase. O DS deve priorizar a eficiência do policial homologador, não apenas o cidadão.

---

## Hipóteses H1–H15 — estado pós-pesquisa

| # | Hipótese | Status | Fonte |
|---|---|---|---|
| H1 | O UI Kit não tem adoção formal | ✅ CONFIRMADA | Sommer, Holiwod |
| H2 | O padrão atual existe só na cabeça dos devs | ✅ CONFIRMADA | Holiwod |
| H3 | Demilis é o único guardião do front-end | ✅ CONFIRMADA | Holiwod, Sommer |
| H4 | A DV é o produto com mais desenvolvimento ativo | ✅ CONFIRMADA | Sommer |
| H5 | Existe um documento de requisitos da PC para a DV | ✅ CONFIRMADA | Sommer |
| H6 | Terceiros constroem fora do padrão sem documentação | ✅ CONFIRMADA | Caso IPEM (Sommer) |
| H7 | PC tem identidade visual própria | ✅ CONFIRMADA | Coradini |
| H8 | PC não sabe o que é DS | ✅ CONFIRMADA | Fábio Xavier, Coradini |
| H9 | PC veria valor em um DS para seus próprios sistemas | ✅ CONFIRMADA | Coradini |
| H10 | Não existe Figma no projeto | ✅ CONFIRMADA | Demilis |
| H11 | Não existem tokens formais | ✅ CONFIRMADA | Demilis |
| H12 | O DS não tem analytics | ✅ CONFIRMADA | Holiwod |
| H13 | Policiais usam o sistema em campo com mobile | ❌ REFUTADA | Coradini e Fábio trabalham em escritório |
| H14 | A sessão expirada em campo é problema recorrente | ⏳ A VALIDAR | Não aplicável aos entrevistados — Deise ou policiais de campo |
| H15 | O DS será adotado se for bem documentado | ⏳ A VALIDAR | Depende de mandato institucional — necessário |

---

## Problema central declarado

> O UI Kit do SISP existe como artefato técnico mas não como produto: sem mandato, sem documentação formal, sem governança, e sem sistema de tokens. Isso o torna invisível para terceiros, frágil diante de mudanças de time, e impossível de customizar por clientes com identidade própria.

---

## How Might We (HMW)

- HMW tornar o DS adotável por terceiros sem contexto histórico?
- HMW distribuir o conhecimento hoje centralizado em Demilis?
- HMW dar autonomia de theming aos clientes sem criar inconsistência?
- HMW garantir que cada componente resolva as violações de acessibilidade já mapeadas?

---

## O que está fora do escopo (não-problema)

- Redesign dos produtos DV, Conecta, BO Integrado — o DS é a infraestrutura, não os produtos
- UX do cidadão na DV — dor reconhecida mas segunda fase (decisão da PC)
- Analytics nos produtos — seria um projeto separado
- Todos os temas de clientes — escopo é o sistema base + exemplo de override (PC)
