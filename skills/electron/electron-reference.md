# Electron 41+ Reference Guide

Detailed reference for Electron 41+ development. Covers the multi-process architecture, IPC patterns, security model, breaking changes, and best practices.

---

## Process Architecture

Electron uses Chromium's multi-process architecture:

### Main Process

- **Single instance** — entry point of the app
- Runs in a **full Node.js environment**
- Creates and manages `BrowserWindow` instances
- Handles native OS integrations (menus, dialogs, tray, system notifications)
- Manages application lifecycle (`app.on('ready')`, `app.on('window-all-closed')`)

### Renderer Process

- **One per window** — each `BrowserWindow` gets its own renderer
- Runs web content (HTML, CSS, JavaScript)
- **No direct Node.js access** by default (security)
- Communicates with main process via IPC

### Preload Scripts

- Run **before** renderer content loads
- Have access to a **limited Node.js API** (via contextBridge)
- Bridge between renderer (untrusted) and main (privileged) processes
- Must expose APIs via `contextBridge.exposeInMainWorld`

### Utility Process

- Spawned via `utilityProcess.fork()` from main process
- Runs in a **sandboxed Node.js environment**
- Good for CPU-intensive or crash-prone operations
- Has its own message port for IPC

---

## IPC Patterns (Modern / Recommended)

### Pattern 1: Renderer → Main (invoke/handle)

**Main process:**

```ts
import { ipcMain, BrowserWindow } from 'electron'

ipcMain.handle('get-system-info', async (_event) => {
  return {
    platform: process.platform,
    arch: process.arch,
    version: process.versions.electron
  }
})
```

**Preload script:**

```ts
import { contextBridge, ipcRenderer } from 'electron'

contextBridge.exposeInMainWorld('api', {
  getSystemInfo: () => ipcRenderer.invoke('get-system-info')
})
```

**Renderer:**

```ts
const info = await window.api.getSystemInfo()
```

### Pattern 2: Main → Renderer (webContents.send)

**Main process:**

```ts
function sendStatusUpdate(win: BrowserWindow, status: string) {
  win.webContents.send('status-update', status)
}
```

**Preload script:**

```ts
contextBridge.exposeInMainWorld('api', {
  onStatusUpdate: (callback: (status: string) => void) => {
    const handler = (_event: IpcRendererEvent, status: string) => callback(status)
    ipcRenderer.on('status-update', handler)
    // Return cleanup function
    return () => ipcRenderer.removeListener('status-update', handler)
  }
})
```

**Renderer (React):**

```tsx
useEffect(() => {
  const cleanup = window.api.onStatusUpdate((status) => {
    setStatus(status)
  })
  return cleanup
}, [])
```

### Pattern 3: Two-way IPC with MessagePorts

```ts
// Main process
const { port1, port2 } = new MessageChannelMain()
win.webContents.postMessage('port', null, [port2])
port1.on('message', (event) => {
  console.log('Received:', event.data)
  port1.postMessage('response')
})
port1.start()
```

---

## Security Best Practices (Electron 41+)

### Context Isolation (Default: enabled)

```ts
const win = new BrowserWindow({
  webPreferences: {
    contextIsolation: true,     // default, keep enabled
    nodeIntegration: false,     // default, keep disabled
    sandbox: true,              // default, keep enabled
    preload: path.join(__dirname, '../preload/index.js')
  }
})
```

### Content Security Policy

```html
<meta http-equiv="Content-Security-Policy"
  content="default-src 'self'; script-src 'self'; style-src 'self' 'unsafe-inline'">
```

### Path Validation

```ts
// ❌ BAD: Direct path concatenation
const filePath = userInput + '.json'

// ✅ GOOD: Validate and sanitize
import path from 'path'
const safePath = path.resolve(ALLOWED_DIR, path.basename(userInput) + '.json')
if (!safePath.startsWith(ALLOWED_DIR)) {
  throw new Error('Path traversal detected')
}
```

### IPC Validation

```ts
// ❌ BAD: Trust renderer input blindly
ipcMain.handle('execute-command', async (_event, cmd: string) => {
  return exec(cmd) // DANGEROUS!
})

// ✅ GOOD: Validate and whitelist
const ALLOWED_COMMANDS = ['scan', 'status', 'info'] as const
type AllowedCommand = typeof ALLOWED_COMMANDS[number]

ipcMain.handle('execute-command', async (_event, cmd: string) => {
  if (!ALLOWED_COMMANDS.includes(cmd as AllowedCommand)) {
    throw new Error(`Command not allowed: ${cmd}`)
  }
  return executeWhitelistedCommand(cmd as AllowedCommand)
})
```

---

## Breaking Changes Quick Reference (v36–v41)

| Version | Change | Action Required |
| --------- | -------- | ---------------- |
| v41 | PDFs use OOPIF, not guest WebContents | Use frame tree for PDF detection |
| v41 | Cookie change causes updated | Update cookie change handlers |
| v40 | `clipboard` deprecated in renderer | Move to preload + contextBridge |
| v39 | `--host-rules` deprecated | Use `--host-resolver-rules` |
| v39 | `window.open` always resizable | Use `setWindowOpenHandler` |
| v38 | macOS 11 support removed | Require macOS 12+ |
| v38 | `webFrame.routingId` deprecated | Use `webFrame.frameToken` |
| v37 | Utility process unhandled rejections warn | Add explicit handler if crash needed |
| v37 | `process.exit()` is synchronous in utility | Account for sync behavior |
| v36 | `app.commandLine` lowercases switches | Use `process.argv` instead |
| v36 | `NativeImage.getBitmap()` deprecated | Use `NativeImage.toBitmap()` |
| v36 | Extension methods moved to `session.extensions` | Update method calls |

---

## Deprecated APIs to Avoid

```ts
// ❌ DEPRECATED: clipboard in renderer
// In renderer process:
const text = electron.clipboard.readText()

// ✅ MODERN: Use preload + contextBridge
// preload.ts:
contextBridge.exposeInMainWorld('clipboard', {
  readText: () => clipboard.readText(),
  writeText: (text: string) => clipboard.writeText(text)
})
// renderer:
const text = window.clipboard.readText()

// ❌ DEPRECATED: webFrame.routingId
const id = webFrame.routingId

// ✅ MODERN: webFrame.frameToken
const token = webFrame.frameToken

// ❌ DEPRECATED: NativeImage.getBitmap()
const bitmap = image.getBitmap()

// ✅ MODERN: NativeImage.toBitmap()
const bitmap = image.toBitmap()

// ❌ REMOVED: systemPreferences.isAeroGlassEnabled()
// No replacement needed — always true on Win10+

// ❌ DEPRECATED: session.loadExtension()
await session.defaultSession.loadExtension('/path/to/ext')

// ✅ MODERN: session.extensions
await session.defaultSession.extensions.loadExtension('/path/to/ext')
```

---

## Performance Optimization

### Profile-Guided Optimization (v41.1.1+)

V8 builtins (`Array`, `String`, `RegExp`) are optimized with PGO for faster execution.

### Renderer Performance

```ts
// Use virtual scrolling for large lists
// Debounce high-frequency updates
// Keep renderer process responsive — move heavy work to main

// Example: Debounced IPC call
let debounceTimer: NodeJS.Timeout
function debouncedUpdate(data: unknown) {
  clearTimeout(debounceTimer)
  debounceTimer = setTimeout(() => {
    win.webContents.send('data-update', data)
  }, 100)
}
```

### Background Processing

```ts
// Use utility processes for CPU-intensive work
import { utilityProcess } from 'electron'

const child = utilityProcess.fork(path.join(__dirname, 'worker.js'))
child.postMessage({ type: 'heavy-computation', data: largeDataset })
child.on('message', (result) => {
  // Handle result in main process
})
```

---

## App Lifecycle Pattern

```ts
import { app, BrowserWindow } from 'electron'

app.whenReady().then(() => {
  createWindow()

  // macOS: re-create window when dock icon clicked and no windows
  app.on('activate', () => {
    if (BrowserWindow.getAllWindows().length === 0) {
      createWindow()
    }
  })
})

// Quit when all windows closed (except macOS)
app.on('window-all-closed', () => {
  if (process.platform !== 'darwin') {
    app.quit()
  }
})

// Handle second-instance for single-instance lock
const gotTheLock = app.requestSingleInstanceLock()
if (!gotTheLock) {
  app.quit()
} else {
  app.on('second-instance', (_event, _argv, _workingDir) => {
    const win = BrowserWindow.getAllWindows()[0]
    if (win) {
      if (win.isMinimized()) win.restore()
      win.focus()
    }
  })
}
```
