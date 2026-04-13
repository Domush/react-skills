# Metadata, SEO, and Open Graph

Use this guide for route metadata, social previews, and static SEO assets.

## Metadata API Basics

Use static metadata when values are known at build time.

```ts
// app/layout.tsx
import type { Metadata } from "next";

export const metadata: Metadata = {
  title: {
    default: "NATOfied",
    template: "%s | NATOfied",
  },
  description: "Satirical geopolitics coverage.",
};
```

Use `generateMetadata` when metadata depends on route data.

```ts
// app/article/[slug]/page.tsx
import type { Metadata } from "next";

type Params = Promise<{ slug: string }>;

export async function generateMetadata(props: { params: Params }): Promise<Metadata> {
  const { slug } = await props.params;
  return {
    title: `Article: ${slug}`,
    openGraph: { title: `Article: ${slug}` },
  };
}
```

## OG Image Files

- `app/opengraph-image.tsx` for app-level default OG image
- Segment-specific `opengraph-image.tsx` for per-route visuals

## SEO Asset Conventions

- `robots.ts` for robots policy
- `sitemap.ts` for sitemap generation
- `manifest.webmanifest` for install metadata where needed

## Common Mistakes

- Generating metadata in Client Components
- Fetching the same data twice for page and metadata without memoization strategy
- Missing OG image defaults for social sharing
