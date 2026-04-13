# Routing and File Conventions

Use this guide when shaping route trees, nested layouts, and special App Router files.

## Route Structure Principles

- Model URL hierarchy first, then map UI composition to nested layouts.
- Keep route groups (`(group)`) for organization without URL impact.
- Keep private implementation folders prefixed with `_` when they are not routes.

## Essential Files

| File | Scope | Purpose |
| --- | --- | --- |
| `layout.tsx` | Segment subtree | Shared shell and providers |
| `page.tsx` | Segment | Route content |
| `loading.tsx` | Segment subtree | Streaming fallback UI |
| `error.tsx` | Segment subtree | Recoverable segment errors |
| `not-found.tsx` | Segment subtree | Segment 404 state |
| `default.tsx` | Parallel slots | Fallback content for unmatched slot |

## Dynamic Segments

- `[slug]`: one segment
- `[...slug]`: catch-all
- `[[...slug]]`: optional catch-all

For static generation of dynamic routes, use `generateStaticParams`.

## Parallel and Intercepting Routes

- Use slots (`@slot`) for simultaneous subtrees.
- Add `default.tsx` to avoid hard-refresh 404s on slots.
- Use intercepting matchers (`(.)`, `(..)`, `(...)`) for modal overlays and route interception.

## Proxy in Next.js 16

Prefer `proxy.ts` for global interception (auth redirects, rewrites, headers).

```ts
// proxy.ts
import { NextResponse } from "next/server";
import type { NextRequest } from "next/server";

export function proxy(request: NextRequest) {
  const isAdmin = request.nextUrl.pathname.startsWith("/admin");
  const hasSession = Boolean(request.cookies.get("session"));

  if (isAdmin && !hasSession) {
    return NextResponse.redirect(new URL("/login", request.url));
  }

  return NextResponse.next();
}
```

## Common Mistakes

- Putting both `page.tsx` and `route.ts` in the same segment path
- Missing `default.tsx` for parallel slots
- Treating folders as route segments when they should be route groups
- Overusing intercepting routes where a normal nested route is simpler
