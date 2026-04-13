# Server and Client Component Boundaries

Use this guide to choose where code executes and avoid boundary violations.

## Default Strategy

- Start with Server Components.
- Add Client Components only where hooks, browser APIs, or direct interactivity are required.
- Keep client islands small and leaf-oriented.

## Boundary Rules

- Client files must begin with `"use client"`.
- Client Components cannot be async functions.
- Props passed from server to client must be serializable.
- Functions are not serializable unless passed as Server Actions.

## Pattern: Server Wrapper + Client Leaf

```tsx
// app/dashboard/page.tsx (Server Component)
import { getStats } from "@/lib/stats";
import { StatsChart } from "./StatsChart";

export default async function DashboardPage() {
  const stats = await getStats();
  return <StatsChart points={stats.points} />;
}
```

```tsx
// app/dashboard/StatsChart.tsx (Client Component)
"use client";

import { useMemo } from "react";

type StatsChartProps = { points: number[] };

export function StatsChart({ points }: StatsChartProps) {
  const max = useMemo(() => Math.max(...points), [points]);
  return <p>Peak value: {max}</p>;
}
```

## Suspense Placement

- Wrap async-heavy regions with local Suspense boundaries.
- Prefer granular boundaries over one global boundary for large pages.
- Pair Suspense with `loading.tsx` for route-level feedback.

## Common Mistakes

- Marking entire route trees as client for one interactive widget
- Passing class instances, Dates, or functions as client props
- Using browser globals in server code paths
