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

## 🚀 Quick start  
```bash
# Example: run PHP CLI
docker run --rm ghcr.io/liquidrazor/razor-php:8.3 php -v
```

# Example: use in your Dockerfile
```bash
FROM ghcr.io/liquidrazor/razor-php:8.3
COPY . /app
WORKDIR /app
CMD ["php", "your-script.php"]
```

## 🧪 What’s inside?

✅ Core PHP modules (intl, mbstring, pdo, opcache, etc.)

🛠️ Commonly used extras (gd, zip, pcntl, sockets, bcmath, etc.)

🕸️ Optional “usual suspects” for real apps (amqp, redis, xdebug ready, …)

🧼 Images are stripped of junk to keep them lean.

(Exact module list: see Dockerfile)

## 🗡️ Why Razor?

Because standard PHP images are either:
- Too fat 🐋
- Missing stuff you’ll always end up needing 😤
- Or both.

Razor-PHP is sharpened to cut the crap.

## 📦 Tags

- 8.3 → PHP 8.3 stable build
- 8.4 → PHP 8.4 stable build

cli, fpm variants available

🏴 Disclaimer

This is not “official” PHP. It’s sharper, meaner, and built for devs who don’t want to fight their Dockerfile every damn day.
