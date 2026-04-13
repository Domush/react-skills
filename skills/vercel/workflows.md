# Vercel Workflows

## 1. Setup and First Deploy

1. Authenticate and link.
2. Pull project config and env values.
3. Start local dev for parity checks.
4. Deploy preview.
5. Promote to production only after verification.

```bash
vercel login
vercel link --repo
vercel pull
vercel dev
vercel
```

## 2. CI Prebuilt Production Deploy

Use this when CI builds once and deploys artifact output.

```bash
vercel build --prod --standalone
vercel deploy --prebuilt --prod
```

Validation after deploy:

```bash
vercel inspect <deployment-url> --wait
vercel logs <deployment-url> --follow
```

## 3. Diagnose Failed Deployment

1. Inspect deployment status and build logs.
2. Check runtime logs for function-level failures.
3. Validate protected preview route.
4. Decide between redeploy, rollback, or promote known-good.

```bash
vercel inspect <deployment-url> --logs
vercel logs <deployment-url> --follow
vercel curl /api/health --deployment <deployment-url>
```

Decision after diagnosis:

- Env mismatch: update env vars, then redeploy.
- Code defect in current commit: ship a fixed commit and redeploy.
- Known-good deployment exists: promote it.
- Immediate recovery needed: rollback.

```bash
vercel env add KEY production
vercel redeploy <deployment-url>
vercel promote <known-good-url>
vercel rollback <deployment-url>
```

## 4. Monorepo Linking Recovery

Symptom: wrong project deploys from repo root.

```bash
vercel whoami
vercel link --repo
vercel pull
```

Then deploy from the intended app directory.

## 5. Monorepo First Deploy (Full)

Use this when a repository contains multiple deployable apps.

```bash
vercel login
vercel link --repo
vercel whoami
vercel pull
```

If the target app is not the repo root, switch into that app folder before deploying.

```bash
cd apps/my-app
vercel
vercel --prod
```

## Related Topics

- [quick-reference.md](quick-reference.md)
- [troubleshooting.md](troubleshooting.md)
- [command-map.md](command-map.md)
