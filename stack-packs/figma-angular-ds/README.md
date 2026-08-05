# Stack Pack — figma-angular-ds

Design System com Figma como fonte de design e Angular como entregável final.

---

## Stack

| Camada | Tecnologia | Detalhes |
|---|---|---|
| Design | Figma | Via Figma MCP — criação e edição de componentes |
| Framework | Angular | Monorrepo · componentes como biblioteca |
| CSS | Bootstrap + tokens customizados | Bootstrap para utilitários · tokens CSS como override |
| Ícones | Font Awesome Free | Classe `fa fa-[nome]` |
| Tipografia portal | Montserrat | Google Fonts |
| Tipografia componentes | Arial | Sistema |
| Prototipagem | HTML puro | Apenas para entregáveis intermediários ao cliente |

---

## Convenções obrigatórias

### Componentes Angular
```
Nome do componente: sisp-lib-[nome-kebab]
Parâmetro de config: [sispLib[NomePascal]Config]
Máxima largura de layout: 1200px
```

Exemplos:
```html
<!-- Botão -->
<sisp-lib-button [sispLibButtonConfig]="btnConfig"></sisp-lib-button>

<!-- Accordion -->
<sisp-lib-accordion [sispLibAccordionConfig]="accConfig"></sisp-lib-accordion>
```

### Tokens CSS
```css
/* CORRETO — via token */
color: var(--color-primary);
background: var(--color-bg-subtle);

/* ERRADO — valor hardcoded */
color: #C4000B;
background: #F9FAFB;
```

### Theming por cliente
```css
/* Override apenas dos semânticos */
[data-theme="pc"] {
  --color-primary: #C8A840;  /* dourado Polícia Civil */
}
```

---

## Pipeline de um componente

```
1. Criar component-spec.md em docs/component-specs/
   → preencher violações WCAG e Nielsen a resolver
   → Giuliana aprova

2. Figma (via Figma MCP)
   → criar/redesenhar componente
   → aplicar tokens como variáveis Figma
   → documentar todos os estados
   → Giuliana aprova visualmente

3. Angular refactoring
   → implementar contra o Figma aprovado
   → zero valores hardcoded
   → manter convenção sisp-lib-[nome]
   → Demilis valida
```

---

## Ordem de execução obrigatória

1. Token spec antes de qualquer componente
2. Base Components antes de SISP Components
3. Componentes da DV com prioridade dentro de cada grupo
4. BC-08 Charts e SC-16 Relatório de Consultas: aguardar decisão de biblioteca (não bloquear outros)
5. SC-05 e SC-08: catalogar antes de especificar

---

## Armadilhas conhecidas

### Bootstrap vs. tokens
Bootstrap usa variáveis CSS próprias (`--bs-primary` etc.). Os tokens do DS SISP são separados (`--color-primary` etc.). Não misturar — os componentes devem usar apenas os tokens DS SISP.

### SispLibStyleType
Enum compartilhado por vários componentes (Alerts, Buttons, Badges, Toasts). Qualquer mudança no enum afeta todos. Documentar antes de alterar.

### sisp-lib-form vs. BC-13 Forms
SC-03 usa `sisp-lib-form` internamente. Confirmar com Demilis se é o mesmo componente BC-13 ou um submódulo separado antes de especificar.

### Skeleton Layers (BC-18)
Em manutenção — inacessível no inventário. Não incluir nos sprints sem confirmação de Demilis sobre o status.

### SC-05 Pesquisa de Objetos e SC-08 Login
Inacessíveis no inventário. Catalogar no ambiente stage antes de criar spec.

---

## Ambiente de desenvolvimento

```
Stage DS SISP: sisp-design-system-stage.apps.okd4.ciasc.sc.gov.br
Repositório: (a definir com Demilis)
```

---

## Referências

- `design-tokens/tokens.css` — sistema de tokens
- `docs/component-specs/_template.md` — template de spec
- `docs/analyses/wcag-analysis.md` — violações por componente
- `docs/analyses/nielsen-analysis.md` — violações por componente
- Guia de Padronização SC v1.4 (Novembro 2023)
