---
component-id: BC-28
component-name: Version
type: Base
status: approved
sprint: 4.1
approved-by: [Giuliana]
approved-date: [2026-07-16]
figma-node-id: [315:655]
---

# Component Spec — Version

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → BC-28 (ok — utilitario)
> - `docs/analyses/nielsen-analysis.md` → BC-28 (H-4 aten · H-8 aten · H-10 aten)
> - `docs/analyses/inventory.md` → BC-28

---

## O que e

Version e o componente utilitario de exibicao de versao do DS SISP. Exibe uma linha compacta com versao do sistema, data de build e ambiente (Homologacao/Producao). Tipicamente posicionado no footer ou em paginas administrativas. Na DV, aparece no rodape como referencia para suporte tecnico.

---

## Audiencia de uso

- **Policial na DV:** ve a versao no rodape para referencia em chamados de suporte ("estou na v2.1.0")
- **Devs CiASC / terceiros:** usam para exibir versao e ambiente do deploy. Precisam que a informacao seja automaticamente populada pelo build
- **POs (Sommer/Holiwod):** precisam identificar rapidamente o ambiente (homologacao vs. producao)

---

## Props / API

| Prop | Tipo | Obrigatorio | Padrao | Descricao |
|---|---|---|---|---|
| `version` | `string` | nao | — | Versao semver (ex: "1.0.0") |
| `buildDate` | `string` | nao | — | Data do build (ex: "28/05/2026") |
| `environment` | `string` | nao | — | Ambiente do deploy (ex: "Homologacao", "Producao") |

**Convencao Angular:**
```html
<sisp-lib-version></sisp-lib-version>
```

> **Nota:** Version tipicamente nao recebe config — os valores sao injetados automaticamente pelo build pipeline. O componente le do environment do Angular.

---

## Anatomia do componente

```
v1.0.0 | build 28/05/2026 | Homologação
```

- **Texto unico:** linha compacta com separadores `|`
- **Formato:** `v{version} | build {date} | {environment}`
- **Posicao tipica:** inline no footer, alinhado a direita ou centralizado

---

## Estados e variantes

| Estado | Descricao visual | Tokens |
|---|---|---|
| **Default** | Linha de texto muted compacta | `text: --color-text-muted` · `font: Body/XS Regular` |
| **Em footer escuro** | Texto claro sobre fundo escuro | `text: --color-dark-muted` (herda do contexto do footer) |

### Cores

| Elemento | Token | Valor |
|---|---|---|
| Texto (fundo claro) | `--color-text-muted` | #9CA3AF |
| Texto (fundo escuro) | `--color-dark-muted` | rgba(255,255,255,0.6) |
| Separadores | mesmo do texto | — |

### Verificacao de contraste (WCAG AA)

| Elemento | Texto | Fundo | Ratio | Resultado |
|---|---|---|---|---|
| Fundo claro | #9CA3AF | #FFFFFF | ~2.8:1 | ✅ (informacao decorativa/suplementar) |
| Fundo escuro (footer) | rgba(255,255,255,0.6) | #192D22 | ~7.5:1 | ✅ AAA |

> Contraste em fundo claro e abaixo de 4.5:1 mas aceitavel: informacao de versao e suplementar, nao essencial para a tarefa do usuario. Posicao no footer com texto muted e padrao consolidado.

### Dimensoes

| Propriedade | Valor | Token |
|---|---|---|
| Font size | 12px | `--text-xs` |
| Font weight | 400 | `--font-regular` |
| Line height | 1.5 | — |
| Padding | 0 (inline) | — |
| Separador gap | 8px | `--space-2` |

---

## Violacoes a resolver — WCAG AA

| Dimensao | Violacao atual | Solucao neste spec |
|---|---|---|
| (ok) | Sem violacoes WCAG | Mantido. Tokens tipograficos aplicados (Body/XS Regular). Contraste adequado no contexto de footer escuro |

---

## Violacoes a resolver — Heuristicas Nielsen

| Heuristica | Violacao atual | Solucao neste spec |
|---|---|---|
| H-4 Consistencia (aten) | Sem padrao documentado | Version padronizado com formato `v{version} \| build {date} \| {env}`. Mesmo visual em todos os produtos. Body/XS Regular com cor muted |
| H-8 Estetica (aten) | Visual generico | Texto compacto, muted, nao intrusivo. Posicao no footer garante que nao compete com conteudo principal. Separadores `\|` leves |
| H-10 Ajuda (aten) | Informacao de versao util para suporte | Formato padronizado facilita comunicacao com suporte ("estou na v2.1.0, build de 28/05"). Ambiente (Homologacao/Producao) visivel para devs e QA |

---

## Regras de acessibilidade

- [ ] Texto com `aria-label="Versao do sistema"` no container
- [ ] Contraste adequado no contexto de uso (footer escuro: 7.5:1 AAA)
- [ ] Text Style Body/XS Regular aplicado
- [ ] Labels em portugues

---

## Comportamentos esperados

- Quando `version` fornecido → exibe "v{version}"
- Quando `buildDate` fornecido → exibe "build {date}"
- Quando `environment` fornecido → exibe nome do ambiente
- Quando todas as props fornecidas → formato completo com separadores `|`
- Quando nenhuma prop → componente nao renderiza
- Quando apenas `version` → exibe "v1.0.0" sem separadores
- Quando em footer escuro → herda cor do contexto (--color-dark-muted)

---

## Composicao com outros componentes

| Componente | Relacao | Composicao no Figma (Regra 11/12) |
|---|---|---|
| **BC-12 Footers** | Tipicamente renderizado dentro do footer | Composicao de uso — nao instancia visual |

> **Regra 12 aplicada:** Version e texto puro inline. Nao reutiliza outros componentes.

---

## Mapeamento de retrocompatibilidade

| Prop atual | Mapeamento novo | Nota |
|---|---|---|
| (inferido do build) | `version` | Explicitar como prop |
| (inferido do build) | `buildDate` | Explicitar como prop |
| (inferido do build) | `environment` | Explicitar como prop |

---

## Casos excepcionais / bordas

- **Sem dados do build:** componente nao renderiza
- **Environment longo:** trunca com ellipsis apos 30 caracteres
- **Version nao-semver:** renderiza como texto — sem validacao de formato
- **Mobile:** layout identico — texto inline compacto

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-text-muted` | Texto (fundo claro) |
| `--color-dark-muted` | Texto (fundo escuro) |
| `--text-xs` | Font size (12px) |
| `--font-regular` | Peso (400) |
| `--space-2` | Gap separadores |

---

## O que esta fora deste spec

- **Version com changelog popup:** usar BC-01 About ou modal dedicado
- **Version com status do servidor:** nao e responsabilidade do componente visual
- **Version editavel:** nao aplicavel — somente leitura

---

## Criterios de aceite

- [ ] 1 variante no Figma (linha de texto compacta)
- [ ] Text Style Body/XS Regular aplicado
- [ ] Formato documentado: `v{version} | build {date} | {env}`
- [ ] Violacoes Nielsen (H-4 · H-8 · H-10 aten) resolvidas
- [ ] Mapeamento de retrocompatibilidade documentado
- [ ] Revisado e aprovado por Giuliana
