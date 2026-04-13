# Example: Protecting Routes with proxy.ts

## Goal

Gate admin routes and redirect unauthenticated users before route code executes.

## Implementation

```ts
// proxy.ts
import { NextResponse } from "next/server";
import type { NextRequest } from "next/server";

const protectedPrefixes = ["/admin", "/settings"];

export function proxy(request: NextRequest) {
  const isProtected = protectedPrefixes.some((prefix) =>
    request.nextUrl.pathname.startsWith(prefix),
  );

  if (!isProtected) return NextResponse.next();

  const session = request.cookies.get("session")?.value;
  if (!session) {
    return NextResponse.redirect(new URL("/login", request.url));
  }

  return NextResponse.next();
}
```

## Notes

- Keep proxy checks fast and deterministic.
- Perform deeper authorization in route/page server code.
