---
component-id: BC-12
component-name: Footers
type: Base
status: approved
sprint: 4.1
approved-by: [Giuliana]
approved-date: [2026-07-16]
figma-node-id: [264:529]
---

# Component Spec — Footers

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → BC-12 (ok — aplicar tokens de tipografia)
> - `docs/analyses/nielsen-analysis.md` → BC-12 (H-4 aten · H-8 aten)
> - `docs/analyses/inventory.md` → BC-12

---

## O que e

Footer e o componente institucional fixo na base de toda pagina do SISP. Exibe a identidade do Governo de Santa Catarina / CiASC, links utilitarios (acessibilidade, termos, contato) e creditos. Presente em 100% dos produtos SISP. Na DV, aparece em todas as telas abaixo do conteudo principal.

---

## Audiencia de uso

- **Policial na DV:** ve o footer em toda tela — referencia institucional e links de suporte (termos de uso, acessibilidade, contato)
- **Devs CiASC / terceiros:** incluem o footer como componente obrigatorio em toda pagina do produto. Precisam configurar links e visibilidade do logo por produto
- **POs (Sommer/Holiwod):** precisam que a identidade institucional SC esteja presente e padronizada em todos os produtos

---

## Props / API

| Prop | Tipo | Obrigatorio | Padrao | Descricao |
|---|---|---|---|---|
| `links` | `FooterLink[]` | nao | `[]` | Links utilitarios exibidos no footer |
| `showLogo` | `boolean` | nao | `true` | Exibe o logo institucional (Governo SC / CiASC) |
| `copyrightText` | `string` | nao | `'Governo de Santa Catarina'` | Texto de copyright exibido abaixo dos links |
| `year` | `number` | nao | ano atual | Ano exibido no copyright |

**FooterLink:**
```typescript
interface FooterLink {
  label: string;       // Texto do link — em portugues
  url: string;         // URL de destino
  icon?: string;       // Classe Font Awesome opcional (ex: 'fa-solid fa-envelope')
  external?: boolean;  // Se true, abre em nova aba com rel="noopener noreferrer"
}
```

**Convencao Angular:**
```html
<sisp-lib-footer [sispLibFooterConfig]="config"></sisp-lib-footer>
```

**Exemplo de uso:**
```typescript
config: SispLibFooterConfig = {
  showLogo: true,
  copyrightText: 'Governo de Santa Catarina',
  links: [
    { label: 'Acessibilidade', url: '/acessibilidade', icon: 'fa-solid fa-universal-access' },
    { label: 'Termos de Uso', url: '/termos' },
    { label: 'Politica de Privacidade', url: '/privacidade' },
    { label: 'Contato', url: '/contato', icon: 'fa-solid fa-envelope' }
  ]
};
```

---

## Anatomia do componente

```
┌────────────────────────────────────────────────────────────────────┐
│                         FOOTER (fundo escuro)                      │
│                                                                    │
│   [Logo SC]   Acessibilidade  ·  Termos de Uso  ·  Privacidade   │
│                                                                    │
│              (C) 2026 Governo de Santa Catarina                    │
│                                                                    │
└────────────────────────────────────────────────────────────────────┘
```

- **Container:** fundo escuro (--color-dark-surface), largura 100%, max-width --max-width centralizado
- **Logo zone:** logo institucional SC/CiASC a esquerda (quando `showLogo = true`)
- **Links zone:** links utilitarios centralizados, separados por ponto medio (·)
- **Copyright zone:** texto de creditos abaixo, centralizado

---

## Estados e variantes

| Estado / Variante | Descricao visual | Tokens usados |
|---|---|---|
| **Default** | Footer completo com logo, links e copyright | `bg: --color-dark-surface` · `text: --color-dark-text` |
| **Sem logo** | Footer sem logo institucional (showLogo = false) | Mesmo, sem zona de logo |
| **Sem links** | Footer apenas com copyright | Mesmo, sem zona de links |
| **Link hover** | Link com underline ao hover | `text: --color-dark-text` · `text-decoration: underline` |
| **Link focus** | Focus ring no link | `outline: 2px solid var(--color-border-focus)` · `outline-offset: 2px` |

### Cores

| Elemento | Token | Valor |
|---|---|---|
| Fundo | `--color-dark-surface` | #192D22 |
| Texto | `--color-dark-text` | #FFFFFF |
| Links | `--color-dark-text` | #FFFFFF |
| Links hover | `--color-dark-text` | #FFFFFF + underline |
| Texto muted (copyright) | `--color-dark-muted` | rgba(255,255,255,0.6) |
| Separadores | `--color-dark-muted` | rgba(255,255,255,0.6) |
| Focus ring | `--color-border-focus` | #C4000B |

### Verificacao de contraste (WCAG AA)

| Elemento | Texto | Fundo | Ratio | Resultado |
|---|---|---|---|---|
| Links | #FFFFFF | #192D22 | >13:1 | ✅ AAA |
| Copyright muted | rgba(255,255,255,0.6) | #192D22 | ~7.5:1 | ✅ AAA |
| Focus ring | #C4000B | #192D22 | ~3.6:1 | ✅ AA (grafico 3:1) |

### Dimensoes

| Propriedade | Valor | Token |
|---|---|---|
| Padding vertical | 24px | `--space-6` |
| Padding horizontal | 16px | `--space-4` |
| Max width | 1200px | `--max-width` |
| Gap logo → links | 16px | `--space-4` |
| Gap links → copyright | 12px | `--space-3` |
| Gap entre links | 16px | `--space-4` |
| Font size links | 14px | `--text-sm` |
| Font weight links | 400 | `--font-regular` |
| Font size copyright | 12px | `--text-xs` |
| Font weight copyright | 400 | `--font-regular` |
| Logo height | 32px | — |
| Separador (·) font size | 14px | `--text-sm` |

---

## Violacoes a resolver — WCAG AA

| Dimensao | Violacao atual | Solucao neste spec |
|---|---|---|
| Contraste (ok) | Sem violacao | Mantido — fundo escuro #192D22 com texto branco garante >13:1 AAA |
| Tipografia (ok → aten) | Tokens de tipografia nao aplicados | Todos os textos usam font tokens (--text-sm, --text-xs, --font-regular). Text Styles aplicados (Body/SM para links, Body/XS para copyright) |

---

## Violacoes a resolver — Heuristicas Nielsen

| Heuristica | Violacao atual | Solucao neste spec |
|---|---|---|
| H-4 Consistencia (aten) | Sem padrao documentado entre produtos | Footer padronizado com config object (sispLibFooterConfig). Mesmo componente em todos os produtos. Links configurados por produto, visual identico. Logo substituivel por tema (PC, CBM) |
| H-8 Estetica (aten) | Visual generico | Fundo escuro institucional (--color-dark-surface #192D22), tipografia por tokens, separadores sutis, espacamento consistente. Alinhamento com identity bar do BC-14 Headers |

---

## Regras de acessibilidade

- [ ] Footer com `<footer>` semantico (nao `<div>`)
- [ ] `role="contentinfo"` implicito do `<footer>`
- [ ] Links com `aria-label` quando acompanhados de icone sem texto
- [ ] Links externos com `target="_blank"` e `rel="noopener noreferrer"` + indicador visual (icone external link ou texto "(abre em nova aba)")
- [ ] Focus ring visivel em todos os links: `2px solid var(--color-border-focus)`
- [ ] Navegavel por teclado (Tab navega entre links)
- [ ] Contraste minimo 4.5:1 para texto — verificado (>13:1 AAA)
- [ ] Logo com `alt` descritivo ("Logo Governo de Santa Catarina")
- [ ] Labels em portugues

---

## Comportamentos esperados

- Quando `showLogo = true` → logo institucional exibido a esquerda
- Quando `showLogo = false` → zona de logo removida, links e copyright ocupam largura total
- Quando `links` tem itens → links exibidos centralizados, separados por ·
- Quando `links` esta vazio → zona de links removida, apenas copyright
- Quando link tem `external = true` → abre em nova aba, exibe icone de link externo (fa-solid fa-arrow-up-right-from-square, 12px)
- Quando link tem `icon` → icone exibido a esquerda do label com gap --space-2
- Quando viewport < 768px (mobile) → layout empilha verticalmente: logo (centralizado) → links (empilhados, um por linha) → copyright
- Quando viewport >= 768px (desktop) → layout horizontal conforme anatomia

---

## Composicao com outros componentes

| Componente | Relacao | Composicao no Figma (Regra 11/12) |
|---|---|---|
| **BC-15 Icons** | Icones nos links (envelope, external, acessibilidade) | Font Awesome — `aria-hidden="true"` nos icones decorativos |
| BC-14 Headers | Header + Footer formam o frame institucional de toda pagina | Composicao de uso — mesmo vocabulario visual (fundo escuro, texto branco) |

> **Regra 12 aplicada:** footer nao reutiliza outros componentes como instancias — links sao nativos (nao sao Buttons nem Tabs). Icones via BC-15 Icons (Font Awesome).

---

## Mapeamento de retrocompatibilidade

| Prop atual | Mapeamento novo | Nota |
|---|---|---|
| `links` | `links: FooterLink[]` | Extendido com `icon`, `external` |
| `showLogo` | `showLogo` | Mantido |
| — | `copyrightText` (novo) | Permite customizar texto institucional |
| — | `year` (novo) | Ano do copyright |

---

## Casos excepcionais / bordas

- **Sem links e sem logo:** footer renderiza apenas copyright — valido para paginas minimalistas
- **Muitos links (> 6):** links quebram para segunda linha no desktop. Wrap natural com flexbox
- **Label de link longo:** trunca com ellipsis apos max-width 200px
- **Tema PC/CBM:** logo e copyrightText mudam por configuracao. Fundo escuro mantem --color-dark-surface (que pode ser sobrescrito por tema)
- **Pagina sem footer:** componente nao renderiza se config nao for fornecida
- **Links sem URL:** link renderiza como texto nao clicavel (degradacao gracil)

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-dark-surface` | Fundo do footer |
| `--color-dark-text` | Texto e links |
| `--color-dark-muted` | Copyright, separadores |
| `--color-border-focus` | Focus ring dos links |
| `--max-width` | Largura maxima (1200px) |
| `--font-body` | Familia tipografica |
| `--text-sm` | Font size links (14px) |
| `--text-xs` | Font size copyright (12px) |
| `--font-regular` | Peso do texto (400) |
| `--space-6` | Padding vertical |
| `--space-4` | Padding horizontal, gap logo→links, gap entre links |
| `--space-3` | Gap links→copyright |
| `--space-2` | Gap icone→label |

---

## O que esta fora deste spec

- **Footer com newsletter/subscribe:** nao e padrao SISP
- **Footer com redes sociais:** nao documentado no contexto CiASC
- **Footer com mapa do site completo:** footers governamentais SC sao minimalistas
- **Footer fixo (sticky):** footer e estatico, aparece apos o conteudo. Footer sticky e anti-padrao para paginas com scroll
- **Footer com versao do sistema:** BC-28 Version e componente separado

---

## Criterios de aceite

- [ ] 1 variante principal no Figma com layout responsivo documentado
- [ ] Fundo escuro (--color-dark-surface) com texto branco
- [ ] Links com hover (underline) e focus ring
- [ ] Logo institucional configuravel (showLogo)
- [ ] Copyright com ano e texto customizavel
- [ ] Todos os textos com tokens tipograficos
- [ ] Contraste verificado — >13:1 AAA para texto principal
- [ ] HTML semantico documentado (`<footer>`, `role="contentinfo"`)
- [ ] Links externos com indicador visual e `rel="noopener noreferrer"`
- [ ] Violacao Nielsen H-4 (consistencia) resolvida — config padronizado
- [ ] Violacao Nielsen H-8 (estetica) resolvida — visual institucional com tokens
- [ ] Mapeamento de retrocompatibilidade documentado
- [ ] Revisado e aprovado por Giuliana
