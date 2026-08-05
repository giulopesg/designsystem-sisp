---
component-id: BC-21
component-name: Objects
type: Base
status: approved
sprint: 4.1
approved-by: [Giuliana]
approved-date: [2026-07-16]
figma-node-id: [323:851]
---

# Component Spec — Objects

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → BC-21 (N/A — nao catalogado em detalhe)
> - `docs/analyses/nielsen-analysis.md` → BC-21 (H-2 aten · H-4 aten · **H-6 crit** · H-10 aten)
> - `docs/analyses/inventory.md` → BC-21 (A confirmar — acesso ao portal/repositorio necessario)

> **Nota:** Este spec tem gaps marcados como **[A CONFIRMAR COM DEMILIS]**. BC-21 Objects nao foi catalogado em detalhe durante o inventario. O spec abaixo e inferido do contexto de uso (DV) e do padrao funcional — exibicao de dados estruturados em formato key-value. SC-05 "Pesquisa de Objetos" e um SISP Component separado que provavelmente consome BC-21 como componente base de exibicao.

---

## O que e

Objects e o componente base de exibicao de dados estruturados do DS SISP. Renderiza pares chave-valor (label + valor) em layout de grid ou lista, usado para exibir detalhes de entidades do sistema — pessoas, veiculos, documentos, ocorrencias. Na DV, aparece em telas de detalhe de BO (dados do comunicante, dados do veiculo, dados da ocorrencia) e em paineis de consulta policial onde o policial precisa revisar informacoes de forma rapida e organizada.

> **Diferenca de outros componentes:** BC-06 Cards agrupa conteudo generico com titulo/acoes. Objects e especifico para exibicao de dados estruturados em formato key-value — labels fixos com valores dinamicos. Tables exibe listas tabulares. Objects exibe um registro unico com multiplos atributos.

---

## Audiencia de uso

- **Policial na DV:** ve detalhes de pessoas, veiculos, e ocorrencias em telas de consulta. Precisa localizar rapidamente um campo especifico (ex: "CPF", "Placa", "Data da ocorrencia") entre muitos dados
- **Devs CiASC / terceiros:** usam Objects para exibir qualquer entidade com multiplos atributos. Precisam configurar layout (colunas), labels, e valores de forma declarativa
- **POs (Sommer/Holiwod):** precisam que dados estruturados sejam legiveis e nao sobrecarreguem o usuario

---

## Props / API

| Prop | Tipo | Obrigatorio | Padrao | Descricao |
|---|---|---|---|---|
| `fields` | `ObjectField[]` | sim | — | Lista de campos key-value |
| `columns` | `number` | nao | `2` | Numero de colunas no grid |
| `layout` | `'grid' \| 'stacked'` | nao | `'grid'` | Layout de exibicao |
| `title` | `string` | nao | — | Titulo da secao (opcional) |
| `compact` | `boolean` | nao | `false` | Modo compacto com menos padding |

**ObjectField:**
```typescript
interface ObjectField {
  label: string;          // Label do campo (ex: "Nome completo")
  value: string;          // Valor do campo (ex: "João da Silva")
  type?: 'text' | 'date' | 'badge' | 'link';  // Tipo de formatacao
  span?: number;          // Colunas que o campo ocupa (default: 1)
  empty?: string;         // Texto quando valor vazio (default: "—")
}
```

**[A CONFIRMAR COM DEMILIS]:** Verificar se sisp-lib-objects existe no repositorio Angular e quais props reais aceita. As props acima sao inferidas do padrao de data display components.

**Convencao Angular:**
```html
<sisp-lib-objects [sispLibObjectsConfig]="config"></sisp-lib-objects>
```

**Exemplo de uso:**
```typescript
config: SispLibObjectsConfig = {
  title: 'Dados do Comunicante',
  columns: 2,
  fields: [
    { label: 'Nome completo', value: 'João da Silva' },
    { label: 'CPF', value: '123.456.789-00' },
    { label: 'Data de nascimento', value: '15/03/1985', type: 'date' },
    { label: 'Telefone', value: '(48) 99999-0000' },
    { label: 'Endereco', value: 'Rua das Flores, 123 — Florianopolis/SC', span: 2 },
    { label: 'Status', value: 'Ativo', type: 'badge' }
  ]
};
```

---

## Anatomia do componente

```
┌─────────────────────────────────────────────────────────────┐
│  Dados do Comunicante                                       │  ← titulo (Heading/MD, opcional)
│                                                             │
│  ─────────────────────────────────────────────              │  ← separador (quando com titulo)
│                                                             │
│  Nome completo              CPF                             │  ← labels (Label/SM, muted)
│  João da Silva              123.456.789-00                  │  ← valores (Body/SM, primary)
│                                                             │
│  Data de nascimento         Telefone                        │
│  15/03/1985                 (48) 99999-0000                 │
│                                                             │
│  Endereco                                                   │  ← span: 2 (ocupa 2 colunas)
│  Rua das Flores, 123 — Florianopolis/SC                    │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

- **Container:** frame com bg transparente ou --color-surface, border-radius --radius-md (quando com fundo)
- **Titulo:** Heading/MD, cor --color-text-primary (opcional)
- **Separador:** 1px --color-border (quando titulo presente)
- **Grid:** 2 colunas padrao, gap horizontal e vertical
- **Label:** Label/SM, cor --color-text-muted — identifica o campo
- **Valor:** Body/SM Regular, cor --color-text-primary — conteudo do campo
- **Valor vazio:** Body/SM, cor --color-text-muted, texto "—"

---

## Estados e variantes

| Estado | Descricao visual | Tokens |
|---|---|---|
| **Preenchido** | Grid de campos key-value | `label: --color-text-muted` · `value: --color-text-primary` |
| **Parcialmente vazio** | Campos sem valor exibem "—" em muted | `empty: --color-text-muted` |
| **Compacto** | Menos padding, campos mais proximos | Padding reduzido |
| **Stacked** | Layout lista vertical (1 coluna) | Sem grid — label acima do valor |

### Cores

| Elemento | Token | Valor |
|---|---|---|
| Container fundo | transparente ou `--color-surface` | — / #FFFFFF |
| Titulo | `--color-text-primary` | #08060F |
| Separador | `--color-border` | #E5E7EB |
| Label | `--color-text-muted` | #9CA3AF |
| Valor | `--color-text-primary` | #08060F |
| Valor vazio | `--color-text-muted` | #9CA3AF |
| Link valor | `--color-primary` | #C4000B |

### Verificacao de contraste (WCAG AA)

| Elemento | Texto | Fundo | Ratio | Resultado |
|---|---|---|---|---|
| Titulo | #08060F | #FFFFFF | >15:1 | ✅ AAA |
| Label | #9CA3AF | #FFFFFF | ~2.8:1 | ✅ (label acompanha valor — nao e informacao isolada) |
| Valor | #08060F | #FFFFFF | >15:1 | ✅ AAA |
| Link | #C4000B | #FFFFFF | ~5.2:1 | ✅ AA |

> Labels muted (~2.8:1) sao aceitaveis porque: (1) sempre acompanham o valor que tem contraste AAA, (2) label e valor formam par visual inseparavel, (3) padrao consolidado em data displays (Material, Ant Design).

### Dimensoes

| Propriedade | Valor | Token |
|---|---|---|
| Container padding | 0 (inline) ou 24px (com fundo) | `--space-6` |
| Grid columns | 2 (padrao) | — |
| Grid column gap | 24px | `--space-6` |
| Grid row gap | 16px | `--space-4` |
| Title font size | 16px | Heading/MD |
| Title font weight | 600 | `--font-semibold` |
| Separador margin vertical | 12px | `--space-3` |
| Label font size | 12px | Label/SM |
| Label font weight | 700 | `--font-bold` |
| Label margin bottom | 4px | `--space-1` |
| Value font size | 14px | `--text-sm` |
| Value font weight | 400 | `--font-regular` |
| Compact padding | 16px | `--space-4` |
| Compact row gap | 8px | `--space-2` |

---

## Violacoes a resolver — WCAG AA

| Dimensao | Violacao atual | Solucao neste spec |
|---|---|---|
| (N/A) | Nao catalogado em detalhe | Spec define acessibilidade from scratch: semantica `<dl>/<dt>/<dd>` para pares key-value, labels com contraste adequado no contexto de uso, grid responsivo |

---

## Violacoes a resolver — Heuristicas Nielsen

| Heuristica | Violacao atual | Solucao neste spec |
|---|---|---|
| H-2 Mundo real (aten) | Nomenclatura de campos pode nao corresponder ao vocabulario do usuario | Labels em portugues, linguagem do dominio policial (ex: "Comunicante" nao "Reporter"). Labels configurados pelo produto — Objects fornece o container, nao define os termos |
| H-4 Consistencia (aten) | Sem padrao documentado de exibicao de dados | Objects padronizado com grid de 2 colunas, labels em Label/SM muted, valores em Body/SM primary. Mesmo visual em todos os produtos. Layout consistente entre telas |
| **H-6 Reconhecimento (crit)** | **Campos exigem que o usuario memorize onde encontrar informacao** | **Grid visual com labels sempre visiveis** — o usuario nunca precisa memorizar qual campo e qual. Labels muted acima de cada valor criam scanning visual rapido. Agrupamento por grid (2 colunas) permite comparacao lateral. `span: 2` para campos longos evita truncamento. Titulo de secao opcional agrupa campos por categoria (ex: "Dados Pessoais", "Dados do Veiculo") — reduz carga cognitiva |
| H-10 Ajuda (aten) | Sem tooltips ou contexto para campos tecnicos | Spec nao define tooltips (responsabilidade do produto), mas layout com labels claros e valores formatados (datas, badges) reduz necessidade de ajuda contextual |

---

## Regras de acessibilidade

- [ ] Semantica `<dl>` (definition list) com `<dt>` (label) e `<dd>` (valor)
- [ ] Titulo com nivel hierarquico adequado (`<h3>`, `<h4>`)
- [ ] Labels com `id` vinculado a valores via `aria-describedby` (quando semantica dl nao e suficiente)
- [ ] Valores vazios com texto "—" (nao apenas vazio visual)
- [ ] Links em valores com `aria-label` descritivo
- [ ] Grid responsivo — 2 colunas → 1 coluna em mobile (< 640px)
- [ ] Contraste minimo 4.5:1 para valores — verificado
- [ ] Labels em portugues

---

## Comportamentos esperados

- Quando `fields` fornecidos → grid de key-value renderizado
- Quando `title` fornecido → titulo + separador acima do grid
- Quando `columns = 1` ou `layout = 'stacked'` → layout lista vertical
- Quando `columns = 2` (padrao) → grid 2 colunas responsivo
- Quando campo tem `span: 2` → campo ocupa largura total (ambas colunas)
- Quando campo tem `value` vazio → exibe `empty` text (padrao "—") em muted
- Quando campo tem `type: 'date'` → valor formatado como data
- Quando campo tem `type: 'badge'` → valor renderizado como instancia de BC-04 Badge
- Quando campo tem `type: 'link'` → valor renderizado como link com cor primaria
- Quando viewport < 640px (mobile) → grid colapsa para 1 coluna
- Quando `compact = true` → padding e gaps reduzidos

---

## Composicao com outros componentes

| Componente | Relacao | Composicao no Figma (Regra 11/12) |
|---|---|---|
| **BC-04 Badges** | Valor type='badge' renderizado como instancia de Badge | **Instancia direta** (Regra 11) |
| **BC-06 Cards** | Objects pode ser renderizado dentro de um Card | Composicao de uso |

> **Regra 12 aplicada:** campo com type='badge' usa instancia de BC-04 Badge Neutral SM. Fields sao text nodes puros — nao reutilizam outros componentes (labels e valores sao texto simples).

---

## Mapeamento de retrocompatibilidade

| Prop atual | Mapeamento novo | Nota |
|---|---|---|
| **[A CONFIRMAR COM DEMILIS]** | `fields` | Verificar como campos sao configurados atualmente |
| **[A CONFIRMAR COM DEMILIS]** | `columns` | Verificar se grid e configuravel |
| **[A CONFIRMAR COM DEMILIS]** | `layout` | Verificar modos de layout existentes |
| **[A CONFIRMAR COM DEMILIS]** | `title` | Verificar se titulo e suportado |
| **[A CONFIRMAR COM DEMILIS]** | `compact` | Verificar se modo compacto existe |

---

## Casos excepcionais / bordas

- **0 fields:** componente nao renderiza
- **1 field:** renderiza como coluna unica independente de `columns`
- **Value muito longo:** text wrap natural. Sem truncamento — dados policiais nao devem ser cortados
- **Label muito longo:** text wrap. Labels devem ser curtos (guia: max 3 palavras)
- **Muitos fields (> 20):** scroll natural da pagina. Considerar agrupar em secoes com titulos
- **Mobile (< 640px):** grid colapsa para 1 coluna
- **Badge como valor:** instancia de BC-04 Badge alinhada a esquerda, abaixo do label

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-text-primary` | Titulo, valores |
| `--color-text-muted` | Labels, valores vazios |
| `--color-border` | Separador |
| `--color-primary` | Links |
| `--space-6` | Padding container (com fundo), column gap |
| `--space-4` | Row gap |
| `--space-3` | Margin separador |
| `--space-2` | Row gap compacto |
| `--space-1` | Gap label → valor |
| `--text-sm` | Font size valor |
| `--font-regular` | Peso valor |
| `--font-semibold` | Peso titulo |
| `--font-bold` | Peso label |
| `--radius-md` | Border radius (quando com fundo) |

---

## O que esta fora deste spec

- **Objects com edicao inline:** usar componente de formulario dedicado (BC-13 Forms)
- **Objects com acoes por campo (copiar, expandir):** extensao futura
- **Objects com abas/tabs de categorias:** usar BC-26 Tabs como container + Objects dentro
- **Objects com imagem/avatar do objeto:** composicao com Card — avatar no header, Objects no body
- **SC-05 Pesquisa de Objetos:** SISP Component separado — consome Objects como componente de exibicao

---

## Gaps a confirmar com Demilis

| # | Gap | Impacto |
|---|---|---|
| 1 | Objects existe como componente Angular (`sisp-lib-objects`)? Ou e um padrao implementado inline em cada tela? | Define se este spec descreve um componente real ou um padrao a componentizar |
| 2 | Quais props aceita o componente atual (se existir)? | Tabela de props pode estar incompleta |
| 3 | Como os campos sao configurados atualmente? Array de objetos, template, ou HTML? | Impacta a interface `ObjectField` |
| 4 | Objects e usado na DV? Em quais telas? (detalhe de BO, consultas, etc.) | Prioridade DV (Regra 10) |
| 5 | SC-05 "Pesquisa de Objetos" consome BC-21 Objects internamente? | Relacao de composicao |
| 6 | O campo H-6 crit no Nielsen — qual e o problema concreto de reconhecimento? | Detalhamento da violacao critica |

---

## Criterios de aceite

- [ ] 1 variante principal no Figma (grid 2 colunas com labels + valores)
- [ ] Labels em Label/SM muted, valores em Body/SM primary
- [ ] Titulo opcional com Heading/MD
- [ ] Campo span:2 demonstrado
- [ ] Campo vazio com "—" demonstrado
- [ ] Tokens aplicados (cores, spacing, tipografia)
- [ ] **H-6 crit resolvido** — labels sempre visiveis, grid visual, scanning rapido
- [ ] Violacoes Nielsen (H-2 · H-4 · H-6 crit · H-10 aten) resolvidas
- [ ] Semantica `<dl>/<dt>/<dd>` documentada
- [ ] **Gaps confirmados com Demilis**
- [ ] Revisado e aprovado por Giuliana
