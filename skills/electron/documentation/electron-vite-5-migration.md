# electron-vite 5 — Migration from v4

> Source: <https://electron-vite.org/guide/migration>
> Fetched: 2026-04-04

## General Changes

### Deprecation of `externalizeDepsPlugin`

The `externalizeDepsPlugin` is deprecated. In v5, use `build.externalizeDeps` to customize. Enabled by default — no need to import the plugin.

```ts
// ❌ v4 (deprecated)
import { defineConfig, externalizeDepsPlugin } from 'electron-vite'
export default defineConfig({
  main: {
    plugins: [externalizeDepsPlugin()]
  },
  preload: {
    plugins: [externalizeDepsPlugin()]
  }
})

// ✅ v5 (no plugin needed — enabled by default)
import { defineConfig } from 'electron-vite'
export default defineConfig({
  main: {
    // externalizeDeps is enabled by default
    // customize via build.externalizeDeps if needed
  },
  preload: {
    // same
  }
})
```

### Deprecation of `bytecodePlugin`

The `bytecodePlugin` is deprecated. In v5, use `build.bytecode` instead.

```ts
// ❌ v4 (deprecated)
import { defineConfig, bytecodePlugin } from 'electron-vite'
export default defineConfig({
  main: {
    plugins: [bytecodePlugin()]
  },
  preload: {
    plugins: [bytecodePlugin()]
  }
})

// ✅ v5
import { defineConfig } from 'electron-vite'
export default defineConfig({
  main: {
    build: {
      bytecode: true
    }
  },
  preload: {
    build: {
      bytecode: true
    }
  }
})
```

With custom options:

```ts
// ❌ v4 (deprecated)
plugins: [bytecodePlugin({ protectedStrings: ['foo'] })]

// ✅ v5
build: {
  bytecode: {
    protectedStrings: ['foo']
  }
}
```

## Configuration Changes

### Remove function resolution for nested config fields

Function-based configuration is **no longer supported** for nested config fields (`main`, `preload`, `renderer`). Use static configuration objects.

```ts
// ❌ v4 (no longer supported in v5)
import { defineConfig, defineViteConfig } from 'electron-vite'
export default defineConfig({
  main: { /* ... */ },
  preload: { /* ... */ },
  renderer: defineViteConfig(({ command, mode }) => {
    // ...
  })
})

// ✅ v5 — use top-level function for conditional config
import { defineConfig } from 'electron-vite'
export default defineConfig(({ command, mode }) => {
  if (command === 'serve') {
    return {
      main: { /* dev config */ },
      preload: { /* dev config */ },
      renderer: { /* dev config */ }
    }
  } else {
    return {
      main: { /* build config */ },
      preload: { /* build config */ },
      renderer: { /* build config */ }
    }
  }
})
```

## New Features in v5

### Isolated Build

A new `build.isolatedEntries` option for multi-entry development — automatically isolates entries, handles shared chunks/assets, and reduces unnecessary chunk generation.

### Enhanced Bytecode Protection

New Babel-based string protection plugin for bytecode compilation — compiles strings into unique IIFE functions for better obfuscation.

---

# electron-vite Configuration Reference

> Source: <https://electron-vite.org/config/>

## Config File

electron-vite resolves `electron.vite.config.js` in project root. Supported extensions: `.ts`, `.mjs`, `.cjs`, `.mts`, `.cts`.

## Config Intellisense

```ts
import { defineConfig } from 'electron-vite'
export default defineConfig({
  main: { /* ... */ },
  preload: { /* ... */ },
  renderer: { /* ... */ }
})
```

## Conditional Config

```ts
import { defineConfig } from 'electron-vite'
export default defineConfig(({ command, mode }) => {
  if (command === 'serve') {
    return { /* dev config */ }
  } else {
    return { /* build config */ }
  }
})
```

## Environment Variables

```ts
import { defineConfig, loadEnv } from 'electron-vite'
export default defineConfig(({ command, mode }) => {
  const env = loadEnv(mode)
  return { /* config */ }
})
```

Prefixes: `MAIN_VITE_`, `PRELOAD_VITE_`, `RENDERER_VITE_`

## Built-in Config Defaults

### Main process

| Option | Default |
| -------- | --------- |
| `build.target` | `node*` (auto-detected) |
| `build.outDir` | `out/main` |
| `build.lib.entry` | `src/main/{index\|main}.{js\|ts\|mjs\|cjs}` |
| `build.lib.formats` | `es` (ESM) |
| `build.rollupOptions.external` | `electron`, node built-ins |
| `build.minify` | `false` |
| `publicDir` | `resources` |
| `envPrefix` | `MAIN_VITE_` |

### Preload

| Option | Default |
| -------- | --------- |
| `build.target` | `node*` (auto-detected) |
| `build.outDir` | `out/preload` |
| `build.lib.entry` | `src/preload/{index\|preload}.{js\|ts\|mjs\|cjs}` |
| `build.lib.formats` | `es` (ESM) |
| `build.rollupOptions.external` | `electron`, node built-ins |
| `build.minify` | `false` |
| `publicDir` | `resources` |
| `envPrefix` | `PRELOAD_VITE_` |

### Renderer

| Option | Default |
| -------- | --------- |
| `root` | `src/renderer` |
| `build.target` | `chrome*` (auto-detected) |
| `build.outDir` | `out/renderer` |
| `build.rollupOptions.input` | `/src/renderer/index.html` |
| `build.minify` | `false` |
| `envPrefix` | `RENDERER_VITE_` |
