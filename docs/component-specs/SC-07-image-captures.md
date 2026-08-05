---
component-id: SC-07
component-name: Image Captures
type: SISP
status: in-figma
sprint: 5
approved-by: [Giuliana]
approved-date: [2026-07-22]
figma-node-id: [367:1261]
---

# Component Spec — Image Captures

> Fontes consultadas:
> - `docs/analyses/wcag-analysis.md` → SC-07 (vis aten — label "Open Camera" em inglês)
> - `docs/analyses/nielsen-analysis.md` → SC-07 (H-1 aten · H-3 aten · H-4 aten · H-5 aten · H-6 aten · H-9 aten — nenhum crit)
> - `docs/analyses/inventory.md` → SC-07
> - Nota: convenção Angular `sispLibImageConfig` fora do padrão — deveria ser `sispLibImageCaptureConfig`

---

## O que é

Image Capture é o componente de captura de imagem via câmera do dispositivo. Permite ao usuário ativar a câmera, visualizar o feed ao vivo, capturar uma foto, e confirmar ou descartar antes de enviar. Na DV, é usado para fotografar evidências no local da ocorrência (documentos físicos, veículos, lesões) diretamente pelo navegador do dispositivo móvel ou desktop com webcam.

---

## Audiência de uso

- **Policial na DV:** usa o Image Capture para fotografar evidências diretamente no local da ocorrência — documentos de identidade, veículos, marcas de colisão, lesões corporais. Precisa de feedback claro sobre permissão de câmera e confirmação da foto antes de anexar ao B.O.
- **Devs CiASC / terceiros:** configuram o callback `actionAfterCapture` para receber a imagem capturada (base64 ou Blob). Integram com SC-15 Uploaders para envio ao servidor, ou processam localmente
- **Demilis (mantenedor):** captura de evidência fotográfica é parte crítica do fluxo policial — foto ilegível ou perdida impacta o processo
- **POs (Sommer/Holiwod):** evidência digital é parte legal do B.O. — captura confiável é mandatória

---

## Props / API

> **Padrão de API:** Config object. Convenção atual `sispLibImageConfig` — **fora do padrão** (deveria ser `sispLibImageCaptureConfig`). Documentado como-está para retrocompatibilidade. Recomendação: corrigir na refatoração Angular (Sprint 12).

| Prop | Tipo | Obrigatório | Padrão | Descrição |
|---|---|---|---|---|
| `actionAfterCapture` | `Function` | sim | — | Callback executado após captura confirmada. Recebe `{ image: Blob, dataUrl: string }` |
| `facing` | `'user' \| 'environment'` | não | `'environment'` | Câmera preferida: `environment` (traseira) ou `user` (frontal/selfie) |
| `quality` | `number` | não | `0.85` | Qualidade JPEG (0–1). Para evidências policiais, manter ≥ 0.8 |
| `maxWidth` | `number` | não | `1920` | Largura máxima em pixels. Redimensiona proporcionalmente |
| `maxHeight` | `number` | não | `1080` | Altura máxima em pixels. Redimensiona proporcionalmente |
| `showPreview` | `boolean` | não | `true` | Exibe preview da imagem capturada antes de confirmar |
| `label` | `string` | não | `'Abrir câmera'` | Texto do botão trigger |
| `(captureComplete)` | `EventEmitter<CaptureResult>` | não | — | Emitido quando captura é confirmada |
| `(captureCancel)` | `EventEmitter<void>` | não | — | Emitido quando usuário cancela/descarta |
| `(permissionDenied)` | `EventEmitter<void>` | não | — | Emitido quando permissão de câmera é negada |

**CaptureResult:**
```typescript
interface CaptureResult {
  image: Blob;
  dataUrl: string;       // base64 data URL para preview
  width: number;
  height: number;
  timestamp: Date;
}
```

**Convenção Angular (atual — fora do padrão):**
```html
<sisp-lib-image
  [sispLibImageConfig]="config"
  (captureComplete)="onCaptureComplete($event)"
  (captureCancel)="onCaptureCancel()">
</sisp-lib-image>
```

**Convenção Angular (recomendada — Sprint 12):**
```html
<sisp-lib-image-capture
  [sispLibImageCaptureConfig]="config"
  (captureComplete)="onCaptureComplete($event)">
</sisp-lib-image-capture>
```

**Exemplo de uso (evidência fotográfica no B.O.):**
```typescript
config: SispLibImageConfig = {
  facing: 'environment',
  quality: 0.85,
  maxWidth: 1920,
  showPreview: true,
  label: 'Fotografar evidência',
  actionAfterCapture: (result) => this.evidenciaService.anexarFoto(result)
};
```

---

## Anatomia do componente

### Estado Default (botão trigger)
```
┌──────────────────────────┐
│  📷  Abrir câmera        │
└──────────────────────────┘
```

### Câmera aberta (viewfinder)
```
┌─────────────────────────────────────────┐
│                                         │
│                                         │
│          [Feed da câmera]               │
│                                         │
│                                         │
│                                         │
├─────────────────────────────────────────┤
│  [🔄 Trocar câmera]      [📸 Capturar] │
└─────────────────────────────────────────┘
```

### Preview da captura
```
┌─────────────────────────────────────────┐
│                                         │
│                                         │
│          [Imagem capturada]             │
│                                         │
│                                         │
│                                         │
├─────────────────────────────────────────┤
│  [✕ Descartar]           [✓ Confirmar] │
└─────────────────────────────────────────┘
```

### Permissão negada
```
┌─────────────────────────────────────────┐
│                                         │
│        🔒                               │
│        Acesso à câmera negado           │
│        Habilite o acesso nas            │
│        configurações do navegador       │
│                                         │
│        [Tentar novamente]               │
└─────────────────────────────────────────┘
```

- **Trigger:** botão com ícone de câmera + label (instância BC-05 Button)
- **Viewfinder:** área de feed da câmera com controles na base
- **Controles da câmera:** botão capturar (Primary) + botão trocar câmera (Secondary)
- **Controles de preview:** botão descartar (Secondary/Danger) + botão confirmar (Primary)
- **Ícones:** via BC-15 Icons (fa-camera, fa-sync-alt, fa-check, fa-times)

> **Regra 11 — Composição atômica:** botão trigger é instância de BC-05 Button. Botões de ação (capturar, confirmar, descartar, trocar câmera, tentar novamente) são instâncias de BC-05 Button. Ícones via BC-15 Icons.

---

## Estados e variantes

### Estados do componente

| Estado | Descrição visual | Transição |
|---|---|---|
| **Default** | Botão trigger com ícone de câmera + label | Click → solicita permissão → Camera Active |
| **Camera Active** | Feed de vídeo ao vivo + controles (capturar, trocar câmera) | Click capturar → Preview |
| **Preview** | Imagem estática capturada + controles (confirmar, descartar) | Confirmar → executa callback. Descartar → Camera Active |
| **Permission Denied** | Mensagem de permissão negada + ícone de cadeado + botão retry | Retry → solicita permissão novamente |
| **Device Error** | Mensagem de erro de dispositivo (câmera não encontrada, em uso) + botão retry | Retry → tenta inicializar câmera |
| **Disabled** | Botão trigger desabilitado | `opacity: 0.4`, cursor not-allowed |

### Fluxo de estados

```
Default
  │ click
  ▼
[Solicitar permissão]
  │              │
  ▼              ▼
Camera Active   Permission Denied
  │                │ retry
  │                └──→ [Solicitar permissão]
  │ capturar
  ▼
Preview
  │         │
  ▼         ▼
Confirmar   Descartar
  │            │
  ▼            ▼
Default     Camera Active
(callback)
```

### Verificação de contraste (WCAG AA)

| Elemento | Texto | Fundo | Ratio | Resultado |
|---|---|---|---|---|
| Botão trigger (Primary) | #FFFFFF | #C4000B | ≥ 5.2:1 | ✅ AA |
| Botão trigger (Secondary) | #08060F | #FFFFFF | ≥ 18.1:1 | ✅ AAA |
| Mensagem permissão negada | #08060F | #F5F5F5 | ≥ 16:1 | ✅ AAA |
| Hint text (instrução) | #6B7280 | #F5F5F5 | ≥ 4.6:1 | ✅ AA |
| Botão descartar (Danger) | #FFFFFF | #991B1B | ≥ 7.8:1 | ✅ AAA |

### Dimensões

| Propriedade | Valor | Token |
|---|---|---|
| Viewfinder aspect ratio | 4:3 (padrão câmera) | — |
| Viewfinder max-width | 640px | — |
| Viewfinder max-height | 480px | — |
| Viewfinder border-radius | 8px | `--radius-md` |
| Controls bar height | auto (min 56px) | — |
| Controls bar padding | 12px 16px | `--space-3` `--space-4` |
| Controls bar gap | 16px | `--space-4` |
| Permission denied padding | 32px | `--space-8` |
| Permission denied gap (text → button) | 16px | `--space-4` |
| Container border-radius | 12px | `--radius-lg` |
| Container border | 1px solid | — |

---

## Violações a resolver — WCAG AA

| Dimensão | Violação atual | Solução neste spec |
|---|---|---|
| Visual (vis aten) | Label "Open Camera" em inglês | **Label em português:** "Abrir câmera" (default). Prop `label` permite customização (ex: "Fotografar evidência"). Todas as mensagens de interface em português: "Acesso à câmera negado", "Câmera não encontrada", "Capturar", "Confirmar", "Descartar" |

---

## Violações a resolver — Heurísticas Nielsen

| Heurística | Violação atual | Solução neste spec |
|---|---|---|
| H-1 Visibilidade (aten) | Sem feedback de estado da câmera (ativa/capturando/processando) | **Feedback por estado:** Default → botão com ícone de câmera. Camera Active → feed ao vivo (indicador visual de que câmera está ligada). Preview → imagem estática com controles de ação. Permission Denied → ícone de cadeado + mensagem explicativa. Device Error → ícone de erro + mensagem descritiva |
| H-3 Controle (aten) | Sem forma de cancelar ou descartar captura | **Controle total do fluxo:** botão "Descartar" na preview (volta para câmera ativa). Botão "Trocar câmera" (frontal ↔ traseira). Botão fechar no viewfinder (volta para Default). Toda ação do usuário é reversível antes da confirmação |
| H-4 Consistência (aten) | Convenção Angular fora do padrão (`sispLibImageConfig`) | **Documentar correção:** convenção atual documentada como-está. Recomendação de correção para `sispLibImageCaptureConfig` na refatoração (Sprint 12). Comportamento visual segue padrões do DS (mesmos botões, mesmos tokens) |
| H-5 Prevenção (aten) | Sem confirmação antes de enviar foto | **Preview obrigatório (default):** `showPreview: true` por padrão. Usuário vê a foto antes de confirmar. Botão "Descartar" permite nova tentativa sem risco. Impede envio acidental de foto borrada ou errada |
| H-6 Reconhecimento (aten) | Sem dicas visuais sobre o que fazer | **Affordances claras:** botão trigger com ícone de câmera + label "Abrir câmera". Controles do viewfinder com ícones + labels. Mensagem de permissão negada com instrução ("Habilite o acesso nas configurações do navegador"). Ícone de câmera é universalmente reconhecido |
| H-9 Recuperação (aten) | Mensagens de erro não especificadas | **Mensagens específicas:** Permissão negada → "Acesso à câmera negado. Habilite o acesso nas configurações do navegador." + botão "Tentar novamente". Câmera não encontrada → "Nenhuma câmera foi detectada neste dispositivo." Câmera em uso → "A câmera está sendo usada por outro aplicativo. Feche-o e tente novamente." Erro genérico → "Não foi possível acessar a câmera. Tente novamente." |

---

## Regras de acessibilidade

- [ ] Botão trigger com `aria-label="Abrir câmera para capturar imagem"`
- [ ] Viewfinder com `role="img"` e `aria-label="Feed da câmera ao vivo"` quando ativo
- [ ] Botão capturar com `aria-label="Capturar foto"`
- [ ] Botão trocar câmera com `aria-label="Trocar para câmera {frontal/traseira}"`
- [ ] Botão confirmar com `aria-label="Confirmar foto capturada"`
- [ ] Botão descartar com `aria-label="Descartar foto e capturar novamente"`
- [ ] Preview com `role="img"` e `aria-label="Foto capturada — revisar antes de confirmar"`
- [ ] Mensagem de permissão negada com `role="alert"` para anúncio imediato
- [ ] Mensagem de erro de dispositivo com `role="alert"`
- [ ] Mudanças de estado anunciadas via `aria-live="polite"`: "Câmera ativada", "Foto capturada", "Foto descartada"
- [ ] Focus ring visível: `2px solid var(--color-border-focus)`
- [ ] Todos os botões navegáveis por teclado (Tab, Enter/Space)
- [ ] Contraste verificado — mínimo 4.5:1 para texto
- [ ] Labels em português
- [ ] Animação de transição entre estados respeita `prefers-reduced-motion`

---

## Comportamentos esperados

### Ativação da câmera
- Quando clica no botão trigger → solicita `navigator.mediaDevices.getUserMedia()` com `{ video: { facingMode: facing } }`
- Quando permissão concedida → feed de vídeo ao vivo no viewfinder. Estado → Camera Active
- Quando permissão negada → mensagem de permissão negada com instrução e botão retry. Emite `(permissionDenied)`
- Quando câmera não encontrada → mensagem de erro de dispositivo com botão retry
- Quando câmera em uso por outro app → mensagem específica com instrução

### Captura
- Quando clica em "Capturar" → frame do vídeo é extraído como imagem estática
- Quando `showPreview: true` → imagem é exibida na preview com controles confirmar/descartar
- Quando `showPreview: false` → imagem é enviada diretamente ao callback (sem preview)
- Quando confirma → executa `actionAfterCapture({ image, dataUrl, width, height, timestamp })`. Emite `(captureComplete)`. Estado volta para Default. Câmera é desligada (stream parado)
- Quando descarta → estado volta para Camera Active. Feed ao vivo retorna. Usuário pode capturar novamente

### Troca de câmera
- Quando clica em "Trocar câmera" → alterna entre `facingMode: 'environment'` e `facingMode: 'user'`
- Quando dispositivo tem apenas uma câmera → botão de troca não é exibido

### Fechamento
- Quando clica em fechar (×) no viewfinder → câmera é desligada (stream parado). Estado volta para Default. Emite `(captureCancel)`
- Quando componente é destruído (Angular `ngOnDestroy`) → stream de vídeo é parado para liberar câmera

---

## Composição com outros componentes

> **Regra 11 — Composição atômica:** todo elemento que já exista como componente no DS deve ser instância.

| Componente | Relação | Composição no Figma |
|---|---|---|
| **BC-05 Button** | Botão trigger "Abrir câmera" | **Instância direta** — Secondary MD (com ícone fa-camera) |
| **BC-05 Button** | Botão "Capturar" | **Instância direta** — Primary MD |
| **BC-05 Button** | Botão "Confirmar" | **Instância direta** — Primary SM |
| **BC-05 Button** | Botão "Descartar" | **Instância direta** — Danger SM |
| **BC-05 Button** | Botão "Trocar câmera" | **Instância direta** — Secondary SM (com ícone fa-sync-alt) |
| **BC-05 Button** | Botão "Tentar novamente" | **Instância direta** — Secondary SM |
| **BC-15 Icons** | Ícones de câmera, cadeado, erro | **Font Awesome** — fa-camera, fa-sync-alt, fa-lock, fa-exclamation-triangle, fa-check, fa-times |

> **Regra 12 — auditoria:** "este elemento já existe?" Botões existem (BC-05). Ícones existem (BC-15/FA). O viewfinder (feed de câmera ao vivo) é custom — não existe como componente no DS (é específico de captura de imagem com hardware). A preview de imagem capturada é custom — BC-11 File Preview é para arquivos já existentes, não para foto recém-tirada exibida inline. Close button (×) no viewfinder segue padrão aceito de 24×24.

---

## Integração com hardware / browser

> SC-07 depende de APIs de hardware do browser. O componente gerencia o ciclo de vida da câmera (solicitar permissão → iniciar stream → capturar frame → parar stream).

| API | Uso | Fallback |
|---|---|---|
| `navigator.mediaDevices.getUserMedia()` | Acesso ao feed de vídeo da câmera | Mensagem "Seu navegador não suporta acesso à câmera" |
| `<video>` element | Exibe feed ao vivo | — |
| `<canvas>` element | Captura frame do vídeo como imagem | — |
| `canvas.toBlob()` / `canvas.toDataURL()` | Converte frame para Blob/base64 | — |
| `MediaStream.getTracks().forEach(t => t.stop())` | Para câmera ao fechar/destruir | Essencial — câmera ligada sem uso drena bateria |

### Compatibilidade

| Browser | Suporte | Nota |
|---|---|---|
| Chrome (Android/Desktop) | ✅ | Câmera frontal e traseira |
| Safari (iOS) | ✅ | Requer HTTPS. `facingMode` suportado a partir do iOS 11 |
| Firefox | ✅ | `facingMode` suportado |
| Edge | ✅ | Baseado em Chromium |
| IE 11 | ❌ | `getUserMedia` não suportado — fallback para input file |

### Fallback para browsers sem suporte
- Quando `navigator.mediaDevices` não existe → exibe `<input type="file" accept="image/*" capture="environment">` como fallback. Permite captura via app nativa de câmera do dispositivo

---

## Variantes no Figma

| Variante | Properties | Descrição |
|---|---|---|
| **Default** | `State=Default` | Botão trigger com ícone de câmera + label |
| **Camera Active** | `State=CameraActive` | Viewfinder com feed (placeholder cinza) + controles capturar/trocar |
| **Preview** | `State=Preview` | Imagem estática capturada + controles confirmar/descartar |
| **Permission Denied** | `State=PermissionDenied` | Ícone de cadeado + mensagem + botão retry |
| **Device Error** | `State=DeviceError` | Ícone de erro + mensagem + botão retry |

---

## Comportamento responsivo

> Regra 13 — Responsividade obrigatória. Breakpoints: Mobile (<768px), Tablet (768–1023px), Desktop (≥1024px).

| Precisa de variante `Layout=Mobile`? | **Sim** — viewfinder e boxes de erro precisam de largura reduzida |
|---|---|
| **Desktop** | Viewfinder/boxes 480px. Permission/Error box padding 32px |
| **Mobile** | Viewfinder/boxes 343px (375 - 2×16). Permission/Error box padding 16px. Default (botão trigger) sem alteração |
| **Tablet** | Segue Desktop |

**Variantes no Figma:** 10 (5 estados × 2 layouts)

---

## Casos excepcionais / bordas

- **Browser sem `getUserMedia`:** fallback para `<input type="file" accept="image/*" capture>` — abre app nativa de câmera
- **HTTPS obrigatório:** `getUserMedia` requer contexto seguro. Se HTTP, estado Device Error com mensagem "Captura de imagem requer conexão segura (HTTPS)"
- **Dispositivo sem câmera (desktop sem webcam):** estado Device Error com "Nenhuma câmera foi detectada neste dispositivo"
- **Câmera em uso por outro app:** estado Device Error com mensagem específica
- **Permissão negada permanentemente:** no Chrome, `getUserMedia` rejeita com `NotAllowedError`. Mensagem instrui a habilitar nas configurações do browser
- **Troca de câmera em dispositivo com câmera única:** botão "Trocar câmera" não é renderizado
- **Foto muito escura ou borrada:** responsabilidade do usuário (preview permite descartar). Componente não faz análise de qualidade
- **Mobile com orientação landscape/portrait:** viewfinder adapta proporção ao container. `object-fit: cover` para manter proporção do vídeo
- **Stream não parado no `ngOnDestroy`:** risco de câmera ficar ativa em background. Componente DEVE parar todos os tracks no lifecycle hook
- **Múltiplas instâncias na mesma tela:** cada instância gerencia seu próprio stream. Duas câmeras ativas simultaneamente pode falhar — tratar com mensagem "Câmera já está em uso"
- **Imagem HEIC (iOS):** `canvas.toBlob()` gera JPEG com a `quality` configurada, independente do formato de captura
- **Modo noturno / flash:** fora do escopo — depende do browser/hardware. Não é controlável via `getUserMedia` constraints na maioria dos browsers

---

## Tokens utilizados (resumo)

| Token | Uso |
|---|---|
| `--color-primary` | Botão capturar, botão confirmar |
| `--color-danger` | Botão descartar |
| `--color-text-primary` | Mensagens de erro, labels |
| `--color-text-secondary` | Instrução na permissão negada |
| `--color-text-tertiary` | Hint text |
| `--color-border` | Borda do viewfinder/container |
| `--color-border-focus` | Focus ring |
| `--color-surface` | Fundo geral |
| `--color-surface-secondary` | Fundo viewfinder (placeholder quando câmera não ativa) |
| `--color-neutral-900` | Fundo controles da câmera (dark overlay) |
| `--radius-md` | Border-radius viewfinder |
| `--radius-lg` | Border-radius container |
| `--space-3` | Padding controles |
| `--space-4` | Gap controles, gap text → button |
| `--space-8` | Padding mensagem permissão negada |

---

## Relação com SC-15 Uploaders

| Aspecto | SC-07 Image Captures | SC-15 Uploaders |
|---|---|---|
| **Propósito** | Captura nova imagem via câmera | Upload de arquivo já existente no dispositivo |
| **Fonte** | Hardware (câmera do dispositivo) | Sistema de arquivos |
| **Saída** | `Blob` / `dataUrl` (imagem recém-capturada) | `File` (arquivo selecionado) |
| **Preview** | Inline (imagem estática no viewfinder) | Via BC-11 File Preview |
| **Integração** | Pode alimentar SC-15 (foto capturada → upload) | Independente |

> SC-07 e SC-15 são complementares. No fluxo da DV, o policial pode capturar uma foto (SC-07) que depois é enviada ao servidor via uploader (SC-15), ou pode fazer upload direto de um arquivo já salvo (SC-15 sozinho).

---

## O que está fora deste spec

- **Edição de imagem (crop, rotação, filtros):** se necessário, componente separado. Image Capture apenas captura
- **Reconhecimento facial / OCR:** processamento de imagem é responsabilidade da app host ou serviço externo
- **Gravação de vídeo:** componente captura apenas frames estáticos (fotos). Vídeo seria componente separado
- **Flash/lanterna:** não controlável de forma confiável via web APIs na maioria dos browsers
- **Zoom:** controlável em alguns browsers via `MediaTrackConstraints.zoom`, mas suporte inconsistente. Fora do escopo v1
- **Captura de múltiplas fotos em sequência:** cada captura retorna ao Default. Para múltiplas, o layout da tela cria a experiência (ex: lista de SC-07 + SC-15)
- **Armazenamento local (IndexedDB/localStorage):** responsabilidade da app host. Componente entrega `Blob` e `dataUrl`

---

## Critérios de aceite

- [ ] 5 variantes no Figma: Default, CameraActive, Preview, PermissionDenied, DeviceError
- [ ] Botão trigger como instância de BC-05 Button com ícone fa-camera (Regra 11)
- [ ] Todos os botões de ação como instâncias de BC-05 Button (Regra 11)
- [ ] Viewfinder com aspect ratio 4:3 e placeholder cinza
- [ ] Preview com imagem estática e controles confirmar/descartar
- [ ] Mensagem de permissão negada com ícone de cadeado + instrução + botão retry
- [ ] Mensagem de erro de dispositivo com ícone de erro + mensagem descritiva + botão retry
- [ ] Labels em português ("Abrir câmera", não "Open Camera") — resolve vis aten
- [ ] Feedback visual por estado (câmera ativa, foto capturada, erro) — resolve H-1 aten
- [ ] Controles de descarte e cancelamento em todos os estados — resolve H-3 aten
- [ ] Convenção Angular documentada (atual + recomendada) — resolve H-4 aten
- [ ] Preview obrigatório por padrão para confirmar antes de enviar — resolve H-5 aten
- [ ] Ícones + labels em todos os controles — resolve H-6 aten
- [ ] Mensagens de erro específicas por tipo de falha — resolve H-9 aten
- [ ] Contraste verificado — mínimo 4.5:1 para texto
- [ ] ARIA documentado: `role="img"` no viewfinder, `role="alert"` em mensagens de erro, `aria-live` para mudanças de estado
- [ ] Fallback para browsers sem `getUserMedia` documentado
- [ ] Relação com SC-15 Uploaders documentada
- [ ] Violação WCAG (vis aten) resolvida
- [ ] Violações Nielsen (6× aten) resolvidas
- [ ] Revisado e aprovado por Giuliana
