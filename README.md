# DS SISP — Design System CiASC/SC

Design System do Sistema Integrado de Seguranca Publica (SISP), desenvolvido para o CiASC / COSEG / Governo de Santa Catarina.

## Estrutura

| Diretorio | Conteudo |
|---|---|
| `docs/` | PRD, Discovery, analises WCAG/Nielsen, specs de componentes |
| `docs/component-specs/` | 47 specs (27 BC + 17 SC + 3 DC) |
| `design-tokens/` | Tokens CSS — fonte de verdade |
| `deliverables/` | Entregaveis HTML (sitemap, user journey, portal) |
| `portal/` | Deploy Vercel do portal DS (HTML estatico) |
| `session-log/` | Log de decisoes do projeto |
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
component-spec.md -> Figma -> Aprovacao visual -> Angular (sisp-lib-[nome])
```

## Links

- **Figma:** [DS SISP](https://www.figma.com/file/YUSNqTRVZTK2eV7D3fXypx)
- **Stage:** [sisp-design-system-stage.apps.okd4.ciasc.sc.gov.br](https://sisp-design-system-stage.apps.okd4.ciasc.sc.gov.br)
- **Portal:** [Vercel deployment](portal/)
