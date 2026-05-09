<div align="center">

<img src="https://raw.githubusercontent.com/x-dockerize/.github/master/assets/x-dockerize-logo.png" width="360" alt="x-dockerize" />


**Production-ready Docker setups.**
Shared base images and ready-to-deploy Compose templates for everything we self-host.

[![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)](https://www.docker.com/)
[![Traefik](https://img.shields.io/badge/Traefik-24A1C1?style=flat-square&logo=traefikproxy&logoColor=white)](https://traefik.io/)
[![GHCR](https://img.shields.io/badge/Images-GHCR-2088FF?style=flat-square&logo=github&logoColor=white)](https://github.com/orgs/x-dockerize/packages)
[![License](https://img.shields.io/badge/License-MIT-22c55e?style=flat-square)](https://opensource.org/licenses/MIT)

</div>

---

<table align="center"><tr><td align="center" width="33%">
<h3>📦</h3><b>One service, one repo</b><br/><sub>No mega-stacks</sub>
</td><td align="center" width="33%">
<h3>🔀</h3><b>Traefik-routed</b><br/><sub>TLS & hostnames out of the box</sub>
</td><td align="center" width="33%">
<h3>🔐</h3><b>.env-driven</b><br/><sub>No baked-in secrets</sub>
</td></tr></table>

---

## ⚡ Flagship — Shared PHP base images

Multi-arch PHP images on **GitHub Container Registry**. Build your apps on top.

```dockerfile
FROM ghcr.io/x-dockerize/php:8.4-fpm
# your app on top
```

| | Versions | Variants | Architectures |
|---|---|---|---|
| 🐘 [`x-dockerize/x-dockerize`](https://github.com/x-dockerize/x-dockerize) | PHP **8.3** · **8.4** · **8.5** | FPM · CLI | `linux/amd64` · `linux/arm64` |

> Pre-installed: `pdo_mysql`, `gd`, `redis` (PECL), `swoole`, `7z`, plus a `deployer` user (UID/GID 1000, runtime-overridable).

---

## 🚀 Compose templates — same shape, every time

```bash
git clone https://github.com/x-dockerize/<service>.git
cd <service> && cp .env.example .env
./install.sh
```

Each repo ships `docker-compose.production.yml`, `.env.example`, `install.sh`, and a `.docker/<service>/` data layout. Most need an external Traefik instance on `traefik-network`.

---

## 📚 Service catalog

### 🔀 Reverse proxy & VPN

| | Repo | What it deploys |
|---|---|---|
| 🌐 | [**traefik**](https://github.com/x-dockerize/traefik) | Traefik v3 + Let's Encrypt + CrowdSec — the routing layer everything else uses |
| 🔒 | [**headscale**](https://github.com/x-dockerize/headscale) | Self-hosted Tailscale control plane |
| 🛡️ | [**firezone**](https://github.com/x-dockerize/firezone) | Self-hosted VPN with web admin (Firezone 0.7.36 + PostgreSQL) |

### 🗄️ Databases & admin

| | Repo | What it deploys |
|---|---|---|
| 🐘 | [**postgres**](https://github.com/x-dockerize/postgres) | PostgreSQL 16 + **automated backups** to DigitalOcean Spaces & Oracle Object Storage (7d/4w/6m retention) |
| 🐬 | [**mysql**](https://github.com/x-dockerize/mysql) | MySQL with persistent volumes |
| 🍃 | [**mongodb**](https://github.com/x-dockerize/mongodb) | MongoDB |
| 🖥️ | [**pgAdmin**](https://github.com/x-dockerize/pgAdmin) | PostgreSQL admin UI behind Traefik |
| 🖥️ | [**phpMyAdmin**](https://github.com/x-dockerize/phpMyAdmin) | MySQL admin UI behind Traefik |

### 💬 Collaboration & dev tools

| | Repo | What it deploys |
|---|---|---|
| 💬 | [**mattermost**](https://github.com/x-dockerize/mattermost) | Mattermost Team Edition (ARM64-optimized) with external MySQL |
| 🔄 | [**n8n**](https://github.com/x-dockerize/n8n) | Workflow automation |
| 📋 | [**plane**](https://github.com/x-dockerize/plane) | Open-source project management (Jira alternative) |
| 🧪 | [**hoppscotch**](https://github.com/x-dockerize/hoppscotch) | API development client (Postman alternative) |

### 🌐 Web apps

| | Repo | What it deploys |
|---|---|---|
| 🔐 | [**vaultwarden**](https://github.com/x-dockerize/vaultwarden) | Bitwarden-compatible password manager |
| 🔗 | [**shlink**](https://github.com/x-dockerize/shlink) | URL shortener (public API + IP-allowlisted admin) |
| 📊 | [**umami**](https://github.com/x-dockerize/umami) | Privacy-friendly analytics (cookie-free) |
| 📬 | [**listmonk**](https://github.com/x-dockerize/listmonk) | Self-hosted newsletter platform |
| 🤖 | [**ollama**](https://github.com/x-dockerize/ollama) | Local LLM runtime + Open WebUI |

### 📧 Mail testing

| | Repo | What it deploys |
|---|---|---|
| ✉️ | [**mailpit**](https://github.com/x-dockerize/mailpit) | Modern catch-all SMTP — recommended for new setups |
| 📮 | [**mailcatcher**](https://github.com/x-dockerize/mailcatcher) | Older catch-all, kept for legacy projects |

### 🛠️ Ops tooling

| | Repo | What it deploys |
|---|---|---|
| 📦 | [**portainer**](https://github.com/x-dockerize/portainer) | Web UI for managing Docker stacks |
| ☁️ | [**coolify**](https://github.com/x-dockerize/coolify) | Self-hosted PaaS (Heroku/Vercel alternative) |

---

<details>
<summary><b>📐 Conventions across all templates</b></summary>

<br/>

- **One service per repo** — multi-service stacks belong in your own infra repo
- **No baked-in secrets** — everything sensitive lives in `.env`
- **Volumes are explicit** — bind-mounts under `.docker/<service>/`, never anonymous
- **Traefik for routing** — TLS & hostnames via labels, not exposed ports
- **External networks for shared resources** — join `traefik-network` / `postgres-network` / `mysql-network` instead of running your own copy
- **`docker-compose.production.yml`** — filename signals intent

</details>

---

<div align="center">

### 🤝 Contributing

A service we use isn't here yet? Got a clean Compose for it? Open a PR.

<br/>

[![GitHub](https://img.shields.io/badge/All_repos-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/orgs/x-dockerize/repositories)
[![GHCR](https://img.shields.io/badge/Images-2088FF?style=for-the-badge&logo=github&logoColor=white)](https://github.com/orgs/x-dockerize/packages)

<sub>Base images MIT licensed · Compose templates inherit upstream service licenses</sub>

</div>
