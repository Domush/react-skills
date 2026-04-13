# Vercel Quick Reference

## Top Commands

| Task | Command |
| --- | --- |
| Login | `vercel login` |
| Link single project | `vercel link` |
| Link monorepo | `vercel link --repo` |
| Pull project/env config | `vercel pull` |
| Start local dev | `vercel dev` |
| Deploy preview | `vercel` |
| Deploy production | `vercel --prod` |
| Build locally for prebuilt deploy | `vercel build --prod` |
| Deploy prebuilt output | `vercel deploy --prebuilt --prod` |
| Inspect deployment | `vercel inspect <url> --wait` |
| View build/runtime logs | `vercel logs <url> --follow` |
| Access protected preview endpoint | `vercel curl /api/health --deployment <url>` |
| Redeploy | `vercel redeploy <url>` |
| Promote to production | `vercel promote <url>` |
| Roll back | `vercel rollback <url>` |
| List env vars | `vercel env ls` |
| Add env var | `vercel env add KEY production` |
| Pull env vars to file | `vercel env pull .env.local` |
| Run command with env vars | `vercel env run -- npm test` |

## MCP-First Equivalents

| Need | MCP Tool |
| --- | --- |
| List deployments | `mcp_com_vercel_ve_list_deployments` |
| Get deployment details | `mcp_com_vercel_ve_get_deployment` |
| Read build logs | `mcp_com_vercel_ve_get_deployment_build_logs` |
| Read runtime logs | `mcp_com_vercel_ve_get_runtime_logs` |
| Fetch protected deployment URL | `mcp_com_vercel_ve_web_fetch_vercel_url` |
| List projects | `mcp_com_vercel_ve_list_projects` |

## Safety Checklist Before Production Deploy

- Confirm active team and project are correct.
- Confirm required production env vars exist.
- Confirm build passes locally if using prebuilt flow.
- Confirm rollback target is known.

For MCP usage examples and selection guidance, see [mcp-reference.md](mcp-reference.md).
