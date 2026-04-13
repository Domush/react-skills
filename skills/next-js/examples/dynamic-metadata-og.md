# Example: Dynamic Metadata and OG Images

## Goal

Generate route-specific SEO metadata and social previews from slug-based content.

## Implementation

```tsx
// app/article/[slug]/page.tsx
import type { Metadata } from "next";

type Params = Promise<{ slug: string }>;

export async function generateMetadata(props: { params: Params }): Promise<Metadata> {
  const { slug } = await props.params;
  const title = `Article: ${slug}`;

  return {
    title,
    description: `Read ${slug} on NATOfied.`,
    openGraph: {
      title,
      images: [{ url: `/article/${slug}/opengraph-image` }],
    },
  };
}

export default async function Page() {
  return <div>Article page content</div>;
}
```

```tsx
// app/article/[slug]/opengraph-image.tsx
import { ImageResponse } from "next/og";

export const size = { width: 1200, height: 630 };
export const contentType = "image/png";

type Params = Promise<{ slug: string }>;

export default async function Image(props: { params: Params }) {
  const { slug } = await props.params;

  return new ImageResponse(
    (
      <div
        style={{
          fontSize: 64,
          background: "#ffffff",
          width: "100%",
          height: "100%",
          display: "flex",
          alignItems: "center",
          justifyContent: "center",
        }}
      >
        {slug}
      </div>
    ),
    size,
  );
}
```

## Notes

- Keep OG rendering deterministic and lightweight.
- Reuse route data loaders where practical to avoid metadata/page drift.
