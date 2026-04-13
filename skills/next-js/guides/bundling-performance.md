# Bundling and Performance

Use this guide for bundle size, runtime compatibility, and rendering performance.

## Runtime Selection

- Default to Node runtime unless edge placement clearly improves latency and dependencies are edge-safe.
- Audit Node-specific APIs before switching to Edge.

## Bundle Hygiene

- Keep server-only dependencies out of client bundles.
- Split large client components and lazy-load non-critical UI.
- Use dynamic import for optional heavy modules.

## Rendering Performance

- Prevent fetch waterfalls with parallel requests.
- Stream expensive subtrees with Suspense.
- Cache stable reads with route-appropriate revalidation.

## Asset Optimization

- Use `next/image` with explicit dimensions or fill behavior.
- Use `next/font` for self-hosted, optimized font delivery.
- Use `next/script` with explicit loading strategy for third-party scripts.

## Common Mistakes

- Shipping heavy chart/editor libraries in initial route bundles
- Running all routes as `no-store` and increasing origin load
- Treating bundle analysis as optional in performance-sensitive pages
