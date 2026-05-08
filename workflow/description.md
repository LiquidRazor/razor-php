# Project Description

Razor-PHP is a Docker image repository for custom PHP CLI and PHP-FPM images built from PHP source.

The project provides production and development image variants for the latest stable PHP major release and the immediately previous stable PHP major release.

All images are compiled against the latest stable Debian major release.

The repository is responsible for:

- defining Docker build contexts for PHP CLI and PHP-FPM images;
- defining production-ready base images;
- defining development images with additional development tooling;
- building PHP from source instead of relying on upstream PHP Docker images;
- enabling a consistent set of PHP core and PECL extensions;
- publishing rolling major tags, immutable patch tags, and latest-stable aliases;
- supporting multi-architecture image builds for `linux/amd64` and `linux/arm64`;
- publishing images to Docker Hub and GitHub Container Registry;
- keeping image versioning policy independent from hardcoded PHP or Debian versions wherever possible.

The repository structure is intentionally small and image-focused.

Dockerfiles live under:

```text
docker/php/<runtime>/<variant>/Dockerfile
```

Where:

- `<runtime>` is either `cli` or `fpm`;
- `<variant>` is either `base` or `development`.

GitHub Actions workflows live under:

```text
.github/workflows/
```

Agentic configuration files live under:

```text
workflow/
```

This repository is not a PHP application, PHP framework, library package, or Composer package.

It should not introduce application source directories such as `src/`, `lib/`, `include/`, or `app/` unless the repository purpose changes explicitly.

The main implementation surface is Docker build logic, image metadata, workflow automation, and documentation.

