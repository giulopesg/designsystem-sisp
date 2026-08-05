# WIZARD.md — GiuOS

O wizard do GiuOS não é um roteiro fixo. É um mapa de navegação metodológica. Giuliana lidera — o agente orienta, executa junto, e espera aprovação em cada transição.

---

## Dois modos de entrada

### Modo Exploratório
Usado quando a ideia ainda está no começo. O problema não está definido. Precisamos de discovery.
Entra no **Estágio 1**.

### Modo Execução
Usado quando o problema já foi validado e Giuliana sabe o que quer construir.
Entra no **Estágio 3**.

O agente identifica o modo na conversa inicial e confirma antes de avançar.

---

## Estágio 1 — Discovery (Double Diamond D1 / Design Thinking)
*Modo Exploratório apenas*

**O que acontece aqui:**
O agente ativa o `discovery-agent` e conduz a exploração do problema com Giuliana.

Ferramentas usadas:
- Mapeamento de contexto — quem está envolvido, o que está acontecendo hoje
- Personas — quem sente o problema, com qual intensidade
- Jobs to be done — o que as pessoas estão tentando fazer
- How Might We — reformulações do problema como oportunidade
- Se o produto tiver múltiplos atores ou fluxos de serviço: ativa `Service Design` (jornada de serviço, blueprint básico)

**O agente pergunta, Giuliana responde. O agente sintetiza, Giuliana valida.**

Output obrigatório: `docs/DISCOVERY-SYNTHESIS.md`

Aprovação de Giuliana antes de avançar para o Estágio 2.

---

## Estágio 2 — Síntese e Definição do Problema (Double Diamond D1→D2)
*Modo Exploratório apenas*

**O que acontece aqui:**
O agente transforma o que foi descoberto em problema bem definido.

- Declara o problema central em uma frase
- Mapeia as personas afetadas com clareza
- Identifica o que está fora do escopo (não-problema)
- Faz red team leve: qual a hipótese mais frágil aqui? Qual o risco mais óbvio?

Output obrigatório: seção de Definição dentro do `docs/DISCOVERY-SYNTHESIS.md`

Aprovação de Giuliana antes de avançar para o Estágio 3.

---

## Estágio 3 — PRD (Transição D1→D2)
*Ambos os modos*

**O que acontece aqui:**
O agente ativa a skill `prd-generator` e constrói o PRD junto com Giuliana.

O agente propõe cada campo do PRD — Giuliana revisa, corrige, aprova.

Campos obrigatórios do PRD:
- Problema (herdado do discovery ou declarado por Giuliana no Modo Execução)
- Personas afetadas
- Apetite (small-batch 2 semanas | standard 6 semanas | custom)
- Solução esboçada (não um spec — uma direção)
- Rabbit holes (o que pode virar armadilha)
- No-gos (o que explicitamente não entra neste ciclo)
- Critérios de sucesso (como saberemos que o problema foi resolvido)

**O PRD só existe depois que Giuliana aprova. Sem aprovação, não avança.**

Output obrigatório: `docs/PRD.md`

---

## Estágio 4 — Shape Up Shaping + Betting
*Ambos os modos — após PRD aprovado*

**O que acontece aqui:**
O agente ativa a skill `shape-up-shaping` e escreve o pitch junto com Giuliana.

O pitch é baseado no PRD aprovado, mas tem uma forma específica:
- Problema (do PRD)
- Apetite (do PRD)
- Solução esboçada — com breadboards ou fat marker sketches descritos em texto
- Rabbit holes (do PRD, refinados)
- No-gos (do PRD, refinados)

Após o pitch escrito, o agente conduz o **Betting**:
- O que entra no próximo ciclo?
- O que fica para o ciclo seguinte?
- Existe algum risco que impede o Building?

**Giuliana decide o bet. O agente não decide.**

Output obrigatório: `docs/PITCH.md`

---

## Estágio 5 — Spec-Driven Development
*Após Pitch aprovado e Bet definido*

**O que acontece aqui:**
O agente ativa a skill `spec-generator` e **gera a SPEC a partir do PRD e do Pitch aprovados**.

A SPEC define:
- Comportamentos esperados do sistema (o que deve acontecer, não como)
- Estados e transições
- Bordas e casos excepcionais
- O que está fora da spec deste ciclo (alinhado com no-gos)
- Critérios de aceite (derivados dos critérios de sucesso do PRD)

**O agente gera. Giuliana revisa e assina. Sem assinatura, o Building não começa.**

Output obrigatório: `docs/SPEC.md`

---

## Estágio 6 — Building
*Após SPEC aprovada por Giuliana*

**O que acontece aqui:**
O agente ativa o `build-agent` e inicia a execução técnica dentro do ciclo Shape Up.

- Lê o stack pack ativo (ver `stack-packs/`)
- Segue as rules de code-style e design-to-code
- Executa contra a SPEC — qualquer desvio é sinalizado antes de ser implementado
- Para componentes UI: verifica referência no Figma via MCP antes de codificar
- Mantém `session-log/` atualizado com decisões de implementação

**O agente sinaliza qualquer rabbit hole encontrado durante o Building. Giuliana decide: registrar como no-go ou abrir novo pitch.**

Ao final do ciclo: cooldown + retro antes do próximo shaping.

---

## Transições de fase — aprovações obrigatórias

| De | Para | Aprovação necessária |
|---|---|---|
| Discovery | Definição | Giuliana valida DISCOVERY-SYNTHESIS |
| Definição | PRD | Giuliana aprova problema declarado |
| PRD | Shaping | Giuliana aprova PRD completo |
| Shaping | Betting | Giuliana aprova pitch |
| Betting | Spec | Giuliana define o bet |
| Spec | Building | Giuliana assina a SPEC |
| Building | Cooldown | Ciclo encerrado — Giuliana confirma |

O agente nunca avança sozinho. Cada transição tem uma pergunta explícita de aprovação.
