# Deployment
The application runs on a VPS using Docker Compose. GitHub Actions deploys through **Tailscale SSH**.

## Prerequisites
- Docker Engine and Docker Compose installed on the VPS.
- VPS connected to Tailscale with Tailscale SSH enabled.
- Tailscale policies allow `tag:ci` to reach the VPS on port 22 and SSH as the deployment user.
- Reverse proxy running, with the external Docker network `nginx-proxy-net`.

## One-time setup
### VPS
Clone the repository:
```bash
mkdir -p ~/docker
cd ~/docker
git clone https://github.com/lolo8vbdjsk81a/nonamegrapher.git nonamegrapher
cd nonamegrapher
```

Create `.env` from `.env.example`. Never commit the real `.env`.

### GitHub Actions
Add these repository secrets under Settings → Secrets and variables → Actions:

| Secret | Purpose |
|---|---|
| TS_OAUTH_CLIENT_ID | Tailscale OAuth client ID |
| TS_OAUTH_SECRET | Tailscale OAuth client secret |
| VPS_USER | VPS deployment username |
| VPS_HOST | VPS Tailscale hostname or address |

## Deployment
Push to `main`. The workflow connects through Tailscale SSH and runs:
```bash
cd ~/docker/nonamegrapher
git pull --ff-only &&
docker compose build &&
docker compose up -d
```

## Troubleshooting
- SSH fails: check Tailscale SSH is enabled, the destination/user are correct, and both network and SSH policies allow access.
- Git pull fails: inspect `git status --short` and preserve local changes before retrying.
- Site fails: check `docker compose ps`, `docker compose logs --tail=100`, and reverse-proxy routing.

## Recovery
Record the last working commit or image before significant deployments. If deployment fails, redeploy that version. Database changes need a separate recovery plan.
