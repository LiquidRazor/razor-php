# The base images (fully qualified, e.g. ghcr.io/ORG/php:*.*.*-cli)
ARG BASE_IMAGE

FROM ${BASE_IMAGE} AS cli-dev

USER root
RUN apt-get update && apt-get install -y --no-install-recommends git curl unzip vim && rm -rf /var/lib/apt/lists/*

COPY --from=composer:latest /usr/bin/composer /usr/bin/composer
USER app