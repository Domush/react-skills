# Caching and Revalidation

Use this guide to design predictable freshness for App Router data paths.

## Caching Layers

- Fetch cache: response-level caching for fetch calls
- Route/render cache: statically rendered output and revalidation
- Tag/path invalidation: explicit cache invalidation after writes

## Practical Defaults

- For high-change dashboards: `cache: "no-store"`
- For read-mostly collections: `next: { revalidate: 300, tags: ["articles"] }`
- For immutable assets/content: `cache: "force-cache"`

## Invalidate after Mutations

```ts
"use server";

import { revalidatePath, revalidateTag } from "next/cache";

export async function updateArticleAction(id: string) {
  // write to database
  revalidateTag("articles", "max");
  revalidatePath(`/article/${id}`);
}
```

## Choosing Tag vs Path

- Use tags for shared datasets consumed by many routes.
- Use path invalidation for a specific route after targeted writes.
- Use both when a mutation affects list and detail views.

## Common Mistakes

- Invalidating nothing after writes
- Overusing `no-store` and losing cache benefits globally
- Using one strategy for every route regardless of data volatility
