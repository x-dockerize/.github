# x-dockerize

A collection of production-ready Docker setups — both shared base images we build on top of, and ready-to-deploy Compose templates for the services we run day to day.

[![Docker](https://img.shields.io/badge/Docker-2496ED?logo=docker&logoColor=white)](https://www.docker.com/)
[![Traefik](https://img.shields.io/badge/Traefik-24A1C1?logo=traefikproxy&logoColor=white)](https://traefik.io/)
[![GHCR](https://img.shields.io/badge/Images-GHCR-2088FF?logo=github&logoColor=white)](https://github.com/orgs/x-dockerize/packages)

---

## What this is

If you've ever spun up a few servers and self-hosted a few services, you know the drill: every service comes with its own Compose file, its own quirks around volumes, environment variables, and TLS, and somewhere there's always a `traefik` block you copied from another project six months ago.

This organization is two things at once:

1. **A shared set of Docker base images** we build our PHP applications on top of, published to GitHub Container Registry.
2. **A library of Compose templates** for the third-party services we self-host — each one production-flavored, each one wired up to integrate cleanly with the rest.

Everything is open source, and everything follows the same conventions so once you understand one repo in here, you understand all of them.

---

## The base images

The org-named repo, [`x-dockerize/x-dockerize`](https://github.com/x-dockerize/x-dockerize), holds **shared PHP Docker base images** used across our projects.

What it ships:

- PHP **8.3, 8.4, and 8.5** — every active version we care about
- Two variants per version: **FPM** (for web servers) and **CLI** (for queue workers and schedulers)
- Pre-installed extensions: `pdo_mysql`, `gd`, `redis` and `swoole` (PECL), system tools like `7z`, plus the math/encoding libraries you always end up needing
- A `deployer` user (UID/GID 1000) that you can override at runtime via `USER_ID` / `GROUP_ID` to match host permissions
- Multi-arch builds (`linux/amd64` and `linux/arm64`)

Every push to `master` triggers GitHub Actions to build all combinations and push them to GHCR. Adding support for a new PHP version is just a matter of adding a Dockerfile under `php/<version>/` — the workflow picks it up automatically.

You'd use them in your own `Dockerfile` like:

```dockerfile
FROM ghcr.io/x-dockerize/php:8.4-fpm
# your app on top
```

---

## The Compose templates

Every other repo under this org is a **single-service Compose template**. They all share the same structure:

```
.docker/<service>/   ← service-specific config and persistent data layout
scripts/             ← helper shell scripts (start, stop, update, backup)
.env.example         ← every knob, documented
docker-compose.production.yml
install.sh           ← initial bootstrap
```

The pattern is always:

```bash
git clone https://github.com/x-dockerize/<service>.git
cd <service>
cp .env.example .env
# edit .env to taste
./install.sh                                       # or:
docker compose -f docker-compose.production.yml up -d
```

Most templates expect an existing **Traefik instance** running on a shared `traefik-network`, and that's how they pick up TLS, routing, and IP allowlists. A few that need an external database also expect a shared `postgres-network` or `mysql-network`.

> If you don't have Traefik yet, start with [`x-dockerize/traefik`](https://github.com/x-dockerize/traefik) — that's the routing layer everything else hangs off of.

---

## What's available

### 🔀 Reverse proxy & VPN

| Repo | What it does |
| --- | --- |
| [`traefik`](https://github.com/x-dockerize/traefik) | Traefik v3 with automatic Let's Encrypt SSL, self-signed certs for local dev, CrowdSec security middleware, and a dashboard protected by IP allowlist. The entry point everything else routes through. |
| [`headscale`](https://github.com/x-dockerize/headscale) | Self-hosted [Tailscale](https://tailscale.com/) control server. |
| [`firezone`](https://github.com/x-dockerize/firezone) | Self-hosted VPN (Firezone 0.7.36) with web admin UI and PostgreSQL backend. |

### 🗄️ Databases & admin tools

| Repo | What it does |
| --- | --- |
| [`postgres`](https://github.com/x-dockerize/postgres) | **PostgreSQL 16 with automated backups baked in.** Daily backups via `prodrigestivill/postgres-backup-local`, dual cloud storage (DigitalOcean Spaces + Oracle Object Storage, both S3-compatible), and tiered retention (7 daily, 4 weekly, 6 monthly). The DB itself isn't publicly exposed. |
| [`mysql`](https://github.com/x-dockerize/mysql) | MySQL Compose template with persistent volumes and a similar backup-friendly layout. |
| [`mongodb`](https://github.com/x-dockerize/mongodb) | MongoDB template. |
| [`pgAdmin`](https://github.com/x-dockerize/pgAdmin) | pgAdmin behind Traefik with Let's Encrypt TLS, accessible at `https://pma.example.com` style hostnames. |
| [`phpMyAdmin`](https://github.com/x-dockerize/phpMyAdmin) | phpMyAdmin behind Traefik, same pattern. |

### 💬 Communication & collaboration

| Repo | What it does |
| --- | --- |
| [`mattermost`](https://github.com/x-dockerize/mattermost) | Mattermost Team Edition (ARM64-optimized image), uses an external MySQL/MariaDB instead of bundling one. |
| [`n8n`](https://github.com/x-dockerize/n8n) | n8n — open-source workflow automation. |
| [`plane`](https://github.com/x-dockerize/plane) | [Plane](https://plane.so/) — open-source project management (Jira-style boards, cycles, modules). |
| [`hoppscotch`](https://github.com/x-dockerize/hoppscotch) | [Hoppscotch](https://hoppscotch.io/) — open-source API development client (Postman alternative). |

### 🌐 Web apps & utilities

| Repo | What it does |
| --- | --- |
| [`vaultwarden`](https://github.com/x-dockerize/vaultwarden) | Bitwarden-compatible self-hosted password manager. |
| [`shlink`](https://github.com/x-dockerize/shlink) | URL shortener — exposes the public redirect API and the admin web client on separate hostnames, with the admin behind an IP allowlist. |
| [`umami`](https://github.com/x-dockerize/umami) | Privacy-friendly, cookie-free web analytics. Connects to an external PostgreSQL via the `postgres-network`. |
| [`listmonk`](https://github.com/x-dockerize/listmonk) | Self-hosted newsletter and mailing-list platform. |
| [`ollama`](https://github.com/x-dockerize/ollama) | Ollama (local LLM runtime) plus Open WebUI for the chat interface, both routed through Traefik with IP allowlist protection. |

### 📧 Mail testing

| Repo | What it does |
| --- | --- |
| [`mailpit`](https://github.com/x-dockerize/mailpit) | Modern catch-all SMTP server with a web UI for inspecting captured email — the recommended option for new setups. |
| [`mailcatcher`](https://github.com/x-dockerize/mailcatcher) | The older catch-all, kept around for projects that still rely on it. |

### 🛠️ Ops tooling

| Repo | What it does |
| --- | --- |
| [`portainer`](https://github.com/x-dockerize/portainer) | Web UI for managing Docker containers and stacks. |
| [`coolify`](https://github.com/x-dockerize/coolify) | Self-hosted PaaS — Heroku/Vercel-style deployment platform. |

> Full list at [github.com/orgs/x-dockerize/repositories](https://github.com/orgs/x-dockerize/repositories).

---

## Conventions

Across every Compose template:

- **One service per repo.** Multi-service stacks belong in your own infrastructure repo, composed from these.
- **No baked-in secrets.** Everything sensitive lives in `.env`. Defaults in `.env.example` are placeholders, never working credentials.
- **Volumes are explicit.** Persistent data goes to bind mounts under `.docker/<service>/` — never anonymous volumes you can't find later.
- **Traefik for routing.** TLS, hostnames, and IP allowlists are configured via Traefik labels, not by exposing service ports directly.
- **External networks for shared resources.** Templates that need Traefik or a shared database join an external Docker network instead of running their own copy.
- **`docker-compose.production.yml`, not `docker-compose.yml`.** The filename signals intent — these are production setups, not "just docker compose up and it works on your laptop" templates.

---

## Contributing

Issues and pull requests are welcome on every repository. If a service we're using isn't in this org yet and you've already got a clean Compose setup for it, open a PR — we're happy to host more.

Per-service questions belong on that repo's issue tracker, not here.

---

## License

The base images in [`x-dockerize`](https://github.com/x-dockerize/x-dockerize) are released under the **MIT License**. Individual Compose templates may not specify a license — when in doubt, treat the configuration as MIT and the dockerized software itself as bound by its own upstream license.
