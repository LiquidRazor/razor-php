*PHP, but sharper — custom Docker images built from source with all the modules you actually need.*

---

# 🔪 Razor-PHP

## ✨ What is this?

Stop wasting time compiling PHP from scratch every time you spin up a project.

**Razor-PHP** gives you:

* ⚡ **Built from source** — no bloated base images.
* 🔌 **All the essentials** — and most of the “you’ll probably need these anyway” extensions.
* 🪶 **Slim & optimized** — stripped down, no fluff.
* 🧩 **Ready for real-world projects** — suitable for APIs, CLI tools, workers, and full-stack apps.

# LiquidRazor PHP Images

Lean, security-minded PHP images built from source with a clean runtime.

Razor-PHP publishes CLI and FPM images for the latest stable PHP major release and the immediately previous stable PHP major release.

Images are multi-arch, published for both `linux/amd64` and `linux/arm64`, and are dual-published to **Docker Hub** and **GHCR**.

All images are compiled against the latest stable Debian major release.

## Quick start

Use the global aliases when you want the latest stable PHP line:

```bash
# Pull latest stable FPM image
docker pull liquidrazor/php:fpm

# Pull latest stable CLI image
docker pull liquidrazor/php:cli

# Pull latest stable development variants
docker pull liquidrazor/php:fpm-dev
docker pull liquidrazor/php:cli-dev
```

Use major-line tags when you want to stay on a specific supported PHP major version:

```bash
# Replace <major> with a supported PHP major version
docker pull liquidrazor/php:<major>-fpm
docker pull liquidrazor/php:<major>-cli
docker pull liquidrazor/php:<major>-fpm-dev
docker pull liquidrazor/php:<major>-cli-dev
```

Use immutable patch tags when reproducible builds matter:

```bash
# Replace <version> with an exact published PHP patch version
docker pull liquidrazor/php:<version>-fpm
docker pull liquidrazor/php:<version>-cli
docker pull liquidrazor/php:<version>-fpm-dev
docker pull liquidrazor/php:<version>-cli-dev
```

> Mirror on GHCR: `ghcr.io/liquidrazor/php:<tag>`

## What’s inside

* Built **from source** for the latest two stable PHP major versions.
* Compiled against the latest stable Debian major release.
* Common extensions enabled: `opcache`, `mbstring`, `intl`, `gd` with JPEG/PNG/WebP/FreeType support, `pdo_pgsql`, `pgsql`, `bcmath`, `sockets`, `pcntl`, `zip`, `xml`, and other common runtime extensions.
* PECL extensions: `redis`, `apcu`, `igbinary`, `yaml`.
* FPM tuned for containers with `pm=ondemand`, `/ping`, `/status`, and a non-root `app` user.
* Development variants include `xdebug`, `pcov`, Composer, and basic development tools.

## Supported platform versions

### PHP

Razor-PHP supports:

* the latest stable PHP major release;
* the immediately previous stable PHP major release.

When a new PHP major version becomes stable, support shifts forward:

* the new stable major is added;
* the previous stable major remains supported;
* the oldest previously supported major is retired.

This keeps the image set current without pretending old runtimes deserve eternal life because someone, somewhere, forgot to upgrade a cron script in 2019.

### Debian

All Razor-PHP images are compiled against the latest stable Debian major release.

When a new Debian major version becomes stable, the image build baseline moves forward to that Debian release. Older Debian major releases are not kept as long-term build targets unless explicitly required for a temporary compatibility migration.

The Debian baseline is intentionally tied to the latest stable major release so the images receive modern toolchains, current system libraries, and actively maintained security updates.

## Tags

### Global aliases

Global aliases track the latest stable supported PHP line:

* `fpm`
* `cli`
* `fpm-dev`
* `cli-dev`

Use these when you want current stable PHP without pinning a major version.

### Rolling major tags

Rolling major tags track the latest patch release within a supported PHP major version:

* `<major>-fpm`
* `<major>-cli`
* `<major>-fpm-dev`
* `<major>-cli-dev`

Use these when you want to stay on one supported PHP major line while still receiving patch updates.

### Immutable patch tags

Immutable patch tags point to one exact PHP patch release and never change once published:

* `<version>-fpm`
* `<version>-cli`
* `<version>-fpm-dev`
* `<version>-cli-dev`

Use these for reproducible builds, release artifacts, CI baselines, and anything where “latest” is not a strategy but a small controlled fire.

### Architectures

Images are published as multi-architecture manifest lists for:

* `linux/amd64`
* `linux/arm64`

Docker automatically pulls the correct architecture for your platform.

## Minimal FPM usage

```bash
docker run --rm \
  -p 9000:9000 \
  -u app \
  -v "$PWD:/app" \
  -e PHP_MEMORY_LIMIT=256M \
  liquidrazor/php:fpm
```

Health endpoints inside the container:

* Ping: `/ping`
* Status: `/status`

## Example: Docker Compose

```yaml
services:
  php:
    image: liquidrazor/php:fpm
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

Configure Caddy, Nginx, Traefik, or whatever reverse-proxy beast you keep in the basement to forward PHP requests to `php:9000`.

## Environment / overrides

Drop `.ini` files in `/etc/php/conf.d` by mounting them at runtime or baking them into a derived image.

```bash
# Example: raise upload limits
echo "upload_max_filesize=50M" > ./90-upload.ini

docker run --rm \
  -v "$PWD/90-upload.ini:/etc/php/conf.d/90-upload.ini:ro" \
  liquidrazor/php:fpm
```

FPM pool defaults excerpt:

```ini
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

Razor-PHP publishes images for the latest stable PHP major release and the immediately previous stable PHP major release.

All images are compiled against the latest stable Debian major release.

Tag behavior:

* **Global aliases** such as `fpm` and `cli` track the latest stable supported PHP line.
* **Rolling major tags** such as `<major>-fpm` track the latest patch release for that supported major line.
* **Immutable patch tags** such as `<version>-fpm` never change once published.

Rebuild behavior:

* Images are rebuilt regularly to receive base distribution security updates.
* PHP patch updates are published under rolling major tags and exact immutable patch tags.
* Consumers that need deterministic production releases should pin immutable patch tags.

## Troubleshooting

Check loaded extensions:

```bash
docker run --rm liquidrazor/php:cli php -m
```

Inspect the multi-architecture manifest:

```bash
docker buildx imagetools inspect liquidrazor/php:fpm
```

Check FPM connectivity:

```bash
nc -vz localhost 9000
```

Run the PHP version command:

```bash
docker run --rm liquidrazor/php:cli php -v
```

## Mirrors

* Docker Hub: `docker.io/liquidrazor/php:<tag>`
* GHCR: `ghcr.io/liquidrazor/php:<tag>`
