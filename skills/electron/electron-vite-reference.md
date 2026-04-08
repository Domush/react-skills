# electron-vite 5+ Reference Guide

Detailed reference for electron-vite 5+ development. Covers configuration, project structure, migration from v4, new features, and common patterns.

---

## Project Structure

### Standard Layout
```
project-root/
├── app/                          # or src/ — application source
│   ├── main/
│   │   ├── index.ts              # Main process entry point
│   │   └── services/             # Node.js services (DB, filesystem, etc.)
│   ├── preload/
│   │   └── index.ts              # Preload script entry point
│   └── renderer/
│       ├── index.html            # HTML entry point
│       └── src/
│           ├── App.tsx           # React root component
│           ├── main.tsx          # React entry point
│           ├── components/       # UI components
│           ├── hooks/            # Custom React hooks
│           └── assets/           # Static assets
├── resources/                    # Public/static resources (copied as-is)
├── out/                         # Build output (auto-generated)
│   ├── main/
│   ├── preload/
│   └── renderer/
├── electron.vite.config.ts       # electron-vite configuration
├── electron-builder.yml          # electron-builder configuration
├── tsconfig.json
└── package.json
```

### Custom Source Layout (e.g., `app/` instead of `src/`)
When using a custom source directory, configure the entry points explicitly:

```ts
import { defineConfig } from 'electron-vite'
import { resolve } from 'path'

export default defineConfig({
  main: {
    build: {
      lib: {
        entry: resolve('app/main/index.ts')
      }
    }
  },
  preload: {
    build: {
      lib: {
        entry: resolve('app/preload/index.ts')
      }
    }
  },
  renderer: {
    root: resolve('app/renderer'),
    build: {
      rollupOptions: {
        input: {
          index: resolve('app/renderer/index.html')
        }
      }
    }
  }
})
```

---

## Configuration (v5+)

### Basic Configuration

```ts
// electron.vite.config.ts
import { defineConfig } from 'electron-vite'

export default defineConfig({
  main: {
    // Vite config for main process
  },
  preload: {
    // Vite config for preload scripts
  },
  renderer: {
    // Vite config for renderer process
  }
})
```

### Complete Configuration Example (v5)

```ts
import { defineConfig } from 'electron-vite'
import react from '@vitejs/plugin-react'
import tailwindcss from '@tailwindcss/vite'
import { resolve } from 'path'

export default defineConfig({
  main: {
    // No externalizeDepsPlugin needed in v5 — enabled by default
    resolve: {
      alias: {
        '@': resolve('app'),
        '@/main': resolve('app/main')
      }
    },
    build: {
      lib: {
        entry: resolve('app/main/index.ts'),
        formats: ['cjs']           // CJS for main process if needed
      },
      rollupOptions: {
        external: ['better-sqlite3', 'node-powershell']
      }
    }
  },
  preload: {
    // No externalizeDepsPlugin needed in v5
    resolve: {
      alias: {
        '@': resolve('app')
      }
    },
    build: {
      lib: {
        entry: resolve('app/preload/index.ts'),
        formats: ['cjs']
      },
      rollupOptions: {
        external: ['electron']
      }
    }
  },
  renderer: {
    plugins: [react(), tailwindcss()],
    resolve: {
      alias: {
        '@': resolve('app/renderer/src')
      }
    },
    root: resolve('app/renderer'),
    build: {
      rollupOptions: {
        input: {
          index: resolve('app/renderer/index.html')
        }
      }
    }
  }
})
```

### Conditional Configuration (v5)

In v5, function-based configs for nested fields (`main`, `preload`, `renderer`) are **no longer supported**. Use the top-level function instead:

```ts
import { defineConfig } from 'electron-vite'

// ✅ v5: Top-level function for conditional config
export default defineConfig(({ command, mode }) => {
  const isDev = command === 'serve'

  return {
    main: {
      build: {
        minify: !isDev
      }
    },
    preload: {
      build: {
        minify: !isDev
      }
    },
    renderer: {
      build: {
        minify: !isDev
      }
    }
  }
})
```

---

## Migration from v4 to v5

### Step 1: Update Dependencies

```bash
npm install -D electron-vite@^5.0.0
```

### Step 2: Remove Deprecated Plugin Imports

```ts
// ❌ v4 — Remove these imports
import { externalizeDepsPlugin, bytecodePlugin } from 'electron-vite'

// ✅ v5 — Only import defineConfig (and optionally loadEnv)
import { defineConfig } from 'electron-vite'
```

### Step 3: Remove Plugin Usage

```ts
// ❌ v4
main: {
  plugins: [externalizeDepsPlugin()]
}

// ✅ v5 — No plugin needed; externalizeDeps is enabled by default
main: {
  // If you need to customize:
  // build: { externalizeDeps: { /* options */ } }
}
```

### Step 4: Migrate Bytecode Plugin

```ts
// ❌ v4
main: {
  plugins: [bytecodePlugin({ protectedStrings: ['secret'] })]
}

// ✅ v5
main: {
  build: {
    bytecode: {
      protectedStrings: ['secret']
    }
  }
}
```

### Step 5: Fix Function-Based Nested Configs

```ts
// ❌ v4 — Function configs on nested fields
renderer: defineViteConfig(({ command, mode }) => ({
  // ...
}))

// ✅ v5 — Move function to top level
export default defineConfig(({ command, mode }) => ({
  main: { /* ... */ },
  preload: { /* ... */ },
  renderer: { /* ... */ }
}))
```

---

## New Features in v5

### Isolated Build (`build.isolatedEntries`)

For multi-entry apps (e.g., multiple preload scripts):

```ts
main: {
  build: {
    isolatedEntries: true,   // Automatically isolate entries
    lib: {
      entry: {
        index: resolve('app/main/index.ts'),
        worker: resolve('app/main/worker.ts')
      }
    }
  }
}
```

### Enhanced Bytecode Protection

```ts
main: {
  build: {
    bytecode: {
      protectedStrings: ['API_KEY', 'SECRET_TOKEN']
      // New: Babel-based string protection — compiles strings
      //      into unique IIFE functions for better obfuscation
    }
  }
}
```

---

## Environment Variables

### Prefixes
| Process | Prefix |
|---------|--------|
| Main | `MAIN_VITE_` |
| Preload | `PRELOAD_VITE_` |
| Renderer | `RENDERER_VITE_` |

### Usage in Config
```ts
import { defineConfig, loadEnv } from 'electron-vite'

export default defineConfig(({ command, mode }) => {
  const env = loadEnv(mode)
  return {
    main: {
      define: {
        'import.meta.env.APP_VERSION': JSON.stringify(process.env.npm_package_version)
      }
    }
  }
})
```

### Usage in Code
```ts
// Main process (.env file: MAIN_VITE_API_URL=https://api.example.com)
const apiUrl = import.meta.env.MAIN_VITE_API_URL

// Renderer process (.env file: RENDERER_VITE_APP_NAME=MyApp)
const appName = import.meta.env.RENDERER_VITE_APP_NAME
```

---

## CLI Commands

```bash
# Development (with HMR for renderer, hot-reload for main/preload)
electron-vite dev

# Build for production
electron-vite build

# Preview production build
electron-vite preview
```

### package.json Scripts
```json
{
  "scripts": {
    "dev": "electron-vite dev",
    "build": "electron-vite build",
    "preview": "electron-vite preview",
    "dist": "electron-vite build && electron-builder --win",
    "lint": "npx tsc --noEmit"
  }
}
```

---

## Common Patterns

### Path Aliases with TypeScript

```ts
// electron.vite.config.ts
resolve: {
  alias: {
    '@': resolve('app/renderer/src'),
    '@shared': resolve('app/shared'),
    '@main': resolve('app/main')
  }
}
```

```json
// tsconfig.json
{
  "compilerOptions": {
    "paths": {
      "@/*": ["./app/renderer/src/*"],
      "@shared/*": ["./app/shared/*"],
      "@main/*": ["./app/main/*"]
    }
  }
}
```

### HMR and Hot Reloading
- **Renderer**: Full HMR via Vite dev server (React Fast Refresh works)
- **Main process**: Automatically restarts when files change
- **Preload scripts**: Automatically reloads renderer when changed

### Asset Handling
```ts
// In renderer — works with Vite's asset pipeline
import logo from '@/assets/logo.png'

// In main process — use resources directory
import { resolve } from 'path'
const iconPath = resolve(__dirname, '../../resources/icon.png')
```

---

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Still importing `externalizeDepsPlugin` in v5 | Remove import — enabled by default |
| Still importing `bytecodePlugin` in v5 | Use `build.bytecode` option instead |
| Using `defineViteConfig` for nested fields | Use top-level function in `defineConfig` |
| Forgetting to set `root` for non-standard renderer path | Set `renderer.root` explicitly |
| Using wrong env prefix | Use `MAIN_VITE_`, `PRELOAD_VITE_`, `RENDERER_VITE_` |
| Importing Node.js modules in renderer | Use IPC + preload to access Node.js |
