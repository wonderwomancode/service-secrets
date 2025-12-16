# Branding & Customization

Infisical's UI can be customized by building a custom Docker image with modified assets.

## Current Deployment

We currently use the official `infisical/infisical:latest` image. To customize branding, you'll need to build a custom image.

## Option 1: Environment Variables (Simple)

Some basic customization is available via environment variables:

```yaml
env:
  - SITE_URL=https://secrets.alternatefutures.ai
  - SMTP_FROM_NAME=AlternateFutures
```

## Option 2: Custom Docker Image (Full Branding)

### 1. Fork Infisical

```bash
gh repo fork infisical/infisical --clone
cd infisical
```

### 2. Modify Assets

Replace these files:

```
frontend/public/
├── favicon.ico              # Browser tab icon
├── images/
│   ├── logo.svg            # Main logo (sidebar)
│   ├── logo-light.svg      # Light mode logo
│   └── gradientLogo.svg    # Gradient version
```

### 3. Modify Theme Colors

Edit `frontend/src/styles/globals.css`:

```css
:root {
  /* Primary brand color */
  --color-primary: #7c3aed;  /* AlternateFutures purple */
  --color-primary-hover: #6d28d9;

  /* Accent colors */
  --color-accent: #10b981;

  /* Background */
  --color-bg-primary: #0f172a;
  --color-bg-secondary: #1e293b;
}
```

### 4. Modify App Name

Edit `frontend/src/const.ts`:

```typescript
export const PRODUCT_NAME = "AlternateFutures Secrets";
export const PRODUCT_TAGLINE = "Secure secrets management for decentralized cloud";
```

### 5. Build & Push

```bash
# Build the image
docker build -t ghcr.io/alternatefutures/infisical:latest .

# Push to registry
docker push ghcr.io/alternatefutures/infisical:latest
```

### 6. Update Deployment

Modify `deploy-akash.yaml`:

```yaml
services:
  infisical:
    image: ghcr.io/alternatefutures/infisical:latest
    # ... rest of config
```

## Logo Guidelines

### Sizes

| Asset | Dimensions | Format |
|-------|-----------|--------|
| favicon.ico | 32x32, 64x64 | ICO |
| logo.svg | 200x40 | SVG |
| logo-light.svg | 200x40 | SVG |
| gradientLogo.svg | 200x200 | SVG |

### Colors

AlternateFutures brand colors:
- Primary: `#7c3aed` (Purple)
- Secondary: `#10b981` (Green/Teal)
- Background: `#0f172a` (Dark slate)

## Deployment After Branding

After building a custom image:

1. Push to GitHub Container Registry
2. Update `deploy-akash.yaml` with new image tag
3. Use MCP to update deployment:

```
mcp__akash__update-deployment:
  dseq: 24645907
  provider: akash18ga02jzaq8cw52anyhzkwta5wygufgu6zsz6xc
  rawSDL: <updated SDL with new image>
```

## CSP Considerations

If adding external resources (fonts, analytics), update the CSP meta tag in `frontend/public/index.html` or use Cloudflare Configuration Rules to disable script injection that conflicts with Infisical's strict CSP.

Current workaround: Cloudflare Configuration Rule disables:
- Rocket Loader
- Email Obfuscation

## Testing Locally

```bash
# Build locally
docker build -t infisical-custom:dev .

# Run with Docker Compose
docker compose -f docker-compose.dev.yml up
```
