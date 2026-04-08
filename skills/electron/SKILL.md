---
name: electron-development
description: Use when configuring, building, or troubleshooting Electron 41+, electron-vite 5+, or electron-builder 26+ projects — covers breaking changes, deprecated patterns, IPC security, build configuration, and proper project structure
---

# Electron Development (Electron 41+ / electron-vite 5+ / electron-builder 26+)

Reference skill for developing modern Electron applications with electron-vite and electron-builder. Covers breaking changes, new APIs, deprecated patterns, security best practices, and build configuration.

**Sub-files for detailed reference:**

- `electron-reference.md` — Electron 41+ architecture, IPC, security, breaking changes
- `electron-vite-reference.md` — electron-vite 5+ configuration, migration, project structure
- `electron-builder-reference.md` — electron-builder 26+ build targets, signing, auto-updates
- `documentation/` — Full fetched documentation from official sources for searching

---

## Electron 41+ Key Changes (Summary)

### Breaking Changes (v36–v41)

| Version | Change | Impact |
| --------- | -------- | -------- |
| v41 | PDFs use OOPIF, not guest WebContents | Use frame tree for PDF detection |
| v40 | `clipboard` deprecated in renderer | Move to preload + contextBridge |
| v39 | `window.open` always resizable | Use `setWindowOpenHandler` to override |
| v38 | macOS 11 removed, `webFrame.routingId` deprecated | Use `webFrame.frameToken` |
| v37 | Utility process unhandled rejection warns | Add explicit handler if crash needed |
| v36 | `app.commandLine` lowercases all switches | Use `process.argv` instead |
| v36 | Extension methods moved to `session.extensions` | Update all extension API calls |

### Process Model

| Process | Purpose | Has Node.js? |
| --------- | --------- | ------------- |
| Main | App lifecycle, OS integration, window management | ✅ Full |
| Renderer | Web content (HTML/CSS/JS per window) | ❌ No (by default) |
| Preload | Bridge between main and renderer | ⚠️ Limited (contextBridge) |
| Utility | CPU-heavy or crash-prone work | ✅ Sandboxed |

### Security Defaults (Keep These!)

```ts
new BrowserWindow({
  webPreferences: {
    contextIsolation: true,     // ✅ Default — never disable
    nodeIntegration: false,     // ✅ Default — never enable
    sandbox: true,              // ✅ Default — never disable
    preload: join(__dirname, '../preload/index.js')
  }
})
```

### Modern IPC Pattern

```ts
// Main: ipcMain.handle (async request-response)
ipcMain.handle('get-data', async (_event, id: string) => {
  return await db.getData(id)
})

// Preload: contextBridge
contextBridge.exposeInMainWorld('api', {
  getData: (id: string) => ipcRenderer.invoke('get-data', id),
  onUpdate: (cb: (data: Data) => void) => {
    const handler = (_e: IpcRendererEvent, data: Data) => cb(data)
    ipcRenderer.on('data-update', handler)
    return () => ipcRenderer.removeListener('data-update', handler)
  }
})

// Renderer
const data = await window.api.getData('123')
```

---

## electron-vite 5+ Key Changes (Summary)

### Deprecated in v5

| Deprecated | Replacement |
| ----------- | ------------- |
| `externalizeDepsPlugin()` | Removed — enabled by default |
| `bytecodePlugin()` | `build.bytecode: true` (or object with options) |
| Function-based nested config fields | Top-level function in `defineConfig` |
| `defineViteConfig` for nested fields | Use `defineConfig(({ command, mode }) => ...)` |

### v5 Config Pattern

```ts
// ✅ electron-vite 5+ correct configuration
import { defineConfig } from 'electron-vite'
import react from '@vitejs/plugin-react'
import tailwindcss from '@tailwindcss/vite'
import { resolve } from 'path'

export default defineConfig({
  main: {
    // No externalizeDepsPlugin needed — enabled by default
    resolve: {
      alias: { '@': resolve('app') }
    },
    build: {
      lib: {
        entry: resolve('app/main/index.ts'),
        formats: ['cjs']
      },
      rollupOptions: {
        external: ['better-sqlite3', 'node-powershell']
      }
    }
  },
  preload: {
    // No externalizeDepsPlugin needed
    build: {
      lib: {
        entry: resolve('app/preload/index.ts'),
        formats: ['cjs']
      }
    }
  },
  renderer: {
    plugins: [react(), tailwindcss()],
    root: resolve('app/renderer'),
    build: {
      rollupOptions: {
        input: { index: resolve('app/renderer/index.html') }
      }
    }
  }
})
```

### Environment Variable Prefixes

| Process | Prefix |
| --------- | -------- |
| Main | `MAIN_VITE_` |
| Preload | `PRELOAD_VITE_` |
| Renderer | `RENDERER_VITE_` |

### New: Isolated Build

```ts
main: {
  build: {
    isolatedEntries: true    // For multi-entry apps
  }
}
```

### New: Enhanced Bytecode Protection

```ts
main: {
  build: {
    bytecode: {
      protectedStrings: ['API_KEY', 'SECRET']
      // Babel-based IIFE obfuscation
    }
  }
}
```

---

## electron-builder 26+ Key Changes (Summary)

### Breaking Changes in v26

| Change | Details |
| -------- | --------- |
| Base image `focal` → `jammy` | Docker/CI builds need Ubuntu 22.04+ |
| `python` → `python3` | Build environments must have python3 |
| TypeScript config support | `electron-builder.ts` now works |
| `electron-rebuild` integration | Native dependency rebuilds updated |
| Legacy `altool` notarization deprecated | Use `notarytool` (automatic) |
| `APPLE_TEAM_ID` env var | Required for Apple ID notarization |
| Corepack support (v26.3) | `packageManager` field respected |
| Win code-sign tooling update (v26.8) | Latest `electron-builder-binaries` |

### Build Config Pattern (YAML)

```yaml
# electron-builder.yml
appId: com.company.appname
productName: My App

directories:
  output: build-output

files:
  - "out/**/*"
  - "package.json"

asar: true
asarUnpack:
  - "**/*.node"
  - "**/node_modules/better-sqlite3/**"

win:
  target:
    - target: nsis
      arch: [x64]
  icon: media/icon.ico
  requestedExecutionLevel: asInvoker

nsis:
  oneClick: true
  createDesktopShortcut: true
  artifactName: "${productName} Setup ${version}.${ext}"

publish:
  - provider: github
    owner: username
    repo: repo-name
```

### Build Scripts

```json
{
  "scripts": {
    "dev": "electron-vite dev",
    "build": "electron-vite build",
    "dist": "electron-vite build && electron-builder --win",
    "postinstall": "electron-builder install-app-deps"
  }
}
```

---

## Project Structure (Recommended)

```ascii
project-root/
├── app/                          # Application source
│   ├── main/
│   │   ├── index.ts              # Main process entry
│   │   ├── services/             # Node.js services
│   │   └── ipc/                  # IPC handlers
│   ├── preload/
│   │   └── index.ts              # Preload entry
│   └── renderer/
│       ├── index.html            # HTML entry
│       └── src/
│           ├── App.tsx
│           ├── main.tsx
│           └── components/
├── resources/                    # Static resources
├── out/                         # Build output (auto)
├── electron.vite.config.ts
├── electron-builder.yml
├── tsconfig.json
└── package.json
```

---

## Common Mistakes

| Mistake | Fix |
| --------- | ----- |
| Enabling `nodeIntegration` in renderer | Use preload + contextBridge instead |
| Importing `externalizeDepsPlugin` in electron-vite 5 | Remove — it's the default now |
| Using `bytecodePlugin()` in electron-vite 5 | Use `build.bytecode` option |
| Using function config for `renderer` field in v5 | Use top-level `defineConfig(fn)` |
| Changing `appId` after release | Never change — breaks updates |
| Missing `asarUnpack` for native `.node` modules | Add to prevent runtime crashes |
| Missing `postinstall` script | Native modules not rebuilt for Electron |
| Using deprecated `altool` for macOS notarization | Remove — `notarytool` is automatic |
| Not validating IPC inputs | Always whitelist/validate in `handle` |
| Using `app.commandLine` for main process flags | Use `process.argv` (v36+ lowercases switches) |
| Using `webFrame.routingId` | Use `webFrame.frameToken` (v38+) |
| Accessing `clipboard` directly in renderer | Use preload bridge (deprecated in v40) |

---

## Deprecated → Modern Quick Reference

| Deprecated | Modern Replacement |
| ----------- | ------------------- |
| `externalizeDepsPlugin()` | Default behavior in electron-vite 5 |
| `bytecodePlugin()` | `build.bytecode: true` |
| `defineViteConfig()` on nested fields | Top-level `defineConfig(fn)` |
| `NativeImage.getBitmap()` | `NativeImage.toBitmap()` |
| `webFrame.routingId` | `webFrame.frameToken` |
| `webFrame.findFrameByRoutingId()` | `webFrame.findFrameByToken()` |
| `session.loadExtension()` | `session.extensions.loadExtension()` |
| `clipboard` in renderer | Preload + contextBridge |
| `--host-rules` CLI switch | `--host-resolver-rules` |
| `altool` notarization | `notarytool` (automatic) |
| `ProtocolResponse.session = null` | `session.fromPartition(id)` |

---

## Quick Reference Links

- [Electron Breaking Changes](https://www.electronjs.org/docs/latest/breaking-changes)
- [Electron Process Model](https://www.electronjs.org/docs/latest/tutorial/process-model)
- [Electron IPC Guide](https://www.electronjs.org/docs/latest/tutorial/ipc)
- [electron-vite Config](https://electron-vite.org/config/)
- [electron-vite v4→v5 Migration](https://electron-vite.org/guide/migration)
- [electron-builder Configuration](https://www.electron.build/configuration)
- [electron-builder NSIS Options](https://www.electron.build/configuration/nsis)
