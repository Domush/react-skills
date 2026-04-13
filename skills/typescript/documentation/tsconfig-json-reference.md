# tsconfig.json Options Reference

## Quick Start Configuration (Recommended for 2026)

```json
{
  "compilerOptions": {
    // Target and Module
    "target": "es2025",
    "module": "esnext",
    "moduleResolution": "bundler",
    "lib": ["es2025"],
    
    // Strict Type Checking (all enabled via "strict": true)
    "strict": true,
    
    // Enhanced Checks
    "exactOptionalPropertyTypes": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitReturns": true,
    "noImplicitOverride": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,
    "noPropertyAccessFromIndexSignature": true,
    
    // Declarations and Output
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    "outDir": "./dist",
    "rootDir": "./src",
    
    // Resolution
    "resolveJsonModule": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "isolatedModules": true,
    "verbatimModuleSyntax": true,
    
    // Module Interop
    "allowSyntheticDefaultImports": true,
    "esModuleInterop": true,
    
    // Types (explicit for best performance)
    "types": ["node", "jest"],
    
    // Path Mapping
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "include": ["src"],
  "exclude": ["node_modules", "dist", "**/*.spec.ts", "**/*.test.ts"]
}
```

## Category: Target and Module

| Option | Type | Default (6.0+) | Purpose |
|--------|------|-----------------|---------|
| `target` | string | `es2025` | JavaScript version to compile to |
| `module` | string | `esnext` | Module system for output |
| `moduleResolution` | string | (not set) | Algorithm for resolving modules |
| `lib` | string[] | Based on `target` | DOM/ES standard library types to include |

### Target Values

```
es5, es2015, es2016, es2017, es2018, es2019, es2020, 
es2021, es2022, es2023, es2024, es2025, esnext
```

**Recommendation**: Use `es2025` for modern applications. Use `es2020` for maximal compatibility.

### Module Values

```
commonjs, esnext, es2015, es2020, es2022, preserve, bundler, nodenext, node16, node12, amd, umd, systemjs
```

**Recommendation**:

- `esnext` for bundled web apps
- `commonjs` for older Node.js (with `--target es2020`)
- `preserve` if using bundler

### Module Resolution Values

```
classic, node, nodenext, bundler, node12, node16, node10
```

**Recommendation**:

- `bundler` for web apps, Next.js, Vite (DEFAULT 2026)
- `nodenext` for Node.js
- Avoid: `classic`, `node`, `node10` (deprecated in 6.0)

## Category: Strict Type Checking

All enabled via `"strict": true`

| Option | Type | Effect |
|--------|------|--------|
| `noImplicitAny` | boolean | Error on variables with implicit `any` |
| `noImplicitThis` | boolean | Error on `this` with implicit `any` |
| `strictNullChecks` | boolean | `null`/`undefined` not assignable to other types |
| `strictFunctionTypes` | boolean | Stricter function parameter type checks |
| `strictBindCallApply` | boolean | Check `bind`, `call`, `apply` types |
| `strictPropertyInitialization` | boolean | Require initialized class properties |
| `alwaysStrict` | boolean | Use strict mode and emit "use strict" |

## Category: Code Quality and Strictness

| Option | Type | Default | Purpose |
|--------|------|---------|---------|
| `exactOptionalPropertyTypes` | boolean | false | Optional properties must be exactly `Property \| undefined` |
| `noUncheckedIndexedAccess` | boolean | false | Index access returns union with `undefined` |
| `noImplicitReturns` | boolean | false | Error on implicit `undefined` returns |
| `noImplicitOverride` | boolean | false | Require `override` keyword on overridden methods |
| `noUnusedLocals` | boolean | false | Error on unused variables |
| `noUnusedParameters` | boolean | false | Error on unused parameters |
| `noFallthroughCasesInSwitch` | boolean | false | Error on fall-through switch cases |
| `noPropertyAccessFromIndexSignature` | boolean | false | Require explicit property access (no computed lookup) |
| `noUncheckedSideEffectImports` | boolean | false | Error on side-effect imports without explicit understanding |

## Category: Module Interoperability

| Option | Type | Default | Purpose |
|--------|------|---------|---------|
| `allowSyntheticDefaultImports` | boolean | true (6.0+) | Allow `import x from` for CommonJS modules |
| `esModuleInterop` | boolean | true (6.0+) | Compatibility with CommonJS module semantics |

**Note**: In TypeScript 6.0, both must be `true` (or omitted).

## Category: Emit

| Option | Type | Default | Purpose |
|--------|------|---------|---------|
| `declaration` | boolean | false | Generate `.d.ts` files |
| `declarationMap` | boolean | false | Generate `.d.ts.map` files |
| `sourceMap` | boolean | false | Generate `.js.map` files |
| `removeComments` | boolean | false | Remove comments from output |
| `inlineSources` | boolean | false | Include original source in maps |
| `outDir` | string | (omitted) | Directory for compiled output |
| `rootDir` | string | `.` (6.0+) | Root directory for inputs |
| `outFile` | string | N/A | DEPRECATED in 6.0 (use bundlers) |

## Category: Resolution

| Option | Type | Default | Purpose |
|--------|------|---------|---------|
| `baseUrl` | string | DEPRECATED (6.0) | Base directory for non-relative module names |
| `paths` | object | (none) | Path mapping for module imports |
| `rootDirs` | string[] | (none) | Multiple virtual root directories |
| `resolveJsonModule` | boolean | false | Allow importing JSON files as modules |
| `allowJs` | boolean | false | Allow `.js` files in compilation |
| `checkJs` | boolean | false | Type-check `.js` files (requires `allowJs`) |

## Category: Isolation and Scripts

| Option | Type | Default | Purpose |
|--------|------|---------|---------|
| `isolatedModules` | boolean | false | Each file can be transpiled independently |
| `verbatimModuleSyntax` | boolean | false | Preserve import/export syntax in output |
| `noImplicitUseStrict` | boolean | false | Don't emit "use strict" automatically |

## Category: Quality and Performance

| Option | Type | Default | Purpose |
|--------|------|---------|---------|
| `skipLibCheck` | boolean | false | Skip type-checking `.d.ts` files |
| `forceConsistentCasingInFileNames` | boolean | false | Error on case mismatches in imports |
| `incremental` | boolean | false | Save incremental compilation state for faster rebuilds |
| `tsBuildInfoFile` | string | `.tsbuildinfo` | Path to incremental compilation state file |

## Category: JSX

| Option | Type | Default | Purpose |
|--------|------|---------|---------|
| `jsx` | string | undefined | How to handle JSX (`preserve`, `react`, `react-jsx`, etc.) |
| `jsxFactory` | string | `React.createElement` | Factory function for JSX |
| `jsxFragmentFactory` | string | `React.Fragment` | Fragment type for JSX |

**Recommendation**: Use `"jsx": "react-jsx"` for React 17+

## Category: Experimental and Advanced

| Option | Type | Default | Purpose |
|--------|------|---------|---------|
| `experimentalDecorators` | boolean | false | Enable experimental decorator syntax |
| `useDefineForClassFields` | boolean | false | Use `Object.defineProperty` for class fields |
| `stableTypeOrdering` | boolean | false | Match TypeScript 7.0 type ordering (helps migration) |
| `ignoreDeprecations` | string | undefined | Suppress deprecation warnings (e.g., `"6.0"`) |

## Environment-Specific Presets

### React/Next.js Web App

```json
{
  "compilerOptions": {
    "target": "es2025",
    "lib": ["es2025", "dom", "dom.iterable"],
    "jsx": "react-jsx",
    "moduleResolution": "bundler",
    "module": "esnext"
  }
}
```

### Node.js Backend

```json
{
  "compilerOptions": {
    "target": "es2020",
    "module": "commonjs",
    "moduleResolution": "nodenext",
    "types": ["node"],
    "outDir": "./dist",
    "rootDir": "./src"
  }
}
```

### Lambda Functions

```json
{
  "compilerOptions": {
    "target": "es2020",
    "module": "commonjs",
    "moduleResolution": "nodenext",
    "types": ["node"],
    "strict": true
  }
}
```

### Monorepo

```json
{
  "compilerOptions": {
    "composite": true,
    "rootDir": "./",
    "baseUrl": "./",
    "paths": {
      "@yourorg/package-a/*": ["./packages/package-a/src/*"],
      "@yourorg/package-b/*": ["./packages/package-b/src/*"]
    }
  },
  "references": [
    { "path": "./packages/package-a" },
    { "path": "./packages/package-b" }
  ]
}
```

## Performance Tips

1. **Set `types` explicitly** (~40% build improvement)

   ```json
   { "compilerOptions": { "types": ["node"] } }
   ```

2. **Enable `skipLibCheck` for development**

   ```json
   { "compilerOptions": { "skipLibCheck": true } }
   ```

3. **Use `incremental` for watch mode**

   ```json
   { "compilerOptions": { "incremental": true } }
   ```

4. **Don't emit if not needed**

   ```json
   { "compilerOptions": { "noEmit": true } }
   ```

5. **Use `isolatedModules` with bundlers**

   ```json
   { "compilerOptions": { "isolatedModules": true } }
   ```

## Common Mistakes to Avoid

❌ **Using `baseUrl` without `paths`**

```json
{ "compilerOptions": { "baseUrl": "./src" } } // Bad
```

✅ **Use `paths` instead**

```json
{ "compilerOptions": { "paths": { "@/*": ["./src/*"] } } }
```

---

❌ **Setting `strict: false` in production**

```json
{ "compilerOptions": { "strict": false } } // Bad
```

✅ **Address strict errors, not the flag**

```typescript
const user: User | null = getUser();
if (user) {
  console.log(user.name); // Type-safe
}
```

---

❌ **Not listing `@types` packages**

```json
{ "compilerOptions": {} } // Types not found
```

✅ **Explicitly list what you need**

```json
{ "compilerOptions": { "types": ["node", "jest"] } }
```

---

## References

- Official TypeScript tsconfig Reference: <https://www.typescriptlang.org/tsconfig/>
- JSON Schema: <https://json.schemastore.org/tsconfig>
