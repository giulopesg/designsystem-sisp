# Discovery DV — Sprint 6 (Layouts)

**Data:** 2026-08-05
**Fonte:** Analise visual do site producao `delegaciavirtual.sc.gov.br`
**Escopo:** Home, Nova Ocorrencia (Advertencia), Etapa 1 (Fato Ocorrido)
**Objetivo:** Fundamentar wireframes das 3 telas com DS SISP aplicado (tema Policia Civil)

---

## 1. Personas DV

### P1 — Cidadao Vitima (primario)

| Campo | Valor |
|---|---|
| **Quem** | Morador de SC que sofreu um fato (furto, ameaca, dano, etc.) |
| **Contexto** | Estressado, possivelmente 1a vez usando o sistema, quer resolver rapido |
| **Necessidade** | Registrar BO online sem precisar ir a delegacia |
| **Dor** | Wall-of-text juridico antes de agir, 10 etapas intimidam, categorias confusas |
| **Meta** | Protocolo em maos para acompanhamento |
| **Dispositivo** | Mobile (80%+ — estimativa baseada no perfil demografico) |
| **Literacia digital** | Variavel — de jovem urbano a idoso rural |

### P2 — Cidadao Estrangeiro (secundario)

| Campo | Valor |
|---|---|
| **Quem** | Turista ou residente estrangeiro em SC sem CPF |
| **Contexto** | Nao fala portugues, possivelmente vitima de furto/roubo durante viagem |
| **Necessidade** | Registrar BO em ingles ou espanhol |
| **Dor** | Seletor de idioma quase invisivel, fluxo assume CPF por padrao |
| **Meta** | Documento oficial do registro para seguro de viagem |
| **Dispositivo** | Mobile |
| **Literacia digital** | Media-alta (turista conectado) |

### P3 — Policial Homologador (contexto operacional)

| Campo | Valor |
|---|---|
| **Quem** | Delegado ou agente da PC que recebe e homologa BOs |
| **Contexto** | Volume alto de BOs, precisa triar rapido |
| **Necessidade** | BOs bem preenchidos, com dados completos e categorias corretas |
| **Dor** | Cidadaos escolhem categoria errada, relatos incompletos, midias ausentes |
| **Meta** | Homologar ou devolver com agilidade |
| **Dispositivo** | Desktop (intranet) |
| **Literacia digital** | Alta (sistema interno) |

> **Nota:** P3 nao usa a DV publica, mas e impactado pela qualidade dos dados que o cidadao insere. Wireframes devem otimizar para P1 e P2, mas com consciencia de P3.

---

## 2. Analise das 3 paginas atuais

### 2.1 Home (`/`)

**Estrutura atual:**
- Header: Logo PC + Logo Governo SC + seletor idioma
- Aviso: "servico disponivel apenas para fatos em SC"
- 4 cards de acao (dourados): Registrar BO, Imprimir BO, Emitir Atestado, Comunicar Denuncia
- Grid 2 colunas: 13 categorias de BO com icones
- Bloco informativo (2 paragrafos)
- Alert "Atencao" (homologacao + protocolo)
- Footer escuro (denuncie, lista delegacias, copyright, CiASC)

**Problemas identificados:**

| # | Problema | Severidade | Heuristica |
|---|---|---|---|
| H1 | Cards dourados com baixo contraste (texto branco sobre bege) | Critico | WCAG 1.4.3 |
| H2 | 13 categorias listadas na home E na etapa 1 — redundancia | Medio | Nielsen H8 |
| H3 | Bloco informativo longo, repetitivo com o alert | Baixo | Nielsen H8 |
| H4 | Footer sobrecarregado — mistura funcoes distintas | Baixo | Nielsen H8 |
| H5 | Sem hierarquia clara entre as 4 acoes (todas iguais) | Medio | Nielsen H4 |
| H6 | "Registrar" e a acao primaria mas visualmente igual as outras 3 | Alto | Nielsen H4 |

### 2.2 Nova Ocorrencia — Advertencia (`/nova-ocorrencia`)

**Estrutura atual:**
- Header (mesmo)
- Titulo "Advertencia"
- Tabs idioma: PT / EN / ES
- Wall-of-text: exclusoes, requisitos, avisos legais
- IP exposto
- Card cidadania: "Brasileiro com CPF?" (radio)
- Aceite: "Ciente e aceita?" + botao "Aceito"
- Footer (mesmo)

**Problemas identificados:**

| # | Problema | Severidade | Heuristica |
|---|---|---|---|
| A1 | Wall-of-text juridico — usuario nao le, so clica "Aceito" | Critico | Nielsen H6 |
| A2 | Informacao critica (exclusoes) buried em lista densa | Alto | Nielsen H6 |
| A3 | IP exposto sem explicacao contextual | Medio | Nielsen H10 |
| A4 | Pergunta CPF poderia ser mais cedo ou integrada ao fluxo | Medio | Nielsen H7 |
| A5 | Botao "Aceito" sem feedback visual de progresso | Baixo | Nielsen H1 |
| A6 | Nao ha como voltar a home sem usar back do browser | Medio | Nielsen H3 |

### 2.3 Etapa 1 — Fato Ocorrido (pos-aceite)

**Estrutura atual:**
- Header (mesmo)
- Stepper lateral vertical: 10 etapas (1-10)
- Area de conteudo: 13 categorias com icone + descricao expandida
- Footer (mesmo)

**As 10 etapas:**
1. Fato Ocorrido (selecao de categoria)
2. Mais Informacoes do Fato
3. Data e Hora do Fato
4. Local do Fato
5. Envolvidos
6. Medidas Protetivas
7. Objetos
8. Relato do Fato
9. Midias
10. Confirmacao

**Problemas identificados:**

| # | Problema | Severidade | Heuristica |
|---|---|---|---|
| E1 | 10 etapas visíveis de inicio — intimidante | Alto | Nielsen H6 |
| E2 | Categorias repetem a home sem valor adicional significativo | Medio | Nielsen H8 |
| E3 | Sem busca/filtro para as 13 categorias | Medio | Nielsen H7 |
| E4 | Icones genericos, pouco diferenciados | Baixo | Nielsen H4 |
| E5 | Stepper lateral ocupa espaco no mobile | Alto | Nielsen H7 |
| E6 | Nenhuma indicacao de tempo estimado | Medio | Nielsen H1 |

---

## 3. Fluxo de uso proposto (3 paginas)

### Fluxo atual:
```
Home (/)
  -> clica "Registrar BO"
    -> Advertencia (/nova-ocorrencia)
      -> le exclusoes + requisitos
        -> responde CPF sim/nao
          -> clica "Aceito"
            -> Etapa 1: Fato Ocorrido
              -> seleciona categoria
                -> [continua 9 etapas...]
```

### Fluxo proposto (simplificado):
```
Home (/)
  -> CTA primario "Registrar Ocorrencia" (destaque visual)
  -> Acoes secundarias (Imprimir, Atestado, Denuncias)
  -> Categorias como atalho direto (clica categoria -> pula pra etapa 1 com pre-selecao)

Nova Ocorrencia (/nova-ocorrencia)
  -> Advertencia resumida (accordion com detalhes)
  -> CPF integrado ao aceite
  -> Progresso visivel (stepper no header, nao lateral)

Etapa 1: Fato Ocorrido
  -> Stepper horizontal compacto (agrupa 10 etapas em 4 fases)
  -> Categorias com busca + agrupamento semantico
  -> Descricao on-demand (expand, nao wall)
```

### Decisoes de design para wireframes (aprovadas 2026-08-05):

| Decisao | Justificativa | Aprovacao |
|---|---|---|
| CTA "Registrar" como primario (vermelho SC) | Acao principal — 80%+ dos usuarios vem pra isso | Giuliana |
| Categorias na home como atalho COM advertencia | Clica categoria → modal advertencia resumida → etapa 1 pre-selecionada | Giuliana |
| Advertencia como modal resumida (nao pagina inteira) | Mantém requisito legal, reduz friccao no fluxo | Giuliana |
| Stepper: mostrar 2 opcoes no wireframe | Opcao A: 10 etapas (fidelidade atual) vs Opcao B: 4 fases agrupadas — decidir visualmente | Giuliana |
| Busca nas categorias | 13 itens e limite para scan visual — busca ajuda | — |
| Tema PC (dourado + escudo) aplicado via tokens | Tokens do DS com override PC (Decision D5 do projeto) | — |

---

## 4. Mapeamento de componentes DS SISP para wireframes

| Elemento do wireframe | Componente DS SISP | Node ID |
|---|---|---|
| Header | BC-14 Headers | 175:432 |
| Footer | BC-12 Footer | 264:529 |
| CTA Registrar | BC-05 Buttons (Primary LG) | 116:1862 |
| Cards de acao | BC-06 Cards | 118:2041 |
| Categorias de BO | BC-06 Cards (lista) | 118:2041 |
| Alert aviso SC | BC-03 Alerts (Warning) | 118:2456 |
| Stepper de registro | SC-13 Steppers | 330:1650 |
| Radio CPF | BC-13 Radio | 118:2262 |
| Botao Aceito | BC-05 Buttons (Primary MD) | 116:1862 |
| Seletor de idioma | BC-10 Dropdowns | 188:492 |
| Tabs idioma (advertencia) | BC-26 Tabs | 135:222 |
| Badges de status | BC-04 Badges | 124:2690 |
| Login (se necessario) | SC-17 Login SISP | 795:5694 |
| Icones | BC-15 Icons | 223:516 |
| Accordion (advertencia) | BC-02 Accordion | 315:747 |
| Loader (transicoes) | BC-16 Loaders | 177:484 |

---

## 5. Proximos passos

1. **Figma — Pagina "Discovery DV"**
   - Secao 1: Personas (3 cards com dados)
   - Secao 2: Fluxo de uso (diagrama das 3 paginas)
   - Secao 3: Wireframes — Home, Advertencia, Etapa 1 (Desktop + Mobile)

2. **Tema PC**
   - Aplicar tokens com override Policia Civil (dourado primario)
   - Logo PC e Governo SC nas posicoes do header

3. **Validacao com Giuliana** antes de avançar para alta fidelidade
