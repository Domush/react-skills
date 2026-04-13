---
name: vercel
description: Use when deploying, debugging, or operating applications on Vercel from CLI or MCP tools, including monorepo linking, environment variables, build/runtime logs, domains, and CI/CD release workflows.
---

# Vercel

Lean, task-first guidance for Vercel operations.

Core principle: choose the shortest safe path to a verified deployment, then capture evidence with logs and reproducible commands.

## When to Use

- Deploying preview or production builds
- Diagnosing failed builds or runtime errors
- Linking projects in monorepos
- Managing environment variables, domains, or teams
- Automating release flow in CI/CD

Do not use this skill for framework code architecture; use framework-specific skills for app internals.

## Fast Router

- First-time setup or linking: see [workflows.md](workflows.md#L7)
- Common commands by task: see [quick-reference.md](quick-reference.md)
- Production incidents and failures: see [troubleshooting.md](troubleshooting.md)
- Command variants and flags: see [command-map.md](command-map.md)

## Recommended Tool Order

1. Prefer Vercel MCP tools for project/deployment inspection and logs when agent context is isolated or terminal access is limited.
2. Use CLI when you need local build parity, CI shell scripts, or direct command execution in the terminal.
3. For protected preview URLs, use `vercel curl` in terminal workflows, or MCP authenticated fetch tools in non-terminal workflows.

## Guardrails

- Always verify linking before deploy: workspace, team, and project scope.
- Never disable deployment protection to debug preview environments.
- Never hardcode tokens in command history; use environment variables.
- If using prebuilt deploy flow, always pair `vercel build` with `vercel deploy --prebuilt`.

## Minimal Verification Loop

1. Confirm target: team, project, and environment.
2. Deploy or inspect with one command.
3. Validate endpoint health and logs.
4. Roll forward or rollback with explicit command evidence.

## Related Topics

- [quick-reference.md](quick-reference.md)
- [workflows.md](workflows.md)
- [troubleshooting.md](troubleshooting.md)
- [command-map.md](command-map.md)
- [mcp-reference.md](mcp-reference.md)
