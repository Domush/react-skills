# Vercel MCP Reference

Use these tools when terminal execution is unavailable, when you need structured API responses, or when operating in an isolated agent context.

## Core Inspection Tools

| Goal | Tool |
| --- | --- |
| List projects | `mcp_com_vercel_ve_list_projects` |
| List deployments | `mcp_com_vercel_ve_list_deployments` |
| Get deployment details | `mcp_com_vercel_ve_get_deployment` |
| Get build logs | `mcp_com_vercel_ve_get_deployment_build_logs` |
| Get runtime logs | `mcp_com_vercel_ve_get_runtime_logs` |

## Protected Preview Access

Use authenticated fetch for Vercel-protected URLs:

- `mcp_com_vercel_ve_web_fetch_vercel_url` for direct fetch
- `mcp_com_vercel_ve_get_access_to_vercel_url` for temporary shareable access URL

CLI equivalent:

```bash
vercel curl /api/health --deployment <deployment-url>
```

## Tool Selection

- Choose MCP when you need typed JSON responses for automation.
- Choose CLI when running local scripts, CI command chains, or shell-native workflows.
- For incident response, combine both: MCP for broad listing/filtering, CLI for immediate command execution.

## Incident Example (MCP + CLI)

1. List recent deployments with MCP.
2. Pull build/runtime logs with MCP filters.
3. Run `vercel curl` health checks against candidate URLs.
4. Promote or rollback with CLI commands from [command-map.md](command-map.md).

## Related Topics

- [SKILL.md](SKILL.md)
- [workflows.md](workflows.md)
- [quick-reference.md](quick-reference.md)
- [troubleshooting.md](troubleshooting.md)
