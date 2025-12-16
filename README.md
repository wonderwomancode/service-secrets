# AlternateFutures Secrets Service

Self-hosted [Infisical](https://infisical.com) secrets management deployed on Akash Network.

## Live Instance

- **URL:** https://secrets.alternatefutures.ai
- **Deployment:** Akash Network (dseq: 24645907)
- **Provider:** Europlots (`akash18ga02jzaq8cw52anyhzkwta5wygufgu6zsz6xc`)

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Akash Network                             │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │  Infisical  │──│  PostgreSQL │  │    Redis    │        │
│  │   (1 CPU)   │  │   (1 CPU)   │  │  (0.5 CPU)  │        │
│  │   1Gi RAM   │  │   1Gi RAM   │  │  256Mi RAM  │        │
│  └─────────────┘  └─────────────┘  └─────────────┘        │
└─────────────────────────────────────────────────────────────┘
                          │
                    Cloudflare Proxy
                          │
              secrets.alternatefutures.ai
```

## Quick Start

### Access the UI

1. Go to https://secrets.alternatefutures.ai
2. Log in with your organization credentials

### CLI Access

```bash
# Install Infisical CLI
brew install infisical/get-cli/infisical

# Login to your instance
infisical login --domain=https://secrets.alternatefutures.ai

# Run commands with secrets injected
cd your-project
infisical run --env=prod --path=/service-cloud-api -- npm run dev
```

### GitHub Actions

```yaml
- name: Fetch secrets from Infisical
  uses: Infisical/secrets-action@v1
  with:
    client-id: ${{ secrets.INFISICAL_CLIENT_ID }}
    client-secret: ${{ secrets.INFISICAL_CLIENT_SECRET }}
    env-slug: prod
    project-slug: alternatefutures-platform
    secret-path: /service-cloud-api
    api-url: https://secrets.alternatefutures.ai/api
```

## Project Structure

```
/production/
├── shared/                    # Shared across services
│   └── JWT_SECRET
├── service-cloud-api/         # Backend API secrets
│   ├── DATABASE_URL
│   ├── PINATA_*
│   ├── STRIPE_*
│   └── ...
├── service-auth/              # Auth service secrets
│   ├── JWT_REFRESH_SECRET
│   ├── RESEND_API_KEY
│   ├── GOOGLE_CLIENT_*
│   └── ...
├── web-app/                   # Frontend secrets
│   └── VITE_*
├── infrastructure-proxy/      # Proxy TLS certs
│   ├── PINGAP_TLS_CERT
│   └── PINGAP_TLS_KEY
└── akash/                     # Akash deployment
    └── AKASH_MNEMONIC
```

## Machine Identities

| Name | Client ID | Use |
|------|-----------|-----|
| `github-actions-ci` | `73597b4a-080a-4d78-b800-f3b9e142c65a` | GitHub Actions CI/CD |

## Deployment

See [DEPLOYMENT.md](./DEPLOYMENT.md) for:
- Initial deployment to Akash
- Updating the deployment
- Disaster recovery

## Branding & Customization

See [BRANDING.md](./BRANDING.md) for:
- Custom logo/colors
- Building a custom Docker image
- UI customization

## Cost

~$12/month on Akash Network:
- Infisical: ~20 uakt/block
- PostgreSQL: ~25 uakt/block
- Redis: ~10 uakt/block

## Security

- All data encrypted at rest (AES-128-GCM)
- TLS via Cloudflare Origin Certificate
- Deployed on audited Akash providers
- Service tokens rotatable, auditable

## Related Repos

- [service-cloud-api](https://github.com/alternatefutures/service-cloud-api) - Backend API
- [service-auth](https://github.com/alternatefutures/service-auth) - Auth service
- [infrastructure-proxy](https://github.com/alternatefutures/infrastructure-proxy) - Edge proxy
