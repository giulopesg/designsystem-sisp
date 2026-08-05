---
component-id: BC-11
component-name: File Previews
type: Base
status: approved
sprint: 4.1
approved-by: [Giuliana]
approved-date: [2026-07-16]
figma-node-id: [272:573]
---

# Component Spec — File Previews

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → BC-11 (cor aten · vis aten — tipo de arquivo indicado so por icone/cor)
> - `docs/analyses/nielsen-analysis.md` → BC-11 (H-1 aten · H-4 aten · H-5 aten · H-6 aten · H-9 aten)
> - `docs/analyses/inventory.md` → BC-11

---

## O que e

File Preview e o componente de visualizacao de arquivos anexados do DS SISP. Exibe informacoes de um arquivo (nome, tipo, tamanho) com preview visual quando possivel (imagens) e fallback para icone de tipo quando nao ha preview. Na DV, file previews aparecem em BOs com anexos (fotos, documentos PDF, laudos), na lista de arquivos enviados via SC-15 Uploaders, e em detalhes de ocorrencias. Inclui acao de download quando o arquivo e baixavel.

> **Regra 12 aplicada:** File Preview reutiliza BC-15 Icons (icone de tipo de arquivo) e BC-05 Button (botao de download/acao). Icones como instancias de BC-15, botoes como instancias de BC-05.

---

## Audiencia de uso

- **Policial na DV:** ve file previews ao consultar BOs com anexos — fotos de ocorrencias, documentos, laudos. Precisa identificar rapidamente o tipo de arquivo e fazer download
- **Devs CiASC / terceiros:** usam file previews para exibir arquivos retornados pelo BFF. Precisam de API com nome, URL, tipo e opcao de download
- **POs (Sommer/Holiwod):** precisam que arquivos anexados sejam visiveis e acessiveis de forma clara

---

## Props / API

| Prop | Tipo | Obrigatorio | Padrao | Descricao |
|---|---|---|---|---|
| `file` | `FileInfo` | sim | — | Informacoes do arquivo |
| `downloadable` | `boolean` | nao | `true` | Exibe botao de download |
| `previewable` | `boolean` | nao | `true` | Tenta exibir preview visual (imagens) |
| `onDownload` | `Function` | nao | download nativo | Callback customizado ao clicar em download |
| `onPreview` | `Function` | nao | — | Callback ao clicar no preview (ex: abrir modal com imagem ampliada) |

**FileInfo:**
```typescript
interface FileInfo {
  name: string;         // Nome do arquivo com extensao (ex: 'laudo-pericial.pdf')
  url: string;          // URL do arquivo para download/preview
  type: string;         // MIME type (ex: 'application/pdf', 'image/jpeg')
  size?: number;        // Tamanho em bytes (exibido formatado: KB, MB)
  uploadedAt?: string;  // Data de upload (ISO 8601)
}
```

**Convencao Angular:**
```html
<sisp-lib-file-preview [sispLibFilePreviewConfig]="config"></sisp-lib-file-preview>
```

**Exemplo de uso:**
```typescript
// Arquivo PDF
pdfConfig: SispLibFilePreviewConfig = {
  file: {
    name: 'laudo-pericial-2024.pdf',
    url: '/api/files/12345',
    type: 'application/pdf',
    size: 2457600  // 2.4 MB
  },
  downloadable: true
};

// Imagem com preview
imageConfig: SispLibFilePreviewConfig = {
  file: {
    name: 'foto-ocorrencia-01.jpg',
    url: '/api/files/67890',
    type: 'image/jpeg',
    size: 1048576  // 1.0 MB
  },
  previewable: true,
  onPreview: () => this.openImageModal()
};
```

---

## Anatomia do componente

### Com preview (imagem)
```
┌────────────────────────────────────────────┐
│  ┌──────────┐                              │
│  │          │  foto-ocorrencia-01.jpg       │  ← nome do arquivo
│  │  [IMAGE] │  JPEG · 1.0 MB               │  ← tipo + tamanho
│  │          │                    [Download] │  ← botao BC-05
│  └──────────┘                              │
└────────────────────────────────────────────┘
```

### Sem preview (fallback icone)
```
┌────────────────────────────────────────────┐
│                                            │
│  [PDF icon]  laudo-pericial-2024.pdf       │  ← icone de tipo + nome
│              PDF · 2.4 MB                  │  ← tipo + tamanho
│                              [Download]    │  ← botao BC-05
│                                            │
└────────────────────────────────────────────┘
```

- **Container:** card sutil com borda e border-radius
- **Preview zone:** thumbnail da imagem (quando previewable e tipo imagem) ou icone de tipo de arquivo (BC-15 Icons)
- **Info zone:** nome do arquivo + tipo formatado + tamanho formatado
- **Action zone:** botao de download (BC-05 Button Tertiary SM)

---

## Estados e variantes

| Estado / Variante | Descricao visual | Tokens usados |
|---|---|---|
| **Default** | Card com informacoes do arquivo | `bg: --color-surface` · `border: --color-border` |
| **Hover** | Card com fundo sutil | `bg: --color-bg-subtle` |
| **With preview** | Thumbnail da imagem visivel | Preview 64×64 com border-radius |
| **Without preview** | Icone de tipo de arquivo (BC-15 Icons MD) | Icone colorido por tipo |
| **Downloading** | Loader inline no botao de download | BC-16 Loader Spinner SM no botao |
| **Error** | Arquivo indisponivel | Icone de erro + mensagem "Arquivo indisponivel" |

### Mapeamento icone por tipo de arquivo

| Tipo (MIME) | Icone FA | Cor | Label acessivel |
|---|---|---|---|
| `application/pdf` | `fa-solid fa-file-pdf` | `--color-danger` | PDF |
| `image/*` | `fa-solid fa-file-image` | `--color-info` | Imagem |
| `application/msword`, `application/vnd.openxmlformats*doc*` | `fa-solid fa-file-word` | `--color-info` | Documento |
| `application/vnd.ms-excel`, `application/vnd.openxmlformats*sheet*` | `fa-solid fa-file-excel` | `--color-success` | Planilha |
| `text/*` | `fa-solid fa-file-lines` | `--color-text-secondary` | Texto |
| `video/*` | `fa-solid fa-file-video` | `--color-warning` | Video |
| `audio/*` | `fa-solid fa-file-audio` | `--color-warning` | Audio |
| Outros / desconhecido | `fa-solid fa-file` | `--color-text-muted` | Arquivo |

> **WCAG:** icone + label textual do tipo (ex: "PDF · 2.4 MB") — nunca apenas icone/cor.

### Cores

| Elemento | Token | Valor |
|---|---|---|
| Container fundo | `--color-surface` | #FFFFFF |
| Container borda | `--color-border` | #E5E7EB |
| Container hover | `--color-bg-subtle` | #F9FAFB |
| Nome do arquivo | `--color-text-primary` | #08060F |
| Tipo + tamanho | `--color-text-secondary` | #4B5563 |
| Erro | `--color-danger` | #991B1B |
| Preview border | `--color-border` | #E5E7EB |

### Verificacao de contraste (WCAG AA)

| Elemento | Texto | Fundo | Ratio | Resultado |
|---|---|---|---|---|
| Nome do arquivo | #08060F | #FFFFFF | >15:1 | ✅ AAA |
| Tipo + tamanho | #4B5563 | #FFFFFF | ~7.2:1 | ✅ AAA |
| Erro | #991B1B | #FFFFFF | ~6.5:1 | ✅ AAA |
| Nome hover | #08060F | #F9FAFB | ~14.8:1 | ✅ AAA |

### Dimensoes

| Propriedade | Valor | Token |
|---|---|---|
| Container min height | 64px | — |
| Container padding | 12px | `--space-3` |
| Container border-radius | 8px | `--radius-lg` |
| Container border | 1px solid | — |
| Preview thumbnail | 64×64px | — |
| Preview border-radius | 4px | `--radius-sm` |
| Icone tamanho (fallback) | 32px (LG) | — |
| Gap preview/icone → info | 12px | `--space-3` |
| Gap nome → tipo | 4px | `--space-1` |
| Nome font size | 14px | `--text-sm` |
| Nome font weight | 500 | `--font-medium` |
| Tipo + tamanho font size | 12px | `--text-xs` |
| Tipo + tamanho font weight | 400 | `--font-regular` |
| Botao download | BC-05 Tertiary SM | — |

---

## Violacoes a resolver — WCAG AA

| Dimensao | Violacao atual | Solucao neste spec |
|---|---|---|
| Uso de cor (cor aten) | Tipo de arquivo indicado so por icone/cor | 2 canais: (1) icone especifico por tipo (fa-file-pdf, fa-file-image, etc.), (2) label textual do tipo ("PDF", "JPEG", "Documento"). Cor e reforço, nao canal unico |
| Visual (vis aten) | Sem refinamento visual | Container card com borda, border-radius --radius-lg, hover sutil. Preview com thumbnail ou icone tipado. Espacamento por tokens |

---

## Violacoes a resolver — Heuristicas Nielsen

| Heuristica | Violacao atual | Solucao neste spec |
|---|---|---|
| H-1 Visibilidade (aten) | Sem feedback durante download | Estado downloading com spinner inline no botao. Botao muda label para "Baixando..." durante operacao. Feedback visual claro de progresso |
| H-4 Consistencia (aten) | Sem padrao documentado | Mapeamento icone/cor/label por MIME type padronizado. Mesmo visual em todo o DS. Card style consistente com BC-06 Cards (borda + radius + padding) |
| H-5 Prevencao de erros (aten) | Sem validacao antes de download | Download de arquivo grande (> 10 MB) exibe tamanho no label do botao ("Baixar (15.2 MB)") como aviso implicito. Confirmacao nao necessaria — download e reversivel |
| H-6 Reconhecimento (aten) | Tipos de arquivo dificeis de identificar | Icone especifico por tipo + label textual + cor de reforço. Preview visual para imagens. Mapa semantico de icones documentado |
| H-9 Recuperacao de erros (aten) | Sem tratamento de arquivo indisponivel | Estado Error com icone de erro + mensagem "Arquivo indisponivel" + botao "Tentar novamente" (BC-05 Tertiary SM). Fallback gracil |

---

## Regras de acessibilidade

- [ ] Container com `role="group"` e `aria-label` descritivo (ex: "Arquivo: laudo-pericial.pdf")
- [ ] Icone de tipo com `aria-hidden="true"` (tipo comunicado via texto)
- [ ] Tipo do arquivo como texto visivel — nunca so icone
- [ ] Botao de download com `aria-label="Baixar laudo-pericial.pdf (2.4 MB)"` — inclui nome e tamanho
- [ ] Preview de imagem com `alt` descritivo (nome do arquivo)
- [ ] Focus ring no container e no botao: `2px solid var(--color-border-focus)`
- [ ] Container navegavel por teclado (Tab → foca container → Tab → foca botao download)
- [ ] Contraste minimo 4.5:1 em todos os textos — verificado
- [ ] Tamanho formatado em unidades legiveis (KB, MB, GB)
- [ ] Labels em portugues ("Baixar", "Arquivo indisponivel", "Tentar novamente")

---

## Comportamentos esperados

- Quando `previewable = true` e tipo = imagem → exibe thumbnail 64×64 do arquivo
- Quando `previewable = true` e tipo != imagem → exibe icone de tipo (fallback)
- Quando `previewable = false` → sempre exibe icone de tipo, mesmo para imagens
- Quando `downloadable = true` → botao de download visivel (BC-05 Tertiary SM com icone fa-download)
- Quando `downloadable = false` → sem botao de download (visualizacao apenas)
- Quando usuario clica no download → botao entra em estado "downloading" (spinner + "Baixando..."). Ao concluir, retorna ao estado default
- Quando download falha → botao exibe "Erro" momentaneamente, retorna ao estado default
- Quando `onPreview` e fornecido e usuario clica no preview/container → callback executa (ex: abre modal com imagem ampliada)
- Quando arquivo nao existe (URL 404) → estado Error com mensagem "Arquivo indisponivel"
- Quando `size` nao e fornecido → campo de tamanho nao exibe
- Quando `uploadedAt` e fornecido → data formatada exibida abaixo do tamanho ("Enviado em 15/07/2026")
- Quando nome do arquivo e muito longo (> 40 chars) → trunca com ellipsis. `title` com nome completo

---

## Formatacao de tamanho

| Bytes | Exibicao |
|---|---|
| < 1024 | "X bytes" |
| < 1048576 (1 MB) | "X.X KB" |
| < 1073741824 (1 GB) | "X.X MB" |
| >= 1 GB | "X.X GB" |

---

## Composicao com outros componentes

| Componente | Relacao | Composicao no Figma (Regra 11/12) |
|---|---|---|
| **BC-15 Icons** | **Icone de tipo de arquivo (fallback quando sem preview)** | **Instancia direta** — icone LG (32px) com cor semantica por tipo |
| **BC-05 Button** | **Botao de download e botao "Tentar novamente"** | **Instancia direta** — Tertiary SM com icone fa-download |
| SC-15 Uploaders | Upload produz arquivo → File Preview exibe | Composicao de uso — par Upload→Preview documentado |
| BC-19 Modals | Preview de imagem pode abrir em modal ampliada | Composicao de uso |

---

## Mapeamento de retrocompatibilidade

| Prop atual | Mapeamento novo | Nota |
|---|---|---|
| `file: {name, url, type, size}` | `file: FileInfo` | Extendido com `uploadedAt` |
| `downloadable` | `downloadable` | Mantido |
| — | `previewable` (novo) | Controle de exibicao de preview visual |
| — | `onDownload` (novo) | Callback customizado |
| — | `onPreview` (novo) | Callback para preview ampliado |

---

## Casos excepcionais / bordas

- **Arquivo sem extensao:** tipo inferido do MIME type. Se MIME desconhecido, icone generico (fa-file)
- **Arquivo muito grande (> 100 MB):** exibe aviso no label do tamanho ("102.4 MB — arquivo grande")
- **URL invalida:** estado Error imediato
- **Multiplos file previews:** lista vertical com gap --space-3 entre items
- **Mobile (< 640px):** layout se adapta — preview/icone acima, info e botao abaixo (empilhado)
- **Imagem corrompida / timeout de preview:** fallback para icone de tipo (fa-file-image)
- **Nome com caracteres especiais:** sanitizado pelo Angular automaticamente
- **Arquivo de 0 bytes:** exibe "0 bytes" — pode indicar arquivo corrompido

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-surface` | Fundo container |
| `--color-bg-subtle` | Fundo hover |
| `--color-border` | Borda container, preview |
| `--color-text-primary` | Nome do arquivo |
| `--color-text-secondary` | Tipo + tamanho |
| `--color-text-muted` | Icone tipo generico |
| `--color-danger` | Icone PDF, mensagem de erro |
| `--color-info` | Icone imagem, documento |
| `--color-success` | Icone planilha |
| `--color-warning` | Icone video, audio |
| `--color-border-focus` | Focus ring |
| `--radius-lg` | Border radius container (8px) |
| `--radius-sm` | Border radius preview (4px) |
| `--font-body` | Familia tipografica |
| `--text-sm` | Font size nome (14px) |
| `--text-xs` | Font size tipo + tamanho (12px) |
| `--font-medium` | Peso nome (500) |
| `--font-regular` | Peso tipo + tamanho (400) |
| `--space-3` | Padding container, gap preview→info |
| `--space-1` | Gap nome→tipo |
| `--shadow-none` | Sem sombra (card sutil) |

---

## O que esta fora deste spec

- **File preview com edicao (rename, delete inline):** acao destrutiva usa BC-10 Dropdown ou BC-19 Modal Confirmation
- **File preview com progresso de upload:** responsabilidade do SC-15 Uploaders. File Preview exibe o resultado final
- **Galeria de imagens (grid de thumbnails):** componente de composicao — multiplos File Previews em grid. Nao e responsabilidade deste componente unitario
- **Preview de video inline (player):** complexidade excessiva. Exibir thumbnail + botao de download
- **Preview de PDF inline:** nao e padrao SISP. Download do PDF e suficiente
- **Drag-and-drop para reordenar:** nao e caso de uso do SISP

---

## Criterios de aceite

- [ ] 2 variantes visuais no Figma: com preview (imagem) e sem preview (icone)
- [ ] Estado de erro documentado ("Arquivo indisponivel")
- [ ] Mapeamento icone/cor/label por tipo de arquivo (8 categorias MIME)
- [ ] Botao de download como instancia BC-05 Button Tertiary SM (Regra 11)
- [ ] Icone de tipo como referencia BC-15 Icons LG (Regra 11)
- [ ] Tipo de arquivo com 2 canais (icone + label textual) — resolve cor aten
- [ ] Contraste verificado — nome >15:1, tipo 7.2:1 (ambos AAA)
- [ ] ARIA documentado: `role="group"`, `aria-label` descritivo no botao download
- [ ] Formatacao de tamanho padronizada (KB, MB, GB)
- [ ] Violacoes WCAG (cor aten · vis aten) resolvidas
- [ ] Violacoes Nielsen (H-1 · H-4 · H-5 · H-6 · H-9 aten) resolvidas
- [ ] Integracao com SC-15 Uploaders documentada
- [ ] Mapeamento de retrocompatibilidade documentado
- [ ] Revisado e aprovado por Giuliana
