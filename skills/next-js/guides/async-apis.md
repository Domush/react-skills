# Async APIs in Next.js 16

Use this guide when handling request metadata and dynamic route inputs.

## What Became Async

- `cookies()`
- `headers()`
- `params`
- `searchParams`

## Correct Usage

```tsx
// app/article/[slug]/page.tsx
import { cookies, headers } from "next/headers";

type Params = Promise<{ slug: string }>;
type SearchParams = Promise<{ preview?: string }>;

export default async function ArticlePage(props: {
  params: Params;
  searchParams: SearchParams;
}) {
  const { slug } = await props.params;
  const { preview } = await props.searchParams;

  const cookieStore = await cookies();
  const headerStore = await headers();

  const session = cookieStore.get("session")?.value;
  const userAgent = headerStore.get("user-agent");

  return <p>{`${slug}:${preview ?? "none"}:${session ?? "anon"}:${userAgent ?? "na"}`}</p>;
}
```

## Migration Guidance

- Update signatures and await route inputs in server files.
- Keep async request APIs in server contexts only.
- Avoid temporary sync wrappers unless required for staged migration.

## Common Mistakes

- Destructuring `params` without awaiting
- Calling `cookies()` and `headers()` synchronously
- Using request APIs in Client Components
