# Next.js Quick Reference

## Core Decisions

| Question | Preferred Choice | Fallback |
| --- | --- | --- |
| Read data for UI | Server Component | Route Handler when external consumers also need API |
| Mutate data from UI | Server Action | Route Handler for third-party/webhook clients |
| Global request interception | proxy.ts | Route-level auth checks where interception is not needed |
| Component with hooks/browser APIs | Client Component (`use client`) | Server wrapper + small client leaf |
| Runtime choice | Node.js | Edge only for low-latency regional logic and compatible deps |

## App Router File Map

| File | Purpose |
| --- | --- |
| `app/layout.tsx` | Shared shell for segment subtree |
| `app/page.tsx` | Segment page |
| `app/loading.tsx` | Suspense fallback during async render |
| `app/error.tsx` | Segment error boundary |
| `app/not-found.tsx` | Segment 404 UI |
| `app/api/*/route.ts` | Route Handler endpoints |
| `app/**/opengraph-image.tsx` | Dynamic OG image generation |
| `proxy.ts` | Request interception and redirects |

## Legacy to Modern Mapping

| Legacy Pattern | Modern Pattern |
| --- | --- |
| `getServerSideProps` | Async Server Component |
| `getStaticProps` | `generateStaticParams` + static/ISR strategy |
| `next/router` | `next/navigation` |
| `next/head` | Metadata API |
| `middleware.ts` | `proxy.ts` (Next.js 16+) |

## Caching Rules of Thumb

- Cache read-heavy queries with explicit strategy (`force-cache`, `revalidate`, tags).
- Keep writes uncached, then invalidate (`revalidateTag` or `revalidatePath`).
- Avoid cache defaults that hide stale-data bugs.

## Pre-merge Checks

- Build succeeds (`npm run build`)
- No async API misuse (`cookies`, `headers`, `params`, `searchParams`)
- No accidental server/client boundary violations
- Metadata and OG image output verified for critical routes
