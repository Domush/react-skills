# Debugging and Diagnostics

Use this guide to fix hydration issues, runtime errors, and build-time mismatches.

## Fast Triage Order

1. Reproduce in dev and check terminal build/runtime logs.
2. Capture route-level errors from Next.js diagnostics tools.
3. Verify server/client boundaries for the failing route.
4. Check hydration mismatch patterns.
5. Confirm runtime compatibility (Node vs Edge).

## Hydration Mismatch Checklist

- Browser-only APIs used during server render
- Time-variant values rendered on server without stabilization
- Random values/IDs generated during render
- Invalid HTML nesting
- Third-party script side effects

## Boundary Mismatch Checklist

- Async Client Component declarations
- Non-serializable props passed to client leaves
- Unintended client promotion from `use client` in shared files

## Build and Bundle Checks

- Validate ESM/CJS compatibility for imported packages
- Mark browser-only packages client-side only
- Analyze large client chunks before merging

## Helpful Commands

```bash
npm run dev
npm run build
npm run lint
```

## Common Mistakes

- Debugging only in browser without checking server output
- Fixing symptoms in client code when root cause is server boundary misuse
- Ignoring route-level loading/error behavior during triage
