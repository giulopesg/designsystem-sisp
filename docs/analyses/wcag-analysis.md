---
source: ds-sisp-inventario.html + ds-sisp-analise-acessibilidade.docx
last-updated: 2026-06-22
dimensions: contraste | uso-de-cor | visual | tipografia
severity: crit | aten | ok | n/a
---

# Análise WCAG AA — DS SISP

Quatro dimensões avaliadas por componente:
- **Contraste (wcag):** relação de contraste texto/fundo — mínimo 4.5:1 (texto normal) / 3:1 (texto grande)
- **Uso de cor (cor):** dependência exclusiva de cor para transmitir informação
- **Visual (vis):** estados e indicadores visuais (foco, hover, disabled, erro)
- **Tipografia (tip):** tamanho, peso, legibilidade

---

## Tokens críticos — aplicar a todo o DS antes de qualquer componente

| Token atual | Problema | Solução |
|---|---|---|
| `#8FB13A` (verde claro SC) | Contraste 2.5:1 — **inutilizável para texto** | Nunca usar como cor de texto ou estado. Apenas decorativo com texto branco/escuro sobre ele se necessário |
| `#7A7A7A` (cinza médio) | Falha para texto sobre branco (~3.8:1) | Substituir por `#6B7280` no mínimo, preferir `#4B5563` |
| `#336633` (verde primário) + `#C4000B` (vermelho) juntos | Combinação mais problemática para deuteranopia/protanopia (~8% dos homens) | Verde passa a ser exclusivamente semântico (sucesso). Vermelho é ação primária. Nunca justapostos como elementos de igual hierarquia |
| Verde sobre header escuro | Falha de contraste — cor SC sobre fundo escuro do SISP | Usar `#FFFFFF` ou verde muito claro sobre fundo escuro |

---

## Componentes com WCAG Crítico

### BC-03 · Alerts
| Dimensão | Severidade | Detalhe |
|---|---|---|
| Contraste | aten | Variante Light com contraste potencialmente insuficiente |
| Uso de cor | **crit** | 8 variantes diferenciadas só por cor — sem ícone, sem rótulo semântico |
| Visual | ok | — |
| Tipografia | ok | — |

**Solução:** Cada variante deve ter ícone + cor + rótulo ARIA. `role="alert"` não documentado — adicionar. Revisar contraste de todas as 8 variantes.

---

### BC-04 · Badges
| Dimensão | Severidade | Detalhe |
|---|---|---|
| Contraste | aten | Algumas variantes dependem de fundo colorido — verificar cada uma |
| Uso de cor | **crit** | Diferenciação exclusivamente por cor entre os 8 tipos |
| Visual | ok | — |
| Tipografia | aten | Tamanhos não documentados |

**Solução:** Adicionar ícone ou forma diferenciadora em cada variante. Definir escala de tamanhos (sm/md/lg).

---

### BC-13 · Forms ⚠️ Maior risco de acessibilidade
| Dimensão | Severidade | Detalhe |
|---|---|---|
| Contraste | aten | Estados de erro e foco precisam verificação |
| Uso de cor | **crit** | Erro indicado apenas por cor vermelha — sem ícone, sem texto de erro documentado |
| Visual | **crit** | Estados de validação, erro e foco não mapeados. Campos sem label visível |
| Tipografia | aten | Hierarquia visual entre label, placeholder e valor não estabelecida |

**Solução:** Cada campo de erro deve ter: cor + ícone + texto de erro (não só placeholder). Labels always visible (não placeholder como label). Focus ring `var(--color-border-focus)` em todos os campos. Campos com máscara precisam spec própria.

---

### BC-26 · Tabs
| Dimensão | Severidade | Detalhe |
|---|---|---|
| Contraste | ok | — |
| Uso de cor | **crit** | Aba ativa diferenciada apenas por cor verde — sem indicador de posição adicional |
| Visual | aten | — |
| Tipografia | ok | — |

**Solução:** Adicionar sublinhado ou borda inferior na aba ativa além da cor. ARIA `role="tablist"`, `role="tab"`, `aria-selected` não documentados.

---

### SC-10 · Notificações
| Dimensão | Severidade | Detalhe |
|---|---|---|
| Contraste | ok | — |
| Uso de cor | **crit** | Lida vs. não lida diferenciada apenas por cor — sem indicador visual adicional |
| Visual | aten | — |
| Tipografia | ok | — |

**Solução:** Adicionar marcador visual (ponto, negrito, ícone) para não lida além da cor.

---

### SC-13 · Steppers
| Dimensão | Severidade | Detalhe |
|---|---|---|
| Contraste | ok | — |
| Uso de cor | **crit** | Status dos steps (Completed/Required/Optional) diferenciados exclusivamente por cor |
| Visual | aten | — |
| Tipografia | ok | — |

**Solução:** Adicionar ícone por estado (✓ completed, ! required, — optional). Definir enum compartilhado com SC-11.

---

## Componentes com WCAG Atenção

| ID | Nome | Dimensões em atenção | Principal problema |
|---|---|---|---|
| BC-01 | About | vis aten · tip aten | Sem tokens de tipografia aplicados |
| BC-02 | Accordions | cor aten · vis aten | Estado disabled sem diferenciação clara |
| BC-05 | Buttons | cor aten · vis aten | Visual 100% Bootstrap — sem identidade SISP. Estado loading não documentado |
| BC-06 | Cards | cor aten · vis aten | Identidade visual Bootstrap pura |
| BC-07 | Carousels | cor aten · **vis crit** | Setas sem contraste suficiente sobre imagens. Sem keyboard nav documentado |
| BC-09 | Confirmation Modals | cor aten · vis aten | Ação destrutiva sem destaque visual diferenciado |
| BC-10 | Dropdowns | cor aten · vis aten | Item selecionado sem indicador além de cor |
| BC-11 | File Previews | cor aten · vis aten | Tipo de arquivo indicado só por ícone/cor |
| BC-14 | Headers | wcag aten · vis aten | Logo sobre fundo escuro — contraste a verificar |
| BC-15 | Icons | wcag aten · cor aten | Ícones sem alternativas textuais. Font Awesome pode ter variações de tamanho mínimo |
| BC-19 | Modals | **vis crit** | Trap de foco (keyboard) não documentado |
| BC-20 | Navigation Canvas | cor aten · vis aten | Item ativo sem diferenciação além de cor |
| BC-22 | Offcanvas | **vis crit** | Trap de foco não documentado |
| BC-23 | Popovers | vis aten · tip aten | Tamanho mínimo de texto não garantido |
| BC-24 | Route Selectors | cor aten · vis aten | Rota ativa sem diferenciação além de cor |
| BC-25 | Tables | cor aten · vis aten · tip aten | Linha selecionada sem indicador além de cor. Cabeçalhos sem scope |
| BC-27 | Toasts | wcag aten · **cor crit** · vis aten | Danger/Success diferenciados só por cor sem ícone consistente |
| SC-01 | Atualizações Recentes | cor aten · vis aten | — |
| SC-02 | Consultar Pessoa | cor aten · vis aten | — |
| SC-03 | Consultar Registro | cor aten · vis aten | — |
| SC-04 | Consultar Veículo | cor aten · vis aten | — |
| SC-06 | Pesquisa Textual | cor aten · vis aten | — |
| SC-07 | Image Captures | vis aten | Label "Open Camera" em inglês |
| SC-11 | Resource Trees | cor aten · vis aten | Status (Completed/Required/Optional) só por cor |
| SC-12 | Session Control | cor aten · vis aten | Countdown sem feedback progressivo (verde→âmbar→vermelho) |
| SC-14 | Timelines | cor aten · tip aten | Date-pills: 1º grupo vermelho, demais azuis — lógica não documentada |
| SC-15 | Uploaders | cor aten · vis aten | Label "Upload File" em inglês |

---

## Componentes OK em WCAG

| ID | Nome | Nota |
|---|---|---|
| BC-12 | Footers | Aplicar tokens de tipografia |
| BC-16 | Loaders | Verificar spinner sem label (sem texto alternativo) |
| BC-17 | Maintenance | Funcional |
| BC-28 | Version | Utilitário |
| SC-09 | Logradouros | Verificar máscara de CEP com leitor de tela |

---

## Não avaliados (N/A — componente não existe ou inacessível)

| ID | Nome | Motivo |
|---|---|---|
| BC-08 | Charts | Componente não existe — TODO |
| BC-18 | Skeleton Layers | Inacessível durante o inventário |
| BC-21 | Objects | Não catalogado em detalhe |
| SC-05 | Pesquisa de Objetos | Inacessível durante o inventário |
| SC-08 | Login | Inacessível durante o inventário |
| SC-16 | Relatório de Consultas | Componente não existe — TODO |

---

## Referência rápida: checklist por tipo de componente

### Formulários (BC-13, SC-03, SC-06, SC-09)
- [ ] Label sempre visível (não usar placeholder como substituto)
- [ ] Estado de erro: cor + ícone + texto descritivo
- [ ] Focus ring visível em todos os campos
- [ ] Campos com máscara: anunciar formato ao screen reader
- [ ] `aria-required`, `aria-invalid`, `aria-describedby` documentados

### Componentes de status/cor (BC-03, BC-04, BC-27, SC-10, SC-11, SC-13)
- [ ] Cada estado tem: cor + ícone + rótulo (não só cor)
- [ ] Contraste de texto sobre fundo colorido ≥ 4.5:1
- [ ] ARIA role adequado (`role="alert"`, `aria-live`, etc.)

### Modais e overlays (BC-09, BC-19, BC-22)
- [ ] Trap de foco documentado e implementado
- [ ] Fechar com Escape documentado
- [ ] Focus retorna ao elemento que abriu após fechar

### Navegação (BC-10, BC-20, BC-24, BC-26)
- [ ] Item ativo: cor + indicador adicional (sublinhado, borda, ícone)
- [ ] `aria-current="page"` ou `aria-selected` documentado
- [ ] Navegável por teclado (Tab, Enter, Arrow keys)

### Labels em inglês (SC-07, SC-15)
- [ ] "Open Camera" → "Abrir câmera"
- [ ] "Upload File" → "Enviar arquivo"
