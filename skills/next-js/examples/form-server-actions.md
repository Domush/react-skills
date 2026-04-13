# Example: Form Mutations with Server Actions

## Goal

Submit a form without client-side API plumbing, validate input, and refresh affected data.

## Implementation

```tsx
// app/admin/articles/NewArticleForm.tsx
import { createArticleAction } from "./actions";

export function NewArticleForm() {
  return (
    <form action={createArticleAction}>
      <label htmlFor="title">Title</label>
      <input id="title" name="title" required minLength={3} />
      <button type="submit">Create</button>
    </form>
  );
}
```

```ts
// app/admin/articles/actions.ts
"use server";

import { revalidateTag } from "next/cache";
import { z } from "zod";

const schema = z.object({ title: z.string().trim().min(3) });

export async function createArticleAction(formData: FormData) {
  const parsed = schema.safeParse({ title: formData.get("title") });
  if (!parsed.success) {
    throw new Error("Invalid article payload");
  }

  // Persist to DB here
  revalidateTag("articles", "max");
}
```

## Why This Pattern

- Keeps write logic server-side
- Avoids manual client fetch boilerplate
- Makes invalidation explicit and reviewable
