---
component-id: [BC-XX ou SC-XX]
component-name: [nome]
type: [Base | SISP]
status: [draft | approved | in-figma | in-angular]
sprint: [número]
approved-by: []
approved-date: []
figma-node-id: []
---

# Component Spec — [NOME]

> Ler antes de criar este spec:
> - `docs/analyses/wcag-analysis.md` → seção [NOME]
> - `docs/analyses/nielsen-analysis.md` → seção [NOME]
> - Inventário: `docs/analyses/inventory.md` → [BC/SC-XX]

---

## O que é

[Descrição de 1–2 frases. O que o componente faz, quando é usado.]

---

## Audiência de uso

[Quem usa este componente no contexto SISP — ex: "policial homologando BO na DV", "dev construindo tela de consulta"]

---

## Props / API

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| | | | | |

**Convenção Angular:**
```html
<sisp-lib-[nome] [sispLib[Nome]Config]="config"></sisp-lib-[nome]>
```

---

## Estados e variantes

| Estado / Variante | Descrição visual | Tokens usados |
|---|---|---|
| Default | | |
| Hover | | |
| Focus | | |
| Active | | |
| Disabled | | |
| [outros específicos] | | |

---

## Violações a resolver — WCAG AA

> Copiar da análise WCAG. Cada item deve ser resolvido no design do Figma.

| Dimensão | Violação atual | Solução proposta |
|---|---|---|
| Contraste (wcag) | | |
| Uso de cor (cor) | | |
| Visual (vis) | | |
| Tipografia (tip) | | |

---

## Violações a resolver — Heurísticas Nielsen

> Copiar da análise Nielsen. Cada item deve ser resolvido no design do Figma.

| Heurística | Violação atual | Solução proposta |
|---|---|---|
| H-1 Visibilidade de estado | | |
| H-2 Mundo real | | |
| H-3 Controle do usuário | | |
| H-4 Consistência | | |
| H-5 Prevenção de erros | | |
| H-6 Reconhecimento | | |
| H-7 Flexibilidade | | |
| H-8 Estética | | |
| H-9 Recuperação de erros | | |
| H-10 Ajuda | | |

---

## Regras de acessibilidade

- [ ] Contraste mínimo 4.5:1 para texto normal / 3:1 para texto grande
- [ ] Estado de foco visível (ring `var(--color-border-focus)`)
- [ ] Navegável por teclado
- [ ] ARIA role e atributos documentados
- [ ] Não depende apenas de cor para transmitir informação
- [ ] Label em português
- [outros específicos do componente]

---

## Comportamentos esperados

[Descrever os comportamentos que o componente deve ter — o QUE, não o COMO implementar]

- Quando [condição] → [resultado]
- Quando [condição] → [resultado]

---

## Comportamento responsivo

> Regra 13 — Responsividade obrigatória. Breakpoints: Mobile (<768px), Tablet (768–1023px), Desktop (≥1024px).

| Precisa de variante `Layout=Mobile`? | [Sim / Não — justificar] |
|---|---|
| **Desktop** | [Comportamento padrão — já definido acima] |
| **Mobile** | [O que muda: layout, visibilidade, interação] |
| **Tablet** | [Segue Desktop / ajustes específicos via tokens responsivos] |

> Se "Não precisa": componente auto-contido (largura 100% do container ou < 320px). Documentar por quê.
> Se "Sim precisa": descrever o que muda em cada breakpoint e criar variante `Layout=Mobile` no Figma.

---

## Casos excepcionais / bordas

- [O que acontece com conteúdo muito longo?]
- [O que acontece sem dados?]
- [O que acontece em mobile?]

---

## O que está fora deste spec

- [O que explicitamente não entra neste componente neste sprint]

---

## Critérios de aceite

- [ ] Todos os estados listados existem no Figma com tokens aplicados
- [ ] Violações WCAG AA listadas estão resolvidas
- [ ] Violações Nielsen listadas estão resolvidas
- [ ] Label em português
- [ ] Exemplo Angular documentado
- [ ] Revisado e aprovado por Giuliana
