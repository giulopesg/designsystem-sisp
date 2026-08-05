---
component-id: SC-15
component-name: Uploaders
type: SISP
status: in-figma
sprint: 5
approved-by: [Giuliana]
approved-date: [2026-07-22]
figma-node-id: [362:1179]
---

# Component Spec — Uploaders

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → SC-15 (cor aten · vis aten — label "Upload File" em inglês)
> - `docs/analyses/nielsen-analysis.md` → SC-15 (H-1 **crit** · H-5 **crit** · H-9 **crit** · H-2 aten · H-3 aten · H-4 aten · H-6 aten)
> - `docs/analyses/inventory.md` → SC-15
> - Nota: integração com BC-11 File Previews não documentada no inventário — spec define o par Upload→Preview

---

## O que é

Uploader é o componente de envio de arquivos do SISP. Permite ao usuário selecionar arquivos via botão ou drag-and-drop, com validação pré-upload (tipo e tamanho), barra de progresso durante o envio, e preview do arquivo anexado. Na DV, uploaders são usados para anexar evidências ao B.O. (fotos de ocorrência, documentos de identidade, laudos) e upload de documentos em medidas protetivas.

---

## Audiência de uso

- **Policial na DV:** usa o uploader para anexar evidências ao B.O. — fotos tiradas no local, documentos digitalizados, laudos. Precisa de feedback claro de que o arquivo foi aceito e enviado com sucesso
- **Devs CiASC / terceiros:** configuram tipos de arquivo aceitos, tamanho máximo, e callback de upload via Config object. Integram com BC-11 File Previews para mostrar arquivos já enviados
- **Demilis (mantenedor):** upload de evidências é funcionalidade crítica da DV — arquivos rejeitados ou perdidos impactam processos policiais
- **POs (Sommer/Holiwod):** evidências digitais são parte legal do B.O. — upload confiável é mandatório

---

## Props / API

> **Padrão de API:** Config object. Padrão documentado como-está para retrocompatibilidade.

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `onUpload` | `Function` | sim | — | Callback executado ao enviar arquivo. Recebe `File` ou `File[]` |
| `accept` | `string` | não | `'*'` | Tipos de arquivo aceitos (ex: `'.pdf,.jpg,.png'`) |
| `multiple` | `boolean` | não | `false` | Permite seleção de múltiplos arquivos |
| `maxFileSize` | `number` | não | `10` | Tamanho máximo por arquivo em MB |
| `maxFiles` | `number` | não | `5` | Número máximo de arquivos (quando `multiple: true`) |
| `dragAndDrop` | `boolean` | não | `true` | Habilita área de drag-and-drop |
| `showPreview` | `boolean` | não | `true` | Exibe preview do arquivo após upload (via BC-11) |
| `label` | `string` | não | `'Enviar arquivo'` | Texto do botão de upload |
| `hint` | `string` | não | — | Texto auxiliar (ex: "PDF, JPG ou PNG — máx. 10 MB") |
| `(uploadComplete)` | `EventEmitter<UploadResult>` | não | — | Emitido quando upload finaliza (sucesso ou erro) |
| `(fileRemoved)` | `EventEmitter<File>` | não | — | Emitido quando usuário remove arquivo da lista |

**UploadResult:**
```typescript
interface UploadResult {
  file: File;
  status: 'success' | 'error';
  url?: string;           // URL do arquivo no servidor (se sucesso)
  error?: string;         // Mensagem de erro (se falha)
  progress?: number;      // 0–100
}
```

**Convenção Angular:**
```html
<sisp-lib-uploader
  [sispLibUploaderConfig]="config"
  (uploadComplete)="onUploadComplete($event)"
  (fileRemoved)="onFileRemoved($event)">
</sisp-lib-uploader>
```

**Exemplo de uso (evidências no B.O.):**
```typescript
config: SispLibUploaderConfig = {
  accept: '.pdf,.jpg,.jpeg,.png,.heic',
  multiple: true,
  maxFileSize: 10,
  maxFiles: 10,
  dragAndDrop: true,
  showPreview: true,
  label: 'Anexar evidência',
  hint: 'PDF, JPG ou PNG — máx. 10 MB por arquivo',
  onUpload: (files) => this.evidenciaService.upload(files)
};
```

---

## Anatomia do componente

### Área de upload (default)
```
┌─────────────────────────────────────────┐
│                                         │
│        ☁ Arraste arquivos aqui          │
│        ou                               │
│        [Enviar arquivo]                 │
│                                         │
│   PDF, JPG ou PNG — máx. 10 MB          │
└─────────────────────────────────────────┘
```

### Com arquivo(s) anexado(s)
```
┌─────────────────────────────────────────┐
│  ☁ Arraste arquivos aqui ou [Enviar]    │
└─────────────────────────────────────────┘

┌──────────────────────────────────┐
│ 📄 foto_ocorrencia.jpg    2.3 MB │  ✓  ×
│ ████████████████████████ 100%    │
├──────────────────────────────────┤
│ 📄 laudo_pericial.pdf    4.1 MB  │  ⟳  —
│ ████████████░░░░░░░░░░░  45%     │
├──────────────────────────────────┤
│ 📄 arquivo_grande.zip   25 MB    │  ✕
│ ⚠ Arquivo excede o limite de 10 MB      │
└──────────────────────────────────┘
```

- **Drop zone:** área tracejada com ícone de nuvem, texto instrucional, e botão (instância BC-05 Button)
- **Hint:** texto auxiliar com tipos e tamanho aceitos (Body/XS muted)
- **File list:** lista de arquivos com BC-11 File Preview instances
- **Progress bar:** barra de progresso por arquivo (BC-16 Loader Bar Determinate padrão visual)
- **Status icons:** check (sucesso), spinner (enviando), erro (falha), × (remover)

> **Regra 11 — Composição atômica:** botão de upload é instância de BC-05 Button. File previews são instâncias de BC-11 File Preview. Ícones via BC-15 Icons. Barra de progresso segue padrão visual de BC-16 Loader Bar Determinate.

---

## Estados e variantes

### Estados do componente

| Estado | Descrição visual | Drop zone |
|---|---|---|
| **Default** | Área tracejada com ícone + texto + botão | Borda `--color-border` tracejada |
| **Drag Over** | Área destacada para indicar drop válido | Borda `--color-primary` sólida, fundo `--color-primary-bg` sutil |
| **Drag Invalid** | Arquivo sendo arrastado mas tipo inválido | Borda `--color-danger` sólida, ícone ✕ |
| **Uploading** | Arquivo(s) sendo enviados | Drop zone colapsada, lista com progress bars |
| **Complete** | Todos os arquivos enviados com sucesso | Lista com checks verdes |
| **Error** | Um ou mais arquivos falharam | Arquivo com status erro + mensagem |
| **Disabled** | Upload desabilitado | `opacity: 0.4`, cursor not-allowed |

### Status por arquivo

| Status | Indicador | Ícone | Ação disponível |
|---|---|---|---|
| **Queued** | Aguardando envio | fa-clock | Remover (×) |
| **Uploading** | Progress bar animada | fa-spinner (girando) | Cancelar (×) |
| **Success** | Progress bar 100% verde | fa-check (✓) | Remover (×) |
| **Error** | Mensagem de erro vermelha | fa-exclamation-triangle | Tentar novamente (⟳) · Remover (×) |

### Validação pré-upload (resolve H-5 crit)

| Validação | Quando | Mensagem |
|---|---|---|
| Tipo de arquivo | Antes do upload | "Tipo de arquivo não aceito. Use: PDF, JPG ou PNG" |
| Tamanho do arquivo | Antes do upload | "Arquivo excede o limite de {maxFileSize} MB" |
| Número de arquivos | Antes de adicionar | "Limite de {maxFiles} arquivos atingido" |
| Arquivo duplicado | Antes de adicionar | "Este arquivo já foi adicionado" |

> **Regra crítica:** validação acontece **antes** do upload iniciar, nunca depois. O arquivo rejeitado não é enviado ao servidor — erro é local e imediato.

### Verificação de contraste (WCAG AA)

| Elemento | Texto | Fundo | Ratio | Resultado |
|---|---|---|---|---|
| Hint text | #6B7280 | #FFFFFF | ≥ 4.6:1 | ✅ AA |
| File name | #08060F | #FFFFFF | ≥ 18.1:1 | ✅ AAA |
| Error message | #991B1B | #FEF2F2 | ≥ 5.7:1 | ✅ AAA |
| Success icon | #166534 | #FFFFFF | ≥ 7.5:1 | ✅ AAA |
| Progress bar | #C4000B | track #E5E7EB | ≥ 3:1 gráfico | ✅ |
| Drop zone border | #9CA3AF | #FFFFFF | ≥ 2.7:1 | ✅ (gráfico) |
| Drop zone text | #6B7280 | #FFFFFF | ≥ 4.6:1 | ✅ AA |

### Dimensões

| Propriedade | Valor | Token |
|---|---|---|
| Drop zone min-height | 120px | — |
| Drop zone border | 2px dashed | — |
| Drop zone border-radius | 8px | `--radius-md` |
| Drop zone padding | 24px | `--space-6` |
| File item height | auto (min 48px) | — |
| File item padding | 12px 16px | `--space-3` `--space-4` |
| File item gap | 12px | `--space-3` |
| Progress bar height | 4px | — |
| Progress bar border-radius | 9999px | `--radius-full` |
| Icon size (status) | 16px | BC-15 SM |
| Gap drop zone → file list | 16px | `--space-4` |
| Gap entre file items | 0 (divider 1px) | — |

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução neste spec |
|---|---|---|
| Uso de cor (cor aten) | Status do arquivo (sucesso/erro) diferenciado por cor | **3 canais:** (1) Cor do ícone (verde check / vermelho exclamação), (2) Ícone semântico (✓ / ! / ⟳), (3) Texto descritivo ("Enviado com sucesso" / "Falha no envio — tentar novamente"). Nunca depende apenas de cor |
| Visual (vis aten) | Label "Upload File" em inglês | **Label em português:** "Enviar arquivo" (default). Prop `label` permite customização. Hint text em português descrevendo tipos aceitos |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução neste spec |
|---|---|---|
| H-1 Visibilidade (CRIT) | Progresso de upload não documentado | **Progress bar por arquivo** com porcentagem visível. Status icon por arquivo (queued/uploading/success/error). Texto de status: "Enviando... 45%" / "Enviado com sucesso" / "Falha no envio". Counter geral: "3 de 5 arquivos enviados" |
| H-5 Prevenção (CRIT) | Sem validação de tipo/tamanho antes do upload — erro só aparece depois | **Validação pré-upload:** tipo e tamanho verificados antes de iniciar o envio. Arquivo rejeitado não é enviado — mensagem local imediata. Hint text documenta tipos e tamanho aceitos. Drag de tipo inválido mostra visual de rejeição |
| H-9 Recuperação (CRIT) | Mensagem de arquivo rejeitado não especificada | **Mensagens de erro específicas:** tipo inválido ("Tipo de arquivo não aceito. Use: PDF, JPG ou PNG"), tamanho excedido ("Arquivo excede o limite de 10 MB"), falha de rede ("Falha no envio — tentar novamente"). Botão de retry por arquivo. Remoção de arquivo com erro |
| H-2 Mundo real (aten) | Terminologia técnica | Labels em português, termos do domínio policial (ex: "Anexar evidência"), hint com exemplos de tipos aceitos |
| H-3 Controle (aten) | Sem cancelamento de upload | Botão × cancela upload em andamento. Botão × remove arquivo já enviado. Confirmação antes de remover arquivo já enviado com sucesso |
| H-4 Consistência (aten) | Sem padrão documentado | Comportamento consistente com plataforma (input file + drag-and-drop). Mesmos ícones de status que SC-13 (check/error). Mesma barra de progresso que BC-16 |
| H-6 Reconhecimento (aten) | Sem dicas visuais | Hint text com tipos e tamanho. Ícone de nuvem na drop zone. Preview do arquivo (thumbnail para imagens, ícone para documentos) via BC-11 |

---

## Regras de acessibilidade

- [ ] Drop zone com `role="button"` quando clicável e `aria-label="Enviar arquivo. Tipos aceitos: PDF, JPG, PNG. Tamanho máximo: 10 MB"`
- [ ] Input file hidden — ativado por click no drop zone ou botão
- [ ] **Drag-and-drop acessível:** drop zone focável por Tab, ativável por Enter/Space (abre file dialog)
- [ ] File list com `role="list"` e `role="listitem"` por arquivo
- [ ] Status do arquivo anunciado via `aria-live="polite"`: "foto_ocorrencia.jpg enviado com sucesso"
- [ ] Progresso anunciado via `aria-valuenow`, `aria-valuemin="0"`, `aria-valuemax="100"` na progress bar
- [ ] Botão remover com `aria-label="Remover foto_ocorrencia.jpg"`
- [ ] Botão retry com `aria-label="Tentar novamente laudo_pericial.pdf"`
- [ ] Erro de validação associado ao arquivo via `aria-describedby`
- [ ] Focus ring visível: `2px solid var(--color-border-focus)`
- [ ] Contraste verificado — mínimo 3:1 para gráficos, 4.5:1 para texto
- [ ] Labels em português
- [ ] Animação da progress bar respeita `prefers-reduced-motion`

---

## Comportamentos esperados

### Seleção de arquivo
- Quando clica no botão "Enviar arquivo" ou na drop zone → abre file dialog nativo do browser com filtro `accept`
- Quando arrasta arquivo sobre a drop zone → visual muda para Drag Over (borda sólida primary, fundo highlight)
- Quando arrasta arquivo de tipo inválido → visual muda para Drag Invalid (borda danger, ícone ✕)
- Quando solta arquivo na drop zone → validação pré-upload executa antes de qualquer envio

### Validação pré-upload
- Quando arquivo tem tipo não aceito → arquivo rejeitado localmente. Mensagem: "Tipo de arquivo não aceito. Use: {accept}". Arquivo não aparece na lista ou aparece com status Error imediato
- Quando arquivo excede `maxFileSize` → arquivo rejeitado localmente. Mensagem: "Arquivo excede o limite de {maxFileSize} MB"
- Quando `multiple: true` e número de arquivos excede `maxFiles` → arquivos excedentes rejeitados. Mensagem: "Limite de {maxFiles} arquivos atingido"
- Quando arquivo é duplicado (mesmo nome + tamanho) → rejeitado. Mensagem: "Este arquivo já foi adicionado"

### Upload
- Quando arquivo passa validação → entra na fila como Queued. `onUpload(file)` é chamado
- Quando upload inicia → status muda para Uploading. Progress bar anima de 0% a 100%
- Quando upload finaliza com sucesso → status Success. Check verde. Emite `(uploadComplete)` com `status: 'success'`
- Quando upload falha → status Error. Mensagem de erro. Botão retry disponível. Emite `(uploadComplete)` com `status: 'error'`
- Quando clica em retry → re-executa `onUpload(file)`. Status volta para Uploading
- Quando clica em × durante upload → upload cancelado. Arquivo removido da lista
- Quando clica em × em arquivo enviado → confirmação "Remover arquivo?" → se sim, remove e emite `(fileRemoved)`

### Preview
- Quando `showPreview: true` e arquivo é imagem → thumbnail (via BC-11 File Preview, Preview=Image)
- Quando `showPreview: true` e arquivo não é imagem → ícone do tipo (via BC-11 File Preview, Preview=Icon)
- Quando `showPreview: false` → apenas nome, tamanho e status

---

## Composição com outros componentes

> **Regra 11 — Composição atômica:** todo elemento que já exista como componente no DS deve ser instância.

| Componente | Relação | Composição no Figma |
|---|---|---|
| **BC-05 Button** | Botão "Enviar arquivo" na drop zone | **Instância direta** — Secondary SM (default) ou Primary SM (quando drop zone compacta) |
| **BC-11 File Previews** | Preview de cada arquivo na lista | **Instância direta** — Preview=Image (imagens) ou Preview=Icon (documentos) |
| **BC-16 Loader** | Progress bar de upload | **Padrão visual** — Bar Determinate (mesma linguagem, mas inline por arquivo) |
| **BC-15 Icons** | Ícones de status (check, error, retry, cloud) | **Font Awesome** — fa-check, fa-exclamation-triangle, fa-redo, fa-cloud-upload-alt, fa-times |

> **Regra 12 — auditoria:** "este elemento já existe?" Botão de upload existe (BC-05). Preview de arquivo existe (BC-11). Barra de progresso existe (BC-16 — padrão visual). A drop zone é custom — não existe como componente no DS (é específica de upload). Close/remove buttons (×) seguem padrão aceito de 24×24.

---

## Integração com BFF

> SC-15 usa padrão Config object. O componente não chama o BFF diretamente — o upload real é responsabilidade da app host via `onUpload`. O componente gerencia UI (validação local, progresso, status visual).

| Fluxo | Responsabilidade | Componente |
|---|---|---|
| Validar tipo/tamanho | SC-15 (local, pré-upload) | Validação client-side |
| Enviar arquivo | App host → `onUpload(file)` | SC-15 mostra progresso |
| Reportar sucesso/erro | App host → retorno de `onUpload` | SC-15 atualiza status visual |
| Remover arquivo do servidor | App host → `(fileRemoved)` handler | SC-15 remove da lista |

---

## Variantes no Figma

| Variante | Properties | Descrição |
|---|---|---|
| **Default** | `State=Default` | Drop zone vazia com botão e hint |
| **Drag Over** | `State=DragOver` | Drop zone com highlight (arquivo sendo arrastado) |
| **With Files** | `State=WithFiles` | Drop zone compacta + lista de arquivos (1 uploading, 1 success, 1 error) |
| **Complete** | `State=Complete` | Todos os arquivos enviados com sucesso |
| **Disabled** | `State=Disabled` | Drop zone desabilitada, opacidade reduzida |

---

## Comportamento responsivo

> Regra 13 — Responsividade obrigatória. Breakpoints: Mobile (<768px), Tablet (768–1023px), Desktop (≥1024px).

| Precisa de variante `Layout=Mobile`? | **Sim** — drop zone e file list precisam de largura reduzida |
|---|---|
| **Desktop** | Drop zone 480px. Padding 24px. File items padding 16px horizontal |
| **Mobile** | Drop zone 343px (375 - 2×16). Padding 16px. File items padding 12px horizontal. Drag-and-drop não suportado — botão abre seletor nativo |
| **Tablet** | Segue Desktop |

**Variantes no Figma:** 10 (5 estados × 2 layouts)

---

## Casos excepcionais / bordas

- **Arquivo de 0 bytes:** rejeitado com "Arquivo vazio"
- **Nome de arquivo muito longo (> 40 chars):** truncar com ellipsis, tooltip com nome completo
- **Upload de 10+ arquivos simultaneamente:** processar em fila (máx. 3 paralelos). Progress individual + counter geral
- **Perda de conexão durante upload:** status Error com "Falha na conexão — tentar novamente"
- **Mesmo arquivo selecionado duas vezes:** rejeitado com "Este arquivo já foi adicionado"
- **Browser sem suporte a drag-and-drop:** drop zone funciona apenas como botão (click → file dialog)
- **Mobile:** drop zone sem drag-and-drop (não suportado). Botão abre seletor nativo de arquivos/câmera
- **Arquivo HEIC (iPhone):** aceitar se `.heic` está em `accept`. Converter para preview se possível
- **Upload cancelado pelo usuário:** arquivo removido da lista silenciosamente
- **maxFiles atingido:** botão e drop zone desabilitados. Mensagem: "Remova um arquivo para adicionar outro"

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-primary` | Borda drop zone (drag over), progress bar fill |
| `--color-primary-bg` | Fundo drop zone (drag over) |
| `--color-success` | Ícone check (success) |
| `--color-danger` | Ícone error, mensagem de erro, borda drop zone (drag invalid) |
| `--color-danger-bg` | Fundo mensagem de erro |
| `--color-text-primary` | Nome do arquivo |
| `--color-text-secondary` | Tamanho do arquivo |
| `--color-text-tertiary` | Hint text, drop zone text |
| `--color-border` | Borda drop zone (default, tracejada) |
| `--color-border-focus` | Focus ring |
| `--color-surface` | Fundo geral |
| `--color-surface-secondary` | Fundo file item hover |
| `--radius-md` | Border-radius drop zone |
| `--radius-full` | Border-radius progress bar |
| `--space-3` | Padding file item, gap |
| `--space-4` | Gap drop zone → file list, padding horizontal |
| `--space-6` | Padding drop zone |

---

## O que está fora deste spec

- **Crop/redimensionamento de imagem:** se necessário, componente separado. Uploader apenas envia
- **Compressão client-side:** responsabilidade da app host antes de chamar `onUpload`
- **Upload resumable (chunks):** responsabilidade da app host. Componente gerencia UI
- **Galeria de arquivos já enviados:** responsabilidade do layout da tela, não do uploader
- **Integração direta com câmera:** captura de imagem é SC-07 Image Captures. Uploader é para arquivos já existentes no dispositivo
- **Assinatura digital de documentos:** fora do escopo do componente

---

## Critérios de aceite

- [ ] 5 variantes no Figma: Default, DragOver, WithFiles, Complete, Disabled
- [ ] Drop zone com borda tracejada, ícone de nuvem, texto instrucional, e botão BC-05 Button
- [ ] Validação pré-upload para tipo, tamanho, número e duplicidade — resolve H-5 crit
- [ ] Progress bar por arquivo com porcentagem — resolve H-1 crit
- [ ] Mensagens de erro específicas por tipo de falha — resolve H-9 crit
- [ ] 4 status por arquivo (Queued/Uploading/Success/Error) com ícone + cor + texto
- [ ] File previews como instâncias de BC-11 File Preview (Regra 11)
- [ ] Botão como instância de BC-05 Button SM (Regra 11)
- [ ] Hint text documenta tipos e tamanho aceitos
- [ ] Labels em português ("Enviar arquivo", não "Upload File")
- [ ] Drag-and-drop com visual feedback (DragOver / DragInvalid)
- [ ] Retry por arquivo que falhou
- [ ] Remoção de arquivo com confirmação (se já enviado)
- [ ] Contraste verificado — mínimo 3:1 para gráficos, 4.5:1 para texto
- [ ] ARIA documentado: `role="button"` na drop zone, `aria-live` para status, `aria-valuenow` na progress bar
- [ ] EventEmitters `(uploadComplete)` e `(fileRemoved)` documentados
- [ ] Violações WCAG (cor aten, vis aten) resolvidas
- [ ] Violações Nielsen (H-1 crit, H-5 crit, H-9 crit) resolvidas
- [ ] Revisado e aprovado por Giuliana
