# Example: Dynamic Routes with `generateStaticParams`

## Goal

Pre-render popular article pages while keeping route code simple.

## Implementation

```tsx
// app/article/[slug]/page.tsx
import { notFound } from "next/navigation";

type Params = Promise<{ slug: string }>;

export async function generateStaticParams() {
  // Replace with real query for top slugs
  return [{ slug: "nato-summit-brief" }, { slug: "policy-roundup" }];
}

export default async function ArticlePage(props: { params: Params }) {
  const { slug } = await props.params;
  const article = await getArticleBySlug(slug);

  if (!article) notFound();

  return <article>{article.title}</article>;
}

async function getArticleBySlug(slug: string) {
  // Replace with data access implementation
  return { title: slug };
}
```

## Notes

- Combine with segment revalidation for fresh but fast content.
- Use `notFound()` for missing records instead of null UI branches.
