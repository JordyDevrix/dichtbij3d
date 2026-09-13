# Dichtbij3D — deployment

Single `docker-compose.yml` that runs the whole Dichtbij3D platform from prebuilt
images. **Nothing is built here** — every service references a published image, so this
repository can be used directly as a Portainer stack.

| Service | Image | Exposed |
|---|---|---|
| frontend | `ghcr.io/jordydevrix/dichtbij3d-frontend` | `${FRONTEND_PORT}` → 80 |
| backend | `ghcr.io/jordydevrix/dichtbij3d-backend` | internal only |
| postgres | `postgres:16-alpine` | internal only |
| minio | `quay.io/minio/minio` | console on `${MINIO_CONSOLE_PORT}` |

The frontend container is the only entry point: nginx serves the web build and proxies
`/api` to the backend over the internal network. That means **no CORS, no public
database port and no backend host baked into the JavaScript bundle**.

## Quick start

```bash
cp .env.example .env
# fill in JWT_SECRET (openssl rand -hex 48), ADMIN_PASSWORD and the other secrets
docker compose up -d
```

Open <http://localhost:8088> and sign in with `ADMIN_EMAIL` / `ADMIN_PASSWORD`.

## Deploying with Portainer

1. **Stacks → Add stack → Repository**
2. Repository URL: this repository, compose path `docker-compose.yml`
3. Add the environment variables from `.env.example` (at minimum `JWT_SECRET`,
   `ADMIN_PASSWORD`, `PUBLIC_URL`, `FRONTEND_PORT`)
4. Enable **GitOps updates** — polling, or a webhook that the release workflows in the
   backend and frontend repositories can call after they publish a new image
5. Deploy

Pin `BACKEND_TAG` / `FRONTEND_TAG` to a version (`1.4.0`) for reproducible deploys, or
leave them on `latest` and let Portainer re-pull.

Images are public on GHCR by default. If you make a package private, add a registry
credential in Portainer (username = GitHub user, password = a PAT with `read:packages`).

## Updating

```bash
docker compose pull && docker compose up -d
```

Flyway runs the migrations automatically on backend start.

## Production notes

* Put a TLS reverse proxy (Caddy, Traefik, nginx) in front and set
  `PUBLIC_URL=https://dichtbij3d.nl` and `WEBAUTHN_RP_ID=dichtbij3d.nl`.
  Passkeys only work over HTTPS, and the origin must match exactly.
* `DEMO_DATA=false` in production — demo accounts all share a known password.
* Back up the `pgdata` and `miniodata` volumes; MinIO holds every uploaded image and
  3D model file.
* The backend never exposes a port on the host. If you need direct API access, add
  `ports: ['8080:8080']` to the `backend` service and widen `CORS_ORIGINS`.

## Source repositories

* Backend — <https://github.com/JordyDevrix/dichtbij3d-backend>
* Frontend — <https://github.com/JordyDevrix/dichtbij3d-frontend>

Both publish images to GHCR on every push to `main` (`:latest`, `:main`, `:sha-…`) and
on every `v*.*.*` tag (`:1.2.3`, `:1.2`, `:1`).
