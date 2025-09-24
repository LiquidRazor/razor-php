*PHP, but sharper — custom Docker images built from source with all the modules you actually need.*
---

# 🔪 Razor-PHP

## ✨ What is this?
Stop wasting time compiling PHP from scratch every time you spin up a project.  
**Razor-PHP** gives you:
- ⚡ **Built from source** — no bloated base images.
- 🔌 **All the essentials** (and most of the “you’ll probably need these anyway” extensions).
- 🪶 **Slim & optimized** — stripped down, no fluff.
- 🧩 **Ready for real-world projects** — perfect for APIs, CLI tools, and full-stack apps.


# LiquidRazor PHP (from source)

Lean, security-minded PHP images built from source with a clean runtime.  
Multi-arch (amd64 + arm64), dual-published to **Docker Hub** and **GHCR**, with **cosign signatures** and **SBOM** attestations.


## Quick start

```bash
# Pull FPM (latest 8.4 line)
docker pull liquidrazor/php:8.4-fpm

# Or the exact patch (immutable)
docker pull liquidrazor/php:8.4.12-fpm

# CLI
docker pull liquidrazor/php:8.4-cli

# Dev variants (xdebug, pcov, composer, tools)
docker pull liquidrazor/php:8.4-fpm-dev
docker pull liquidrazor/php:8.4-cli-dev
```

> Mirror on GHCR: `ghcr.io/liquidrazor/php:<tag>`

## What’s inside

- Built **from source** (PHP 8.4.x & 8.3.x)
- Common extensions enabled: `opcache`, `mbstring`, `intl`, `gd` (jpeg/png/webp/freetype), `pdo_pgsql`, `pgsql`, `bcmath`, `sockets`, `pcntl`, `zip`, `xml`, etc.
- PECL: `redis`, `apcu`, `igbinary`, `yaml`
- FPM tuned for containers (`pm=ondemand`, `/ping`, `/status`) + non-root `app` user
- Dev variants: `xdebug`, `pcov`, Composer, basic tools

## Tags

Rolling (move with the line):
- `8.4-fpm`, `8.4-cli`, `8.4-fpm-dev`, `8.4-cli-dev`
- `8.3-fpm`, `8.3-cli`, `8.3-fpm-dev`, `8.3-cli-dev`
- Convenience aliases: `fpm`, `cli`, `fpm-dev`, `cli-dev` → track the latest stable line

Immutable (pin to a patch):
- `8.4.12-fpm`, `8.4.12-cli`, `8.4.12-fpm-dev`, `8.4.12-cli-dev`
- `8.3.25-fpm`, `8.3.25-cli`, `8.3.25-fpm-dev`, `8.3.25-cli-dev`

> Architectures: **linux/amd64** and **linux/arm64** (manifest list auto-selects for you).

## Minimal FPM usage (with Nginx/Caddy/Traefik)

```bash
docker run --rm -p 9000:9000   -u app   -v "$PWD:/app"   -e PHP_MEMORY_LIMIT=256M   liquidrazor/php:8.4-fpm
```

Health endpoints (inside container):
- Ping: `/ping`
- Status: `/status`

## Example: docker compose

```yaml
services:
  php:
    image: liquidrazor/php:8.4-fpm
    user: app
    environment:
      PHP_MEMORY_LIMIT: 256M
    volumes:
      - ./:/app
    expose:
      - "9000"

  caddy:
    image: caddy:alpine
    ports:
      - "8080:80"
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile:ro
      - ./:/app
```

*(Configure Caddy/Nginx to proxy to `php:9000`.)*

## Environment / overrides

Drop `.ini` files in `/etc/php/conf.d` (mount or bake):

```bash
# Example: raise limits
echo "upload_max_filesize=50M" > ./90-upload.ini
docker run -v $PWD/90-upload.ini:/etc/php/conf.d/90-upload.ini:ro liquidrazor/php:8.4-fpm
```

FPM pool defaults (excerpt):
```
listen = 0.0.0.0:9000
pm = ondemand
pm.max_children = 10
pm.process_idle_timeout = 10s
ping.path = /ping
pm.status_path = /status
user = app
group = app
```

## Versioning policy

- **Rolling tags** (e.g., `8.4-fpm`) track the latest patch of that line.  
- **Immutable tags** (e.g., `8.4.12-fpm`) never change once published.  
- Weekly rebuilds pull in distro security updates.  

## Troubleshooting

- Check loaded extensions:
  ```bash
  docker run --rm liquidrazor/php:8.4-cli php -m
  ```
- Confirm arch manifest:
  ```bash
  docker buildx imagetools inspect liquidrazor/php:8.4-fpm
  ```
- FPM connectivity:
  ```bash
  nc -vz localhost 9000   # from a sidecar or host if published
  ```

## Mirrors

- Docker Hub: `docker.io/liquidrazor/php:<tag>`  
- GHCR: `ghcr.io/liquidrazor/php:<tag>`
