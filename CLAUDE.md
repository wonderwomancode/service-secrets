# CLAUDE.md

## Project Overview

This repository contains the infrastructure configuration for AlternateFutures' self-hosted Infisical secrets management service, deployed on Akash Network.

## Key Files

| File | Purpose |
|------|---------|
| `deploy-akash.yaml` | Akash SDL for deployment (Infisical + PostgreSQL + Redis) |
| `DEPLOYMENT.md` | Detailed deployment and operations guide |
| `BRANDING.md` | UI customization instructions |

## Current Deployment

- **DSEQ:** 24645907
- **Provider:** Europlots (`akash18ga02jzaq8cw52anyhzkwta5wygufgu6zsz6xc`)
- **Ingress:** `v8c1fui9p1dah5m86ctithi5ok.ingress.europlots.com`
- **URL:** https://secrets.alternatefutures.ai

## Infisical API

- **Base URL:** https://secrets.alternatefutures.ai/api
- **Project ID:** `7229df81-d009-429d-a7cc-db5d4f070668`
- **Organization ID:** `6d0888c0-e40a-4a34-8523-872122cef35a`

## Common Operations

### Update Deployment (env vars only)

Use Akash MCP:
```
mcp__akash__update-deployment:
  dseq: 24645907
  provider: akash18ga02jzaq8cw52anyhzkwta5wygufgu6zsz6xc
  rawSDL: <contents of deploy-akash.yaml>
```

### Check Logs

```
mcp__akash__get-logs:
  owner: akash1degudmhf24auhfnqtn99mkja3xt7clt9um77tn
  dseq: 24645907
  gseq: 1
  oseq: 1
  provider: akash18ga02jzaq8cw52anyhzkwta5wygufgu6zsz6xc
  service: infisical
```

### Add Funds

```
mcp__akash__add-funds:
  address: akash1degudmhf24auhfnqtn99mkja3xt7clt9um77tn
  dseq: 24645907
  amount: 5000000uakt
```

## Secrets in deploy-akash.yaml

The SDL contains hardcoded secrets for:
- `ENCRYPTION_KEY` - AES-128-GCM encryption key (32 hex chars)
- `AUTH_SECRET` - JWT signing secret
- `POSTGRES_PASSWORD` - Database password
- `SMTP_PASSWORD` - Resend API key for emails

These should be rotated periodically. After rotation, update the SDL and redeploy.

## CSP Issue Workaround

Infisical has a strict CSP that conflicts with Cloudflare's script injection. Solution:
- Cloudflare Configuration Rule for `secrets.alternatefutures.ai`:
  - Rocket Loader: Off
  - Email Obfuscation: Off

## Related

- Infisical project: `alternatefutures-platform`
- GitHub Actions machine identity client ID: `73597b4a-080a-4d78-b800-f3b9e142c65a`
