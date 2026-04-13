# Data Fetching, Mutations, and APIs

Use this guide to pick the right data-flow primitive per use case.

## Decision Matrix

| Need | Best Primitive | Why |
| --- | --- | --- |
| Render data in UI | Server Component fetch | Minimal client JS, direct server access |
| Handle form mutation from UI | Server Action | Native form flow and simple invalidation |
| Public or third-party API endpoint | Route Handler | Standard HTTP contract |

## Reads in Server Components

```tsx
// app/articles/page.tsx
import { getArticles } from "@/lib/articles";

export default async function ArticlesPage() {
  const articles = await getArticles();
  return <pre>{JSON.stringify(articles, null, 2)}</pre>;
}
```

## Writes with Server Actions

```ts
// app/articles/actions.ts
"use server";

import { revalidateTag } from "next/cache";
import { createArticle } from "@/lib/articles";

export async function createArticleAction(formData: FormData) {
  const title = String(formData.get("title") ?? "").trim();
  if (!title) throw new Error("Title is required");

  await createArticle({ title });
  revalidateTag("articles", "max");
}
```

## Route Handlers for External Consumers

```ts
// app/api/articles/route.ts
import { NextResponse } from "next/server";
import { getArticles } from "@/lib/articles";

export async function GET() {
  const data = await getArticles();
  return NextResponse.json({ data });
}
```

## Waterfall Avoidance

- Parallelize independent reads with `Promise.all`.
- Stream non-critical sections with Suspense.
- Avoid sequential nested fetches across deep component trees.

## Common Mistakes

- Building internal REST endpoints for server-to-server reads
- Using client-side fetch as default for first-page data
- Mutating data without explicit revalidation strategy
