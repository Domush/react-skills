---
name: next-js
description: Use when planning, building, debugging, or migrating a Next.js App Router application, especially when deciding server versus client boundaries, data flow patterns, rendering strategy, caching, route conventions, or Next.js 16 runtime behavior.
metadata:
  priority: 6
  docs:
    - "https://nextjs.org/docs"
    - "https://nextjs.org/docs/app"
  sitemap: "https://nextjs.org/sitemap.xml"
  pathPatterns:
    - "next.config.*"
    - "next-env.d.ts"
    - "app/**"
    - "src/app/**"
    - "pages/**"
    - "src/pages/**"
    - "proxy.*"
    - "middleware.*"
    - "tsconfig*.json"
    - "tailwind.config.*"
    - "postcss.config.*"
  bashPatterns:
    - "\\bnext\\s+(dev|build|start|lint)\\b"
    - "\\bnpm\\s+run\\s+(dev|build|start|lint)\\b"
    - "\\bpnpm\\s+(dev|build|start|lint)\\b"
    - "\\bbun\\s+run\\s+(dev|build|start|lint)\\b"
    - "\\bnpx\\s+create-next-app\\b"
  promptSignals:
    phrases:
      - "next.js"
      - "nextjs"
      - "app router"
      - "server component"
      - "route handler"
      - "server action"
      - "proxy.ts"
    allOf:
      - ["layout", "route"]
    anyOf:
      - "getserversideprops"
      - "getstaticprops"
      - "next/router"
      - "metadata"
      - "hydration"
    noneOf: []
    minScore: 6
  validate:
    - pattern: "getServerSideProps|getStaticProps|getInitialProps"
      message: "Pages Router data APIs are legacy in App Router. Use async Server Components, generateStaticParams, and route handlers."
      severity: recommended
    - pattern: "from\\s+['\"]next/router['\"]"
      message: "next/router is Pages Router only. Use next/navigation in App Router."
      severity: error
    - pattern: "from\\s+['\"]next/head['\"]"
      message: "Use the Metadata API (metadata or generateMetadata) instead of next/head in App Router."
      severity: error
    - pattern: "export\\s+(default\\s+)?function\\s+middleware"
      message: "In Next.js 16, prefer proxy.ts with export function proxy()."
      severity: recommended
    - pattern: "(?<!await )\\bcookies\\(\\s*\\)|(?<!await )\\bheaders\\(\\s*\\)"
      message: "cookies() and headers() are async in Next.js 16. Await them in server contexts."
      severity: recommended
    - pattern: "from\\s+['\"]next-auth['\"]|getServerSession\\("
      message: "Legacy auth path detected. Ensure modern managed auth integration and App Router-compatible patterns."
      severity: recommended
  retrieval:
    aliases:
      - "next-js"
      - "nextjs app router"
      - "next.js 16"
    intents:
      - "choose server vs client component"
      - "migrate pages router to app router"
      - "set up route handlers and server actions"
      - "fix hydration and rendering errors"
      - "configure caching and revalidation"
    entities:
      - "App Router"
      - "Server Components"
      - "Server Actions"
      - "generateMetadata"
      - "generateStaticParams"
      - "proxy.ts"
---

# Next.js Playbook (Optimized)

Purpose: fast, correct decisions for modern Next.js (App Router, React 19, Next.js 16+).

Core rule: default to Server Components for reads, Server Actions for writes, and Route Handlers for external APIs.

## When to Use

- Building new pages, nested layouts, or dynamic routes
- Deciding Server Component versus Client Component boundaries
- Designing data reads, writes, invalidation, and streaming behavior
- Migrating legacy Pages Router patterns to App Router
- Debugging hydration, bundling, runtime, or metadata issues

## When Not to Use

- Pure React Native or non-Next.js frontend work
- Backend-only services not hosted inside Next.js routes/functions

## Fast Workflow

1. Pick route structure first, then component boundaries.
2. Model data flow: read path, write path, invalidation path.
3. Choose runtime only when needed (Node default, Edge selective).
4. Add loading/error/not-found boundaries before shipping.
5. Validate with build plus runtime diagnostics.

## Start Here

| Need | Read |
| --- | --- |
| Route layout and conventions | [guides/routing.md](./guides/routing.md) |
| Server vs client boundaries | [guides/server-components.md](./guides/server-components.md) |
| Reads, writes, and APIs | [guides/data-fetching.md](./guides/data-fetching.md) |
| Async request APIs in Next.js 16 | [guides/async-apis.md](./guides/async-apis.md) |
| Caching and invalidation strategy | [guides/caching-strategies.md](./guides/caching-strategies.md) |
| Hydration and runtime debugging | [guides/debugging.md](./guides/debugging.md) |
| Metadata, SEO, and OG images | [guides/metadata.md](./guides/metadata.md) |

## Practical Recipes

- [examples/form-server-actions.md](./examples/form-server-actions.md)
- [examples/protected-routes.md](./examples/protected-routes.md)
- [examples/pagination-ssg.md](./examples/pagination-ssg.md)
- [examples/dynamic-metadata-og.md](./examples/dynamic-metadata-og.md)

## Decision Cheat Sheet

Use [quick-ref.md](./quick-ref.md) during implementation and code review.

## Common Mistakes

- Treating Client Components as default
- Creating internal APIs for server-to-server reads that should stay in components
- Using legacy Pages Router APIs in App Router files
- Forgetting loading/error/not-found boundaries for async trees
- Selecting Edge runtime without dependency/runtime constraints

## Validation Checklist

- `npm run build` passes
- No new diagnostics from editor/linter
- Route-level loading/error/not-found experiences work
- Mutation paths revalidate tags or paths correctly
- Metadata output is correct for share previews and search
