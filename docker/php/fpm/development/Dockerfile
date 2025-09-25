# The base images (fully qualified, e.g. ghcr.io/ORG/php:*.*.*-fpm)
ARG BASE_IMAGE

FROM ${BASE_IMAGE}

USER root
RUN apt-get update && apt-get install -y --no-install-recommends git curl unzip vim && rm -rf /var/lib/apt/lists/*

RUN set -eux; \
    echo "zend_extension=xdebug.so" > /etc/php/conf.d/80-xdebug.ini && \
    echo "xdebug.mode=debug,develop" >> /etc/php/conf.d/80-xdebug.ini && \
    echo "xdebug.start_with_request=trigger" >> /etc/php/conf.d/80-xdebug.ini && \
    echo "xdebug.client_host=host.docker.internal" >> /etc/php/conf.d/80-xdebug.ini && \
    echo "opcache.validate_timestamps=1" > /etc/php/conf.d/95-dev.ini && \
    echo "display_errors=1" >> /etc/php/conf.d/95-dev.ini;

COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

USER app