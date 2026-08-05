---
component-id: BC-13
component-name: Forms
type: Base
status: approved
sprint: 3
approved-by: [Giuliana]
approved-date: [2026-07-13]
figma-node-id: [118:2232, 118:2304, 118:2352, 118:2247, 118:2262]
---

# Component Spec — Forms

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → BC-13 (cor crit · vis crit · wcag aten · tip aten)
> - `docs/analyses/nielsen-analysis.md` → BC-13 (H-1 crit · H-4 crit · H-5 crit · H-9 crit · H-2 aten · H-6 aten · H-8 aten)
> - `docs/analyses/inventory.md` → BC-13
> - Checklist WCAG: Formulários (BC-13, SC-03, SC-06, SC-09)

---

## O que é

Forms é o sistema de entrada de dados do DS SISP. Define a aparência e o comportamento de todos os campos de formulário: text inputs, selects, textareas, checkboxes, radios, e campos com máscara. É o componente com **maior risco de acessibilidade** do DS — 4 violações WCAG críticas e 4 Nielsen críticas.

---

## Audiência de uso

- **Policial na DV:** preenche formulários em cada etapa do BO — dados pessoais, veículos, endereços, narrativa
- **Devs CiASC / terceiros:** consomem os campos em formulários de consulta (SC-02, SC-03, SC-04, SC-06) e registro
- **Cidadão (DV externa):** preenche formulários de registro de ocorrência online

---

## Anatomia de um campo

```
┌──────────────────────────────────────────┐
│  Label *                                 │  ← sempre visível, nunca placeholder
│  ┌──────────────────────────────────┐    │
│  │  Placeholder ou valor            │    │  ← input com borda, focus ring
│  └──────────────────────────────────┘    │
│  Texto de ajuda ou formato esperado      │  ← hint (opcional)
│  ⚠ Mensagem de erro                     │  ← só aparece no estado erro
└──────────────────────────────────────────┘
```

- **Label:** sempre acima do campo, sempre visível. Nunca usar placeholder como substituto
- **Input:** campo de entrada com borda, padding interno, focus ring
- **Hint:** texto de ajuda abaixo do input — formato esperado ("dd/mm/aaaa"), instrução
- **Erro:** ícone ⚠ + texto descritivo em vermelho — aparece abaixo do input no estado erro

---

## Props / API

### Form (container)

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `fields` | `FormField[]` | sim | — | Array de campos do formulário |
| `onSubmit` | `Function` | sim | — | Callback de submissão |
| `validation` | `'onBlur' \| 'onSubmit'` | não | `'onBlur'` | Quando validar: ao sair do campo ou ao submeter |
| `layout` | `'vertical' \| 'horizontal'` | não | `'vertical'` | Disposição label/input |
| `columns` | `1 \| 2 \| 3` | não | `1` | Grid de colunas para campos |

### FormField

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `type` | `'text' \| 'number' \| 'date' \| 'email' \| 'tel' \| 'cpf' \| 'cnpj' \| 'cep' \| 'placa' \| 'select' \| 'textarea' \| 'checkbox' \| 'radio'` | sim | — | Tipo do campo |
| `name` | `string` | sim | — | Identificador único do campo |
| `label` | `string` | sim | — | Label visível — obrigatório, sempre exibido |
| `placeholder` | `string` | não | — | Texto placeholder dentro do input |
| `required` | `boolean` | não | `false` | Indica obrigatoriedade — exibe asterisco (*) no label |
| `disabled` | `boolean` | não | `false` | Desabilita o campo |
| `readonly` | `boolean` | não | `false` | Somente leitura — exibe valor sem permitir edição |
| `hint` | `string` | não | — | Texto de ajuda abaixo do input |
| `mask` | `string` | não | — | Máscara de formatação (ex: "000.000.000-00" para CPF) |
| `options` | `{label, value}[]` | não | — | Opções para select, radio, checkbox |
| `rows` | `number` | não | `3` | Linhas visíveis do textarea |
| `maxLength` | `number` | não | — | Limite de caracteres |
| `errorMessage` | `string` | não | — | Mensagem de erro customizada |

**Convenção Angular:**
```html
<sisp-lib-form [sispLibFormConfig]="config"></sisp-lib-form>
```

**Exemplo de uso:**
```typescript
config: SispLibFormConfig = {
  validation: 'onBlur',
  columns: 2,
  fields: [
    { type: 'text', name: 'nome', label: 'Nome completo', required: true },
    { type: 'cpf', name: 'cpf', label: 'CPF', required: true, mask: '000.000.000-00', hint: 'Apenas números' },
    { type: 'date', name: 'nascimento', label: 'Data de nascimento', hint: 'dd/mm/aaaa' },
    { type: 'select', name: 'uf', label: 'Estado', options: [...] },
    { type: 'textarea', name: 'narrativa', label: 'Relato da ocorrência', rows: 5, maxLength: 2000 },
  ],
  onSubmit: (data) => { ... }
};
```

---

## Estados de campo

| Estado | Descrição visual | Tokens |
|---|---|---|
| Default | Borda neutra, label escuro, placeholder cinza | `border: --color-border` · `label: --color-text-primary` · `placeholder: --color-text-muted` |
| Focus | Borda primária + ring 2px | `border: --color-border-focus` · `ring: 2px solid --color-border-focus` |
| Filled | Borda neutra, texto escuro, valor preenchido | `border: --color-border` · `text: --color-text-primary` |
| Error | Borda vermelha, ícone ⚠ + mensagem de erro abaixo | `border: --color-danger` · `error-text: --color-danger` · `error-bg: --color-danger-bg` |
| Disabled | Fundo sutil, opacidade 0.6, cursor not-allowed | `bg: --color-bg-muted` · `opacity: 0.6` |
| Readonly | Fundo sutil, sem borda, texto normal | `bg: --color-bg-subtle` · `border: none` · `text: --color-text-primary` |

### Dimensões do input

| Propriedade | Valor | Token |
|---|---|---|
| Altura | 40px | — |
| Padding horizontal | 12px | `--space-3` |
| Border radius | 4px | `--radius-sm` |
| Borda | 1px solid | `--color-border` |
| Font size (input) | 16px | `--text-base` |
| Font size (label) | 14px | `--text-sm` |
| Font weight (label) | 600 | `--font-semibold` |
| Font size (hint/error) | 13px | entre `--text-xs` e `--text-sm` |
| Gap label → input | 6px | — |
| Gap input → hint/error | 4px | — |
| Gap entre campos | 20px | `--space-5` |

---

## Tipos de campo específicos

### Campos com máscara (CPF, CNPJ, CEP, Placa, Telefone)

| Tipo | Máscara | Exemplo formatado | Hint automático |
|---|---|---|---|
| `cpf` | `000.000.000-00` | 123.456.789-00 | "Apenas números" |
| `cnpj` | `00.000.000/0000-00` | 12.345.678/0001-00 | "Apenas números" |
| `cep` | `00000-000` | 88000-000 | "Apenas números" |
| `placa` | `AAA-0A00` ou `AAA-0000` | ABC-1D23 ou ABC-1234 | "Formato Mercosul ou antigo" |
| `tel` | `(00) 00000-0000` | (48) 99999-0000 | "DDD + número" |

- Máscara formata automaticamente enquanto o usuário digita
- Campo anuncia formato ao screen reader via `aria-describedby` apontando para o hint
- Caracteres não numéricos são ignorados (exceto placa)

### Select

- Dropdown nativo estilizado com chevron ▼ à direita
- Opção placeholder: "Selecione..." em cor muted
- `aria-required` quando obrigatório

### Textarea

- Altura definida por `rows` (padrão: 3)
- Resize vertical permitido, horizontal bloqueado
- Contador de caracteres quando `maxLength` definido: "142 / 2000"

### Checkbox / Radio

- Checkbox: quadrado com check ✓ quando selecionado
- Radio: círculo com dot quando selecionado
- Tamanho: 20×20px, alinhado com a primeira linha do label
- Cor selecionado: `--color-primary`
- Grupos de checkbox/radio com fieldset + legend

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução neste spec |
|---|---|---|
| Contraste (wcag aten) | Estados de erro e foco não verificados | Focus: ring `--color-border-focus` (#C4000B) sobre branco = 5.2:1 ✅. Erro: texto `--color-danger` (#991B1B) sobre branco = 7.8:1 ✅ AAA |
| Uso de cor (cor **crit**) | Erro indicado apenas por cor vermelha | Erro: cor vermelha + ícone ⚠ + texto descritivo. Três canais redundantes |
| Visual (vis **crit**) | Estados de validação, erro e foco não mapeados. Labels não visíveis | 6 estados documentados. Label **sempre visível** acima do campo. Focus ring de 2px. Erro com ícone + texto |
| Tipografia (tip aten) | Hierarquia label/placeholder/valor não definida | Label: 14px semibold escuro. Placeholder: 16px regular muted. Valor: 16px regular escuro. Hint: 13px regular cinza. Erro: 13px regular vermelho |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução neste spec |
|---|---|---|
| H-1 Visibilidade (CRIT) | Estados de validação não documentados | 6 estados definidos com visual distinto: default, focus, filled, error, disabled, readonly |
| H-2 Mundo real (aten) | Labels não auditados | Labels em português, linguagem natural. Hint com formato esperado |
| H-4 Consistência (CRIT) | Inconsistência: alguns campos com máscara, outros sem | Sistema de máscaras padronizado: CPF, CNPJ, CEP, placa, telefone. Formato documentado para cada tipo |
| H-5 Prevenção (CRIT) | Erro só aparece ao submeter | Validação `onBlur` por padrão — erro aparece ao sair do campo, não ao submeter. Previne acúmulo de erros |
| H-6 Reconhecimento (aten) | Campos sem hint de formato | Prop `hint` para todos os campos. Máscaras têm hint automático. `aria-describedby` conecta hint ao campo |
| H-8 Estética (aten) | Densidade de campos não auditada | Grid de colunas (1/2/3). Gap de 20px entre campos. Grupos com fieldset visual |
| H-9 Recuperação (CRIT) | Mensagens de erro não especificadas | Cada erro tem mensagem descritiva: "CPF inválido — verifique os 11 dígitos". Ícone ⚠ + texto + cor |

---

## Regras de acessibilidade

- [ ] Label **sempre visível** — nunca usar placeholder como substituto de label
- [ ] Label vinculado ao input via `for`/`id` ou wrapping
- [ ] Asterisco (*) em campos obrigatórios + `aria-required="true"`
- [ ] Estado de erro: ícone ⚠ + texto descritivo + cor vermelha (3 canais)
- [ ] `aria-invalid="true"` quando campo está em erro
- [ ] `aria-describedby` conectando input ao hint e/ou mensagem de erro
- [ ] Focus ring visível: `2px solid var(--color-border-focus)`, nunca removido
- [ ] Campos com máscara: hint de formato acessível ao screen reader
- [ ] Fieldset + legend para grupos de checkbox/radio
- [ ] Navegável por teclado: Tab entre campos, Space para checkbox, Arrow keys para radio
- [ ] Contador de caracteres anunciado ao screen reader quando maxLength definido

---

## Comportamentos esperados

- Quando `validation = 'onBlur'` e campo perde foco → valida e mostra erro se inválido
- Quando `validation = 'onSubmit'` e formulário é submetido → valida todos os campos e mostra erros
- Quando campo com erro recebe foco e é corrigido → erro desaparece em tempo real (onInput)
- Quando `required = true` e campo vazio perde foco → erro "Campo obrigatório"
- Quando `mask` definido → formata automaticamente enquanto digita, aceita apenas caracteres válidos
- Quando `maxLength` definido → exibe contador "142 / 2000", bloqueia input ao atingir limite
- Quando `disabled = true` → campo não editável, não enviado no submit
- Quando `readonly = true` → campo não editável, MAS enviado no submit (ex: dados pré-preenchidos)

---

## Mensagens de erro padrão

| Validação | Mensagem |
|---|---|
| Campo obrigatório vazio | "Campo obrigatório" |
| CPF inválido | "CPF inválido — verifique os 11 dígitos" |
| CNPJ inválido | "CNPJ inválido — verifique os 14 dígitos" |
| CEP inválido | "CEP inválido — formato 00000-000" |
| Email inválido | "Email inválido — verifique o formato" |
| Telefone inválido | "Telefone inválido — inclua DDD + número" |
| Placa inválida | "Placa inválida — formato ABC-1234 ou ABC-1D23" |
| Limite excedido | "Máximo de [N] caracteres" |
| Data inválida | "Data inválida — formato dd/mm/aaaa" |

---

## Composição com outros componentes

| Componente | Relação |
|---|---|
| BC-06 Cards | Formulários frequentemente dentro de cards — usar padding `none` no card |
| BC-05 Buttons | Botão de submit no footer do card, não dentro do form |
| SC-02/03/04 Consultas | Usam sisp-lib-form internamente para campos de busca |
| SC-06 Pesquisa Textual | Usa sisp-lib-form para campo de busca com toggle fonetizado |
| SC-09 Logradouros | Campos de endereço com máscara de CEP |

---

## Casos excepcionais / bordas

- **Muitos campos (> 10):** usar grid de 2 ou 3 colunas. Agrupar campos relacionados com heading interno
- **Campo com valor pré-preenchido via BFF:** usar estado `readonly` — mostra valor sem permitir edição
- **Mobile (< 640px):** forçar layout 1 coluna. Input ocupa 100% da largura. Label acima (nunca lateral)
- **Formulário dentro de stepper (SC-13):** validar o step atual antes de permitir avançar
- **Múltiplos erros:** exibir todos os erros de uma vez, scroll automático para o primeiro campo com erro

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-border` | Borda default do input |
| `--color-border-focus` | Ring de foco + borda focus |
| `--color-danger` | Borda erro, texto erro |
| `--color-danger-bg` | Fundo sutil em erro (opcional) |
| `--color-text-primary` | Label, valor preenchido |
| `--color-text-muted` | Placeholder |
| `--color-text-secondary` | Hint text |
| `--color-bg-muted` | Fundo disabled |
| `--color-bg-subtle` | Fundo readonly |
| `--color-primary` | Checkbox/radio selecionado |
| `--radius-sm` | Border radius do input (4px) |
| `--font-body` | Família tipográfica |
| `--text-sm` | Label (14px) |
| `--text-base` | Input value (16px) |
| `--font-semibold` | Label weight |
| `--space-3` | Padding horizontal input |
| `--space-5` | Gap entre campos |
| `--transition-fast` | Transição de borda/cor (150ms) |

---

## O que está fora deste spec

- **Formulários multi-step:** responsabilidade do SC-13 Steppers, não do BC-13 Forms
- **Upload de arquivos:** responsabilidade do SC-15 Uploaders
- **Busca com autocomplete:** componente separado se necessário
- **Rich text editor:** não existe no SISP, não entra
- **Validação server-side:** o componente exibe erros, mas a lógica de validação server-side é do produto

---

## Critérios de aceite

- [ ] 6 estados de campo (default, focus, filled, error, disabled, readonly) no Figma
- [ ] Tipos de campo: text, select, textarea, checkbox, radio, masked (CPF, CEP, placa)
- [ ] Label sempre visível acima do campo — nunca placeholder como label
- [ ] Estado de erro com ícone ⚠ + texto descritivo + cor (3 canais)
- [ ] Hint de formato para campos com máscara
- [ ] Grid de colunas (1/2/3) documentado
- [ ] Todas as 4 violações WCAG críticas resolvidas
- [ ] Todas as 4 violações Nielsen críticas resolvidas
- [ ] Mensagens de erro padrão documentadas
- [ ] Composição com BC-06 Cards documentada
- [ ] Revisado e aprovado por Giuliana
