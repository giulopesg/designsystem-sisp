# DS SISP — Design System CiASC/SC

Design System do Sistema Integrado de Seguranca Publica (SISP), desenvolvido para o CiASC / COSEG / Governo de Santa Catarina.

## Status atual

- **Fase:** D2 — Building (Sprint 8)
- **Component Sets no Figma:** 62 (27 BC + 21 SC + 10 DC + 4 layouts DV)
- **Component Specs:** 57 (27 BC + 20 SC + 10 DC) — cobertura 100%
- **Variaveis Figma:** 104 (5 colecoes: Primitivos, Tipografia, Espacamento, Border Radius, Cores Semanticas)
- **Text Styles:** 21 (Heading, Overline, Body, Label, Mono)
- **Bindings auditados:** 4400+

## Estrutura

| Diretorio | Conteudo |
|---|---|
| `docs/` | PRD, Discovery, analises WCAG/Nielsen, specs de componentes |
| `docs/component-specs/` | 57 specs — template em `_template.md` |
| `design-tokens/` | Tokens CSS — fonte de verdade |
| `deliverables/` | Entregaveis HTML (sitemap, user journey, prototipos DV, wireframes) |
| `deliverables/dv/` | Prototipos HTML interativos da Delegacia Virtual (3 telas) |
| `deliverables/assets/` | Imagens institucionais (logos PC, Gov SC, CiASC) |
| `session-log/` | Log de 194 decisoes do projeto |
| `src/` | Angular library scaffold (Sprint 10) |
| `.claude/rules/` | Regras do agente AI co-designer |

## Stack

- **Design:** Figma (via MCP)
- **Framework:** Angular (monorrepo sisp-lib)
- **CSS:** Bootstrap (utilitarios) + tokens customizados
- **Icones:** Font Awesome Free
- **Tipografia:** Montserrat (portal) + Arial (componentes Angular)

## Pipeline de componentes

```
component-spec.md -> Figma Component Set -> Aprovacao visual -> Angular (sisp-lib-[nome])
```

## Sprints completos

| Sprint | Entrega |
|---|---|
| 0 | Decisoes visuais + tokens CSS |
| 1 | Entregaveis HTML: sitemap, user journey, wireframes |
| 2 | Figma: fundacao (tokens, style guide, taxonomia) |
| 3 | 10 Base Components nucleo (Buttons, Cards, Forms, Alerts, Badges, Toasts) |
| 4 | 8 Base Components complemento (Tabs, Tables, Modals, Headers, Loaders, Dropdowns, Icons) |
| 4.1 | 13 Base Components restantes |
| 5 | 6 SISP Components DV (Session Control, Notificacoes, Steppers, Uploaders, Image Captures, Login) |
| R1 | Responsividade: variantes Layout=Mobile para 18 componentes + tokens responsivos |
| 6 | Layouts DV Core + DS Portal (10 DC Components + 6 telas) |
| 7 | 8 SISP Components Consultas + Dados |
| 8 | Em andamento — Prototipo navegavel + componentes adicionais (BC-29, SC-17..SC-22) |

## Links

- **Figma:** [DS SISP](https://www.figma.com/file/YUSNqTRVZTK2eV7D3fXypx)
- **Stage:** [sisp-design-system-stage.apps.okd4.ciasc.sc.gov.br](https://sisp-design-system-stage.apps.okd4.ciasc.sc.gov.br)
