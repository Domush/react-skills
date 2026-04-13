# TypeScript 6.0 Release Notes Summary

TypeScript 6.0 is a **transition release** preparing for TypeScript 7.0 (the native Rust port). It reflects modern JavaScript ecosystem realities and improves build performance.

## Major New Features

### 1. Less Context-Sensitivity on `this`-less Functions

- Functions without `this` usage are no longer treated as contextually sensitive
- Better type inference for generic function parameters
- Enables more reliable type argument inference with method syntax

### 2. Subpath Imports Starting with `#/`

- Node.js now supports `#/` prefix in subpath imports
- Simpler import aliases without extra segments
- Supported in `moduleResolution: "nodenext"` and `moduleResolution: "bundler"`

```json
{
  "imports": {
    "#/*": "./dist/*"
  }
}
```

### 3. Combining `--moduleResolution bundler` with `--module commonjs`

- New valid combination for migration paths
- Helps projects move toward modern module systems

### 4. The `--stableTypeOrdering` Flag

- Helps with 6.0-to-7.0 migration
- Matches TypeScript 7.0's deterministic type ordering
- Up to 25% slower; use only for migration diagnosis

### 5. `es2025` Option for `target` and `lib`

- New target option for es2025 JavaScript features
- Includes: `RegExp.escape`, `Promise.try`, `Iterator` methods, `Set` methods

### 6. New Types for Temporal API (Stage 4)

- Built-in TypeScript types for Temporal
- Use via `--target esnext` or `"lib": ["esnext", "esnext.temporal"]`

### 7. New "Upsert" Methods for Map/WeakMap

- `getOrInsert(key, defaultValue)` - Get or set a default
- `getOrInsertComputed(key, () => value)` - Lazy computation
- Simplifies common Map patterns

### 8. `RegExp.escape` (ECMAScript Stage 4)

- Escapes special regex characters automatically
- All runtimes supporting es2025 can use this now

### 9. DOM Lib Consolidation

- `lib.dom` now includes `dom.iterable` and `dom.asynciterable` automatically
- Simplifies configuration for modern web development
- No longer need separate entries in `"lib"` array

## Breaking Changes (CRITICAL)

### 1. `strict` is now `true` by default

**What breaks**: All implicit `any` types error
**Fix**: Set `"strict": false` temporarily, then fix errors

### 2. `types` now defaults to `[]`

**What breaks**: Cannot find `node`, `jest`, or other `@types` packages automatically
**Fix**: Explicitly list required types:

```json
{
  "compilerOptions": {
    "types": ["node", "jest"]
  }
}
```

**Benefit**: 20-50% faster builds

### 3. `rootDir` now defaults to `.`

**What breaks**: Output structure changes (src/ appears in dist/)
**Fix**: Explicitly set `"rootDir": "./src"`

### 4. `target: es5` is DEPRECATED

**What breaks**: ES5 target no longer supported
**Use Instead**: `es2015` minimum (or `es2020`, `es2025`)
**If needed**: Use external transpilers (Babel, swc, esbuild)

### 5. Module Resolution Changes

**Deprecated/Removed**:

- `--moduleResolution node` (aka `node10`)
- `--moduleResolution classic`
- `--module amd`, `umd`, `systemjs`, `none`

**Use Instead**:

- `moduleResolution: "bundler"` for web/bundled apps
- `moduleResolution: "nodenext"` for Node.js

### 6. `baseUrl` is DEPRECATED

**What breaks**: No longer serves as module lookup root
**Fix**: Remove or keep only for `paths` mappings:

```json
{
  "compilerOptions": {
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

### 7. `--downlevelIteration` DEPRECATED

**Reason**: Only affects ES5 (which is deprecated)
**Fix**: Remove from tsconfig.json

### 8. `--outFile` DEPRECATED

**Reason**: Bundlers do this better and faster
**Fix**: Use Webpack, esbuild, Vite, Rollup instead

### 9. Legacy `module` Keyword for Namespaces DEPRECATED

```typescript
// ❌ Deprecated
module Foo {}

// ✅ Use namespace
namespace Foo {}
```

### 10. `asserts` Keyword Replaced by `with`

```typescript
// ❌ Old
import json from './data.json' assert { type: 'json' };

// ✅ New
import json from './data.json' with { type: 'json' };
```

### 11. `esModuleInterop: false` NO LONGER VALID

- CommonJS/ESM interop always enabled
- Update imports: `import express from 'express'` (not `import * as express`)

### 12. `--alwaysStrict: false` NO LONGER VALID

- Strict mode always enforced
- Set to `true` or omit

### 13. `no-default-lib` Directives NO LONGER SUPPORTED

- Use `--noLib` or `--libReplacement` instead

### 14. Command-Line Files with tsconfig.json Files Now Error

```bash
# ❌ Error if tsconfig.json exists
tsc foo.ts

# ✅ Use --ignoreConfig flag
tsc --ignoreConfig foo.ts
```

## Deprecations (Can Suppress with `ignoreDeprecations: "6.0"`)

All of the above breaking changes can be suppressed temporarily:

```json
{
  "compilerOptions": {
    "ignoreDeprecations": "6.0"
  }
}
```

**Important**: TypeScript 7.0 will NOT support these settings. Migrate now.

## Migration Strategy

1. **Upgrade TypeScript**

   ```bash
   npm install typescript@latest
   ```

2. **Run build to identify errors**

   ```bash
   npm run build
   ```

3. **Address common issues**:
   - Add `types: ["node"]` if Node.js errors
   - Set `rootDir: "./src"` if output structure wrong
   - Fix strict type errors (or temporarily set `strict: false`)

4. **Update module resolution**

   ```json
   {
     "compilerOptions": {
       "moduleResolution": "bundler"
     }
   }
   ```

5. **Remove deprecated options**
   - Remove `baseUrl` (keep only if with `paths`)
   - Remove `downlevelIteration`
   - Remove `outFile`
   - Update `--module` to `esnext`

6. **Test thoroughly**

   ```bash
   npm run build && npm run test
   ```

## Recommended Modern tsconfig.json

```json
{
  "compilerOptions": {
    "target": "es2025",
    "module": "esnext",
    "moduleResolution": "bundler",
    "lib": ["es2025"],
    "strict": true,
    "exactOptionalPropertyTypes": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitReturns": true,
    "noImplicitOverride": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,
    "declaration": true,
    "sourceMap": true,
    "outDir": "./dist",
    "rootDir": "./src",
    "types": ["node", "jest"]
  },
  "include": ["src"],
  "exclude": ["node_modules", "dist"]
}
```

## Preparing for TypeScript 7.0

- TypeScript 7.0 is the upcoming **native compiler** (written in Rust)
- Removes ALL deprecated options from 6.0
- Expected 2-3x performance improvements
- Full API compatibility maintained
- Expect minor type ordering differences

## References

- Full TypeScript 6.0 changelog: <https://www.typescriptlang.org/docs/handbook/release-notes/typescript-6-0.html>
- Official TypeScript Blog: <https://devblogs.microsoft.com/typescript/>
