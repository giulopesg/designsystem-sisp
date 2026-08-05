---
source: ds-sisp-inventario.html + ds-sisp-analise-heuristica.docx
last-updated: 2026-06-22
heuristics: H-1 a H-10
severity: 2=crit | 1=aten | 0=ok | -1=n/a
---

# Análise Heurística Nielsen — DS SISP

10 heurísticas avaliadas por componente:
- **H-1** Visibilidade do estado do sistema
- **H-2** Correspondência com o mundo real
- **H-3** Controle e liberdade do usuário
- **H-4** Consistência e padrões
- **H-5** Prevenção de erros
- **H-6** Reconhecimento em vez de lembrança
- **H-7** Flexibilidade e eficiência de uso
- **H-8** Estética e design minimalista
- **H-9** Recuperação de erros
- **H-10** Ajuda e documentação

---

## Achado sistêmico crítico — H-4 Consistência

**22 componentes com risco crítico.** A violação mais grave e mais frequente do DS SISP não é de um componente específico: é estrutural.

**4 padrões arquiteturais coexistindo no mesmo sistema:**

| Padrão | Componentes | Problema |
|---|---|---|
| Config object `[sispLibNomeConfig]` | Maioria dos Base Components | Padrão principal — mas não universal |
| Auto-suficiente via BFF (sem props) | SC-01, SC-03, SC-06, SC-10, SC-12 | Componentes que buscam dados diretamente |
| Híbrido (config + @Output) | SC-11, SC-13 | Mistura dos dois padrões acima |
| Angular nativo (@Input/@Output) | SC-09 Logradouros | Padrão completamente diferente dos demais |

**Impacto:** terceiros contratados não conseguem inferir o padrão correto. Caso comprovado: IPEM → meses de atraso.

---

## Componentes com H Crítico (valor 2 em qualquer heurística)

### BC-03 · Alerts
| H | Severidade | Detalhe |
|---|---|---|
| H-1 | **crit** | Sem indicação de quão urgente/importante é o alerta |
| H-3 | aten | Dismissible não é padrão — usuário não pode controlar sempre |
| H-4 | aten | 8 variantes sem guia semântico de quando usar cada uma |
| H-6 | aten | Usuário precisa memorizar qual cor = qual tipo |

---

### BC-07 · Carousels
| H | Severidade | Detalhe |
|---|---|---|
| H-3 | **crit** | Sem controles de pausa, sem indicação de quantos slides existem |
| H-8 | **crit** | Movimento automático sem controle = design intrusivo |
| H-5 | aten | Sem confirmação ao navegar (perda de contexto) |

---

### BC-13 · Forms ⚠️ Maior risco de acessibilidade
| H | Severidade | Detalhe |
|---|---|---|
| H-1 | **crit** | Estados de validação (erro, enviando, sucesso) não documentados |
| H-4 | **crit** | Inconsistência entre campos: alguns com máscara, outros sem, sem padrão |
| H-5 | **crit** | Sem validação progressiva documentada — erro só aparece ao submeter |
| H-9 | **crit** | Mensagens de erro não especificadas — usuário não sabe o que corrigir |
| H-2 | aten | Labels e vocabulário dos campos não auditados para linguagem natural |
| H-6 | aten | Campos sem hint de formato (ex: "dd/mm/aaaa") |
| H-8 | aten | Densidade de campos não auditada |

---

### BC-15 · Icons
| H | Severidade | Detalhe |
|---|---|---|
| H-2 | **crit** | Mapeamento semântico não existe — mesmo ícone pode ter sentidos diferentes em contextos diferentes |
| H-4 | **crit** | Sem convenção: ícone de "editar" pode ser lápis, caneta ou chave de fenda |
| H-6 | **crit** | Usuário deve memorizar o que cada ícone significa em cada contexto |

---

### BC-16 · Loaders
| H | Severidade | Detalhe |
|---|---|---|
| H-1 | **crit** | Spinner sem label — usuário não sabe o que está carregando |
| H-9 | aten | Sem timeout documentado — loading pode durar infinitamente |

---

### BC-19 · Modals
| H | Severidade | Detalhe |
|---|---|---|
| H-3 | **crit** | Trap de foco não documentado — usuário pode "escapar" da modal sem querer |

---

### BC-22 · Offcanvas
| H | Severidade | Detalhe |
|---|---|---|
| H-3 | **crit** | Trap de foco não documentado |

---

### BC-25 · Tables
| H | Severidade | Detalhe |
|---|---|---|
| H-4 | **crit** | Sem padrão de ordenação visual (ícone ativo vs. inativo inconsistente) |
| H-7 | **crit** | Sem atalho de teclado para ordenação. Sem filtro rápido por coluna |

---

### BC-27 · Toasts
| H | Severidade | Detalhe |
|---|---|---|
| H-1 | **crit** | Sem distinção clara de urgência entre tipos (Danger vs. Warning visualmente ambíguos) |
| H-3 | **crit** | Toast pode desaparecer antes do usuário ler — sem controle de tempo |
| H-9 | aten | Sem ação de "desfazer" em Toasts de ação irreversível |

---

### SC-10 · Notificações
| H | Severidade | Detalhe |
|---|---|---|
| H-1 | **crit** | Erro de BFF aciona 2 toasts Danger simultâneos + mensagem inline — feedback duplicado e confuso |
| H-3 | **crit** | Sem controle sobre quantidade de notificações simultâneas |

---

### SC-12 · Session Control ⚠️ Maior risco operacional
| H | Severidade | Detalhe |
|---|---|---|
| H-1 | **crit** | Countdown existe mas sem feedback progressivo (verde→âmbar→vermelho) — urgência invisível |
| H-3 | **crit** | Sem botão de renovar sessão proeminente — usuário perde trabalho |
| H-5 | **crit** | Sessão pode expirar sem aviso sonoro/visual suficiente — em operação policial = risco real |
| H-9 | **crit** | Comportamento na expiração não documentado — usuário não sabe o que acontece com o que estava fazendo |

**Contexto:** policiais em operação perdem dados se a sessão expirar sem aviso. Caso de medidas protetivas de urgência documentado em pesquisa.

---

### SC-13 · Steppers
| H | Severidade | Detalhe |
|---|---|---|
| H-1 | **crit** | Não fica claro visualmente em qual step o usuário está e quantos faltam |
| H-3 | **crit** | Não está documentado se o usuário pode voltar a um step anterior |
| H-5 | aten | Sem validação por step antes de avançar documentada |
| H-9 | **crit** | Sem mensagem clara se o step falhar |

---

### SC-15 · Uploaders
| H | Severidade | Detalhe |
|---|---|---|
| H-1 | **crit** | Progresso de upload não documentado |
| H-5 | **crit** | Sem validação de tipo/tamanho antes do upload — erro só aparece depois |
| H-9 | **crit** | Mensagem de arquivo rejeitado não especificada |

---

## Tabela completa — severidade por componente e heurística

`2=crit` · `1=aten` · `0=ok` · `-=n/a`

| ID | Nome | H1 | H2 | H3 | H4 | H5 | H6 | H7 | H8 | H9 | H10 |
|---|---|---|---|---|---|---|---|---|---|---|---|
| BC-01 | About | ok | ok | ok | aten | ok | ok | ok | ok | ok | aten |
| BC-02 | Accordions | aten | ok | ok | aten | ok | aten | ok | ok | ok | ok |
| BC-03 | Alerts | **crit** | aten | aten | aten | ok | aten | ok | aten | ok | ok |
| BC-04 | Badges | aten | ok | ok | aten | ok | aten | ok | aten | ok | ok |
| BC-05 | Buttons | aten | ok | ok | **crit** | aten | ok | ok | ok | ok | ok |
| BC-06 | Cards | aten | ok | ok | aten | ok | aten | ok | aten | ok | ok |
| BC-07 | Carousels | aten | ok | **crit** | aten | aten | aten | ok | **crit** | ok | ok |
| BC-08 | Charts | — | — | — | — | — | — | — | — | — | — |
| BC-09 | Conf. Modals | aten | aten | aten | aten | **crit** | ok | ok | aten | aten | ok |
| BC-10 | Dropdowns | aten | ok | aten | aten | ok | aten | ok | ok | ok | ok |
| BC-11 | File Previews | aten | ok | ok | aten | aten | aten | ok | ok | aten | ok |
| BC-12 | Footers | ok | ok | ok | aten | ok | ok | ok | aten | ok | ok |
| BC-13 | Forms | **crit** | aten | aten | **crit** | **crit** | aten | ok | aten | **crit** | ok |
| BC-14 | Headers | aten | ok | ok | **crit** | ok | aten | ok | aten | ok | ok |
| BC-15 | Icons | ok | **crit** | ok | **crit** | ok | **crit** | ok | aten | ok | ok |
| BC-16 | Loaders | **crit** | ok | ok | aten | ok | aten | ok | aten | aten | ok |
| BC-17 | Maintenance | aten | aten | ok | aten | ok | ok | ok | ok | aten | ok |
| BC-18 | Skeleton Layers | — | — | — | — | — | — | — | — | — | — |
| BC-19 | Modals | ok | ok | **crit** | aten | ok | ok | ok | aten | ok | ok |
| BC-20 | Navigation Canvas | aten | ok | ok | aten | ok | aten | aten | aten | ok | ok |
| BC-21 | Objects | ok | aten | ok | aten | ok | **crit** | ok | ok | ok | aten |
| BC-22 | Offcanvas | ok | ok | **crit** | aten | ok | aten | ok | aten | ok | ok |
| BC-23 | Popovers | ok | ok | aten | aten | ok | aten | ok | aten | ok | ok |
| BC-24 | Route Selectors | aten | aten | ok | aten | ok | aten | ok | ok | ok | ok |
| BC-25 | Tables | aten | ok | ok | **crit** | ok | aten | **crit** | aten | ok | ok |
| BC-26 | Tabs | **crit** | ok | ok | aten | ok | ok | ok | aten | ok | ok |
| BC-27 | Toasts | **crit** | aten | **crit** | aten | ok | ok | ok | aten | aten | ok |
| BC-28 | Version | ok | ok | ok | aten | ok | ok | ok | aten | ok | aten |
| SC-01 | Atualiz. Recentes | aten | ok | ok | aten | ok | aten | ok | aten | ok | ok |
| SC-02 | Consultar Pessoa | aten | aten | aten | aten | aten | aten | aten | aten | **crit** | ok |
| SC-03 | Consultar Registro | aten | aten | aten | aten | aten | aten | aten | aten | **crit** | ok |
| SC-04 | Consultar Veículo | aten | aten | aten | aten | aten | aten | aten | aten | **crit** | ok |
| SC-05 | Pesquisa Objetos | — | — | — | — | — | — | — | — | — | — |
| SC-06 | Pesquisa Textual | aten | ok | aten | aten | ok | ok | aten | ok | aten | ok |
| SC-07 | Image Captures | aten | ok | aten | aten | aten | aten | ok | ok | aten | ok |
| SC-08 | Login | — | — | — | — | — | — | — | — | — | — |
| SC-09 | Logradouros | aten | aten | ok | aten | aten | aten | ok | ok | **crit** | ok |
| SC-10 | Notificações | **crit** | aten | **crit** | aten | ok | aten | ok | aten | ok | ok |
| SC-11 | Resource Trees | aten | aten | aten | aten | ok | aten | aten | aten | ok | ok |
| SC-12 | Session Control | **crit** | aten | **crit** | aten | **crit** | aten | ok | ok | **crit** | ok |
| SC-13 | Steppers | **crit** | ok | **crit** | aten | aten | aten | ok | aten | **crit** | ok |
| SC-14 | Timelines | aten | ok | ok | aten | ok | aten | ok | aten | ok | ok |
| SC-15 | Uploaders | **crit** | aten | aten | aten | **crit** | aten | ok | ok | **crit** | ok |
| SC-16 | Rel. Consultas | — | — | — | — | — | — | — | — | — | — |

---

## Ranking de heurísticas mais violadas

| Heurística | Críticos | Atenção | Total afetados |
|---|---|---|---|
| **H-4 Consistência** | 5 | 29 | 34 |
| **H-9 Recuperação de erros** | 8 | 8 | 16 |
| **H-1 Visibilidade de estado** | 8 | 16 | 24 |
| **H-3 Controle do usuário** | 7 | 6 | 13 |
| **H-8 Estética** | 2 | 21 | 23 |
| **H-6 Reconhecimento** | 3 | 17 | 20 |
| **H-5 Prevenção de erros** | 4 | 8 | 12 |
| **H-2 Mundo real** | 2 | 9 | 11 |
| **H-7 Flexibilidade** | 2 | 4 | 6 |
| **H-10 Ajuda** | 0 | 5 | 5 |

---

## Prioridade de resolução para a fase Figma

**Resolver primeiro (impacto sistêmico):**
1. **H-4 Consistência** — 4 padrões arquiteturais coexistindo. Solução: documentar qual padrão usar em qual situação. Aplicar `sispLib[Nome]Config` como padrão principal.
2. **SC-12 Session Control** — 4 violações críticas em componente de alto risco operacional
3. **BC-13 Forms** — 4 violações críticas no componente de maior risco de acessibilidade

**Resolver por tipo ao trabalhar cada componente:**
- **H-1** (visibilidade): toda ação assíncrona precisa de feedback de estado
- **H-9** (recuperação): toda mensagem de erro precisa dizer o que fazer
- **H-3** (controle): toda ação com consequência precisa de reversão ou confirmação
