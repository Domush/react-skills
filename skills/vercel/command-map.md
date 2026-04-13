# Vercel Command Map

## Deploy and Release

```bash
vercel                      # preview deploy
vercel --prod               # production deploy
vercel redeploy <url>
vercel promote <url>
vercel rollback <url>
```

## Inspection and Logs

```bash
vercel inspect <url>
vercel inspect <url> --logs
vercel logs <url> --follow
vercel logs <url> --level error
```

## Environment Variables

```bash
vercel env ls
vercel env add KEY production
vercel env update KEY preview
vercel env rm KEY development
vercel env pull .env.local
vercel env run -- npm test
```

## Domains and Teams

```bash
vercel domains ls
vercel whoami
```

## API Escape Hatch

Use `vercel api` for operations not available as first-class CLI subcommands.

## Example: Safe Blue/Green Promotion

```bash
CANDIDATE_URL=$(vercel --prod --skip-domain)
vercel curl /api/health --deployment "$CANDIDATE_URL"
vercel promote "$CANDIDATE_URL"
```

## Related Topics

- [quick-reference.md](quick-reference.md)
- [workflows.md](workflows.md)
- [troubleshooting.md](troubleshooting.md)
