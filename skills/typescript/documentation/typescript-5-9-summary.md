# TypeScript 5.9 Release Notes Summary

## Key Features

### 1. Minimal and Updated `tsc --init`

- Generates smaller, more focused `tsconfig.json`
- Prescriptive defaults for modern TypeScript development
- Includes: `moduleDetection: "force"`, `target: "esnext"`, `jsx: "react-jsx"`

### 2. Support for `import defer`

- ECMAScript proposal for deferred module evaluation
- Modules only execute when exports are accessed
- Useful for lazy-loading and performance optimization

```typescript
import defer * as feature from './some-feature.js';
// Feature won't load until accessed
console.log(feature.specialConstant);
```

### 3. `--module node20`

- Stable option modeling Node.js v20 behavior
- Unlike `nodenext`, will not receive new features
- Implies `--target es2023` by default

### 4. Summary Descriptions in DOM APIs

- TypeScript DOM types now include MDN-based summaries
- Better hover information in editors

### 5. Expandable Hovers (Preview)

- Expand/collapse type information in hovers
- Better exploration of complex types

### 6. Configurable Maximum Hover Length

- New setting `js/ts.hover.maximumLength` in VS Code
- Default hover length increased

## Notable Behavioral Changes

### `lib.d.ts` Changes

- `ArrayBuffer` is no longer a supertype of `TypedArray` types
- May introduce new errors with typed array handling

### Type Argument Inference Changes

- More precise type inference to avoid "leaks" of type variables
- May require adding explicit type arguments in some cases

## Performance Improvements

- **Cache Instantiations on Mappers**: Avoids redundant type instantiation work
- **Avoiding Closure Creation**: Performance improvements in file existence checks
- Results in measurable speedups in large projects

## References

- Full TypeScript 5.9 changelog at: <https://www.typescriptlang.org/docs/handbook/release-notes/typescript-5-9.html>
