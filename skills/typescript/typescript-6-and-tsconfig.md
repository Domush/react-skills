# TypeScript 6.0 and tsconfig.json

Comprehensive guide to TypeScript 6.0 breaking changes, migration strategies, and tsconfig.json best practices.

## Table of Contents

- [TypeScript 6.0 Overview](#typescript-60-overview)
- [Breaking Changes](#breaking-changes)
- [Deprecations](#deprecations)
- [Migration Guide](#migration-guide)
- [tsconfig.json Deep Dive](#tsconfigjson-deep-dive)
- [Common Issues and Solutions](#common-issues-and-solutions)

---

## TypeScript 6.0 Overview

TypeScript 6.0 is a **transition release** preparing for TypeScript 7.0 (the native Rust port). It introduces breaking changes that reflect modern JavaScript ecosystems:

- Evergreen runtimes (ES5 is rare)
- ESM standardization
- Bundler-first approach
- Stricter typing by default

**Key Goal**: Improve build performance and align with modern development practices.

---

## Breaking Changes

### 1. `strict` Now Defaults to `true`

**Impact**: TypeScript will enforce stricter type-checking by default.

**What happens**:

- All strict-related flags are implicitly enabled
- Implicit `any` types error
- Null/undefined must be explicitly handled
- Object index signatures require refinement

**Migration**:

```json
// Option A: Embrace strict mode (recommended)
{
  "compilerOptions": {
    "strict": true
  }
}

// Option B: Opt-out if needed (temporary)
{
  "compilerOptions": {
    "strict": false
  }
}
```

### 2. `types` Field Now Defaults to `[]` (Empty Array)

**Impact**: TypeScript will NOT automatically include all `@types` packages; you must list them explicitly.

**Why**: Performance improvement (20-50% faster builds). Prevents bloat from unused type definitions.

**What Breaks**:

```typescript
// Error: Cannot find module 'node' or 'fs'
import { readFile } from 'fs';

// Error: Cannot find name 'describe' (missing @types/jest)
describe('test', () => {
  // ...
});
```

**Migration**:

```json
{
  "compilerOptions": {
    "types": ["node", "jest"]
  }
}
```

**Common `types` Values**:

| Environment | Types |
| ----------- | ----- |
| Node.js | `["node"]` |
| Jest | `["jest"]` |
| Browser | `[]` (usually not needed) |
| Node + Jest | `["node", "jest"]` |
| Vitest | `["vitest/globals"]` |

### 3. `rootDir` Defaults to `.` (tsconfig Directory)

**Impact**: TypeScript determines the root of your source tree differently.

**Old Behavior**: `rootDir` was inferred from common directory of input files.
**New Behavior**: `rootDir` defaults to the directory containing `tsconfig.json`.

**What Breaks**:

```diagram
Project/
├── tsconfig.json
├── src/
│   └── index.ts
└── dist/

// Before: Output would be dist/index.ts (inferred rootDir was src/)
// After: Output would be dist/src/index.ts (rootDir is .)
```

**Migration**:

```json
{
  "compilerOptions": {
    "rootDir": "./src",
    "outDir": "./dist"
  }
}
```

### 4. `target: es5` is Deprecated

**Impact**: No more ES5 support in TypeScript core.

**Reason**: IE is retired, evergreen runtimes are universal.

**Minimum target**: `es2015` (ES6)

**Migration**:

```json
// ❌ Old
{
  "compilerOptions": {
    "target": "es5"
  }
}

// ✅ New
{
  "compilerOptions": {
    "target": "es2020" // or es2025
  }
}

// If you truly need ES5 output:
// Use external transpilers like swc, Babel, or esbuild
```

### 5. Module Resolution Changes

**`--moduleResolution node` (aka `node10`) is Deprecated**

Old modes are no longer recommended:

| Mode | Status | Use Instead |
| ------ | -------- | ------------- |
| `node` (node10) | Deprecated | `nodenext` or `bundler` |
| `classic` | Removed | `nodenext` or `bundler` |

**Migration**:

```json
{
  "compilerOptions": {
    // ✅ For Node.js projects
    "moduleResolution": "nodenext",

    // ✅ For bundled web apps
    "moduleResolution": "bundler"
  }
}
```

### 6. `baseUrl` is Deprecated

**Impact**: Module resolution no longer uses `baseUrl` as a lookup root.

**Only Use With**: Path mappings (and it's optional).

**Migration**:

```json
// ❌ Old
{
  "compilerOptions": {
    "baseUrl": "./src",
    "paths": {
      "@app/*": ["app/*"],
      "@lib/*": ["lib/*"]
    }
  }
}

// ✅ New (without baseUrl)
{
  "compilerOptions": {
    "paths": {
      "@app/*": ["./src/app/*"],
      "@lib/*": ["./src/lib/*"]
    }
  }
}
```

### 7. `module` Values Removed

**Removed**:

- `amd`
- `umd`
- `systemjs`
- `none`

**Use Instead**: ESM-based targets (`esnext`, `es2025`, `es2020`, etc.)

### 8. `esModuleInterop: false` is No Longer Valid

**Impact**: CommonJS/ESM interop is always enabled.

**Migration**:

```json
// ❌ Error in TypeScript 6.0
{
  "compilerOptions": {
    "esModuleInterop": false
  }
}

// ✅ Required (or omit, it defaults to true)
{
  "compilerOptions": {
    "esModuleInterop": true
  }
}

// If you have imports that relied on old behavior:
// Before (esModuleInterop: false)
import * as express from 'express'; // ❌ No longer works

// After (esModuleInterop: true)
import express from 'express'; // ✅ Correct
```

---

## Deprecations

These can be suppressed by setting `"ignoreDeprecations": "6.0"` in `tsconfig.json`, but are removed in TypeScript 7.0:

### `--downlevelIteration`

Only affects ES5 output (which is deprecated). Remove if present.

```json
{
  "compilerOptions": {
    // ❌ Deprecated, causes error if set
    "downlevelIteration": true
  }
}
```

### `--outFile`

Use bundlers (Webpack, esbuild, Vite) instead.

```json
{
  "compilerOptions": {
    // ❌ Deprecated
    "outFile": "./dist/bundle.js"
  }
}
```

### legacy `module` Keyword for Namespaces

```typescript
// ❌ Deprecated
module Foo {
  export const bar = 10;
}

// ✅ Use namespace
namespace Foo {
  export const bar = 10;
}
```

### `asserts` Keyword in Imports (Use `with` Instead)

```typescript
// ❌ Old (deprecated)
import json from './data.json' assert { type: 'json' };

// ✅ New (import attributes)
import json from './data.json' with { type: 'json' };
```

### `--alwaysStrict: false`

Strict mode is always enforced. Remove or set to `true`:

```json
{
  "compilerOptions": {
    // ❌ Cannot be false
    "alwaysStrict": false
  }
}
```

---

## Migration Guide

### Step 1: Run Latest TypeScript

```bash
npm install typescript@latest
```

### Step 2: Identify Errors

```bash
npm run build
# or
npx tsc --noEmit
```

### Step 3: Address Common Issues

#### Issue: `Cannot find module 'node'` or `'fs'`

**Cause**: `types` now defaults to `[]`

**Fix**:

```json
{
  "compilerOptions": {
    "types": ["node"]
  }
}
```

#### Issue: `Cannot find name 'describe'` (in tests)

**Cause**: Test framework types not listed

**Fix**:

```json
{
  "compilerOptions": {
    "types": ["jest"]
  }
}
```

#### Issue: Files compiling to wrong output directory

**Cause**: `rootDir` now defaults to `.`

**Fix**:

```json
{
  "compilerOptions": {
    "rootDir": "./src",
    "outDir": "./dist"
  }
}
```

#### Issue: Strict type errors on previously working code

**Cause**: `strict` defaults to `true`

**Options**:

1. Fix the code (recommended)
2. Temporarily set `"strict": false`
3. Suppress with `"ignoreDeprecations": "6.0"` (temporary)

### Step 4: Update Imports

```typescript
// CommonJS imports with esModuleInterop: true
// ❌ Old
import * as express from 'express';

// ✅ New
import express from 'express';
```

### Step 5: Validate and Test

```bash
npm run build
npm run test
npm run lint
```

---

## tsconfig.json Deep Dive

### Recommended Production Configuration

```json
{
  "compilerOptions": {
    // Module and Target
    "target": "es2025",
    "module": "esnext",
    "moduleResolution": "bundler",
    "lib": ["es2025"],

    // JSX (if needed)
    "jsx": "react-jsx",

    // Strict Type Checking
    "strict": true,
    "noImplicitAny": true,
    "noImplicitThis": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "strictBindCallApply": true,
    "strictPropertyInitialization": true,
    "alwaysStrict": true,

    // Enhanced Checks
    "exactOptionalPropertyTypes": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitReturns": true,
    "noImplicitOverride": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,
    "noPropertyAccessFromIndexSignature": true,
    "noUncheckedSideEffectImports": true,

    // Module Interoperability
    "allowSyntheticDefaultImports": true,
    "esModuleInterop": true,

    // Emit and Output
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    "outDir": "./dist",
    "rootDir": "./src",
    "removeComments": false,

    // Resolution
    "isolatedModules": true,
    "verbatimModuleSyntax": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,

    // Path Mapping
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"],
      "@types/*": ["./src/types/*"],
      "@utils/*": ["./src/lib/utils/*"]
    }
  },
  "include": ["src"],
  "exclude": ["node_modules", "dist", "**/*.spec.ts", "**/*.test.ts"]
}
```

### Option Categories and Explanations

#### Type Checking (Strict Mode)

```json
{
  "compilerOptions": {
    "strict": true,
    // Enables: noImplicitAny, noImplicitThis, strictNullChecks,
    // strictFunctionTypes, strictBindCallApply,
    // strictPropertyInitialization

    "noImplicitAny": true,           // Error on implicit any
    "noImplicitThis": true,          // Error on implicit this: any
    "strictNullChecks": true,        // Null/undefined not assignable
    "strictFunctionTypes": true,     // Strict function parameter checks
    "strictBindCallApply": true,     // Check bind/call/apply
    "strictPropertyInitialization": true, // Require initialization
    "alwaysStrict": true             // Emit 'use strict' and extra checks
  }
}
```

#### Code Quality

```json
{
  "compilerOptions": {
    "exactOptionalPropertyTypes": true, // Distinguish optional vs undefined
    "noUncheckedIndexedAccess": true,   // Index access returns union with undefined
    "noImplicitReturns": true,          // Error on implicit return undefined
    "noImplicitOverride": true,         // Require explicit override keyword
    "noUnusedLocals": true,             // Error on unused variables
    "noUnusedParameters": true,         // Error on unused parameters
    "noFallthroughCasesInSwitch": true, // Error on fall-through cases
    "noPropertyAccessFromIndexSignature": true // Require explicit property access
  }
}
```

#### Module Resolution

```json
{
  "compilerOptions": {
    "moduleResolution": "bundler", // For bundled apps
    // or "nodenext" for Node.js

    "isolatedModules": true,           // Each file can be transpiled independently
    "preserveConstEnums": false,       // Erase enums in output (default)
    "verbatimModuleSyntax": true       // Preserve import/export syntax literally
  }
}
```

#### Declaration and Emit

```json
{
  "compilerOptions": {
    "declaration": true,      // Generate .d.ts files
    "declarationMap": true,   // Map .d.ts to source
    "sourceMap": true,        // Generate .js.map files
    "removeComments": false,  // Keep comments in output
    "inlineSources": true     // Include source code in maps (optional)
  }
}
```

### Environment-Specific Configurations

**For Next.js/React Web Apps**:

```json
{
  "compilerOptions": {
    "target": "es2025",
    "lib": ["es2025", "dom", "dom.iterable"],
    "jsx": "react-jsx",
    "moduleResolution": "bundler"
  }
}
```

**For Node.js Backend**:

```json
{
  "compilerOptions": {
    "target": "es2020",
    "module": "esnext",
    "moduleResolution": "nodenext",
    "types": ["node"],
    "outDir": "./dist",
    "rootDir": "./src"
  }
}
```

**For Monorepo**:

```json
{
  "compilerOptions": {
    "rootDir": "./",
    "baseUrl": "./",
    "paths": {
      "@app/*": ["./packages/app/src/*"],
      "@api/*": ["./packages/api/src/*"],
      "@common/*": ["./packages/common/src/*"]
    }
  },
  "references": [
    { "path": "./packages/app" },
    { "path": "./packages/api" },
    { "path": "./packages/common" }
  ]
}
```

---

## Common Issues and Solutions

### Issue: Build Time Increased

**Cause**: Loading unnecessary `@types` packages (if upgrading from <6.0)

**Solution**:

```json
{
  "compilerOptions": {
    "types": ["node", "jest"] // Explicitly list only needed types
  }
}
```

**Result**: 20-50% faster builds

### Issue: Strict Mode Errors Everywhere

**Cause**: `strict: true` is now default

**Solution** (choose one):

1. **Address errors** (recommended):

   ```typescript
   // Add explicit types
   const user: User | null = getUserOrNull();
   
   // Use optional chaining
   const name = user?.name;
   ```

2. **Temporarily suppress**:

   ```json
   {
     "compilerOptions": {
       "ignoreDeprecations": "6.0",
       "strict": false
     }
   }
   ```

3. **Suppress specific checks**:

   ```json
   {
     "compilerOptions": {
       "strict": true,
       "noUnusedLocals": false  // Allow unused variables
     }
   }
   ```

### Issue: Module Resolution Conflicts

**Cause**: Using deprecated `node` or `classic` resolution

**Solution**:

```json
{
  "compilerOptions": {
    "moduleResolution": "bundler"  // For web apps, recommended for 2026
    // or
    "moduleResolution": "nodenext"  // For Node.js
  }
}
```

### Issue: baseUrl Errors

**Cause**: `baseUrl` no longer serves as module lookup root

**Solution**: Remove `baseUrl` or use only with `paths`:

```json
{
  "compilerOptions": {
    "paths": {
      "@/*": ["./src/*"]
      // baseUrl removed
    }
  }
}
```

### Issue: Cannot Find Test Framework Globals

**Cause**: No automatic `@types` loading

**Solution**:

```json
{
  "compilerOptions": {
    "types": ["jest"]  // Add test framework types
  }
}
```

---

## Performance Tips

1. **Limit `types` array**: Faster builds and less bloat
2. **Use `skipLibCheck: true`**: Skip type-checking in node_modules
3. **Enable `isolatedModules`**: Allows parallel compilation
4. **Use `incremental: true`**: Faster subsequent builds (optional)
5. **Avoid `declaration: true` if not needed**: Adds compilation time
6. **Use `pathRoots` for monorepos**: Faster path resolution

---

## TypeScript 6 vs 7 Migration Path

**TypeScript 6.0**: Transition release

- Deprecation warnings for old patterns
- Can suppress with `"ignoreDeprecations": "6.0"`
- 6.0 → 7.0 mostly smooth if warnings are fixed

**TypeScript 7.0**: Native compiler (Rust port)

- Removes all deprecated options
- Potential **2-3x performance improvements**
- Small behavioral changes (mostly type ordering)
- Full API compatibility maintained

**Recommendations**:

- Address all deprecation warnings in TypeScript 6.0
- Update tsconfig.json to align with 6.0+ defaults
- Test thoroughly before upgrading to 7.0
- Expect breaking changes in tsconfig.json 7.0
