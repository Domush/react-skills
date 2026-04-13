# Advanced Patterns

Use this guide for complex routing, streaming UX, and self-hosted deployments.

## Parallel Route UX

- Use slots for side-by-side async content.
- Provide `default.tsx` for each slot to handle hard refreshes.
- Keep slot contracts explicit and small.

## Intercepting Routes for Modals

- Use intercepting matchers for URL-addressable modal overlays.
- Keep full-page route available for direct navigation.
- Close modals with back navigation semantics when possible.

## Suspense and Error Layering

- Add narrow Suspense boundaries around high-latency regions.
- Pair boundaries with local `error.tsx` and route-level fallback states.
- Prefer progressive reveal over blank full-page waits.

## Self-Hosting Notes

- Use standalone output for containerized deploys.
- Validate image optimization and filesystem assumptions in target runtime.
- Keep environment variable strategy explicit for build and runtime stages.

## Common Mistakes

- Building modal interception without a direct route fallback
- One giant Suspense boundary around entire app subtree
- Assuming local filesystem persistence in ephemeral hosting
