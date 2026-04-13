# Vercel Troubleshooting

## Symptom to Action

| Symptom | Likely Cause | Action |
| --- | --- | --- |
| Wrong project/environment deployed | Incorrect link scope or team | Re-run `vercel whoami`; re-link with `vercel link --repo`; run `vercel pull` |
| 401 or access denied during pull/deploy | Missing/invalid auth context | Re-authenticate or refresh token; validate team permissions |
| Preview endpoint returns forbidden | Deployment protection enabled | Use `vercel curl` or Vercel authenticated fetch tools |
| Build succeeds locally but fails in cloud | Env mismatch or build context mismatch | Compare env vars, inspect build logs, verify project root/output settings |
| Runtime errors after successful deploy | Function/env/runtime issue | Query runtime logs and trace by status code/request ID |
| CI deploy hangs for prompts | Interactive CLI in CI | Add non-interactive flags and provide required context |

## Incident Workflow

1. Identify affected deployment URL or ID.
2. Collect build logs and runtime logs.
3. Validate health endpoint.
4. Execute smallest safe remediation:
   - redeploy current commit,
   - promote known-good deployment,
   - rollback.
5. Confirm recovery with logs and endpoint checks.

## Anti-Patterns

- Disabling deployment protection just to test preview URLs.
- Redeploying repeatedly without reading logs.
- Rotating credentials before confirming root cause.
- Mixing prebuilt and remote-build deploy strategies in the same job.

## Related Topics

- [workflows.md](workflows.md)
- [quick-reference.md](quick-reference.md)
- [command-map.md](command-map.md)
