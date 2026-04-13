# Directives (`use client`, `use server`, `use cache`)

Use this guide to apply directives intentionally instead of spreading them broadly.

## `use client`

Apply only to files requiring hooks, browser APIs, or direct event handlers.

- Scope it to leaf components when possible.
- Avoid marking large layout trees as client without need.

## `use server`

Use for Server Actions and server-only callable logic.

- Keep action inputs validated.
- Keep action outputs serializable.
- Pair writes with explicit revalidation.

## `use cache`

Use where cache components mode or explicit cache semantics are required.

- Apply for stable, expensive read paths.
- Combine with cache invalidation tags/paths where needed.

## Common Mistakes

- Adding `use client` to shared utility modules
- Mixing browser-only code in `use server` modules
- Using directives as a workaround for architecture issues
