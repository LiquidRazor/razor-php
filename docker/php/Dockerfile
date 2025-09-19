# docker/php/8.4/Dockerfile
# ===== build base stage =====
FROM debian:bookworm-slim AS build-base
ARG PHP_VERSION=8.4.11
ARG CCACHE_PHP_MAJOR

# Build toolchain + headers for the extensions you enable
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    git \
    curl \
    ca-certificates \
    libcurl4-openssl-dev \
    libedit-dev \
    libsodium-dev \
    libssl-dev \
    zlib1g-dev \
    libonig-dev \
    libxml2-dev \
    libargon2-0-dev \
    libc6-dev \
    libyaml-dev \
    libpng-dev \
    libwebp-dev \
    libjpeg-dev \
    libfreetype6-dev \
    libgmp-dev \
    libzip-dev \
    libpq-dev \
    libc-client2007e-dev \
    libgssapi-krb5-2 \
    libkrb5-dev \
    librabbitmq-dev \
    dirmngr \
    autoconf \
    automake \
    autotools-dev \
    libtool \
    build-essential \
    xz-utils \
    dpkg-dev \
    dpkg \
    file \
    g++ \
    gcc \
    libc-dev \
    make \
    pkgconf \
    re2c \
    ccache \
  && rm -rf /var/lib/apt/lists/*

ENV PATH="/usr/lib/ccache:${PATH}" CCACHE_COMPRESS=1 CCACHE_MAXSIZE=2G

WORKDIR /usr/local/src
RUN curl -fsSL "https://www.php.net/distributions/php-${PHP_VERSION}.tar.xz" -o php.tar.xz \
 && tar -xf php.tar.xz && mv php-${PHP_VERSION} php && rm php.tar.xz

# ===== build fpm stage =====
FROM build-base AS build-fpm
ARG PHP_VERSION=8.4.11
ARG CCACHE_PHP_MAJOR
WORKDIR /usr/local/src/php
ENV CCACHE_DIR=/ccache/$CCACHE_PHP_MAJOR/fpm
RUN --mount=type=cache,target=/ccache/$CCACHE_PHP_MAJOR/fpm ./configure \
  --prefix=/opt/php \
  --with-config-file-path=/etc/php \
  --with-config-file-scan-dir=/etc/php/conf.d \
  --disable-all \
  --enable-option-checking=fatal \
  --enable-cli \
  --enable-fpm \
  --enable-bcmath \
  --enable-ctype \
  --enable-dom \
  --enable-fileinfo \
  --enable-ftp \
  --enable-gd \
  --enable-intl \
  --enable-filter \
  --enable-mbstring \
  --enable-opcache \
  --enable-opcache-jit \
  --enable-pcntl \
  --enable-phar \
  --enable-pdo \
  --enable-posix \
  --enable-session \
  --enable-simplexml \
  --enable-sockets \
  --enable-tokenizer \
  --enable-xml \
  --enable-xmlreader \
  --enable-xmlwriter \
  --with-curl \
  --with-freetype \
  --with-gmp \
  --with-iconv \
  --with-jpeg \
  --with-libedit \
  --with-libxml \
  --with-mhash \
  --with-openssl \
  --with-password-argon2 \
  --with-pgsql \
  --with-pdo-pgsql \
  --with-sodium=shared \
  --with-zip \
  --with-zlib \
  --with-webp \
 && make -j "$(nproc)" && make install

# Package outputs for runtime
RUN mkdir -p /out/conf.d /out/etc/php-fpm.d
RUN printf "[global]\nerror_log=/proc/self/fd/2\ndaemonize=no\n" > /out/php-fpm.conf
RUN printf "[www]\nuser=app\ngroup=app\nlisten=0.0.0.0:9000\npm=ondemand\npm.max_children=10\npm.process_idle_timeout=10s\npm.status_path=/status\nping.path=/ping\nclear_env=no\ncatch_workers_output=yes\n" > /out/etc/php-fpm.d/www.conf

# ===== build cli stage =====
FROM build-base AS build-cli
ARG PHP_VERSION=8.4.11
ARG CCACHE_PHP_MAJOR
WORKDIR /usr/local/src/php
ENV CCACHE_DIR=/ccache/$CCACHE_PHP_MAJOR/cli
RUN --mount=type=cache,target=/ccache/$CCACHE_PHP_MAJOR/cli ./configure \
  --prefix=/opt/php \
  --with-config-file-path=/etc/php \
  --with-config-file-scan-dir=/etc/php/conf.d \
  --disable-all \
  --enable-option-checking=fatal \
  --disable-cgi \
  --enable-cli \
  --enable-bcmath \
  --enable-ctype \
  --enable-dom \
  --enable-fileinfo \
  --enable-ftp \
  --enable-gd \
  --enable-intl \
  --enable-filter \
  --enable-mbstring \
  --enable-opcache \
  --enable-opcache-jit \
  --enable-pcntl \
  --enable-phar \
  --enable-pdo \
  --enable-posix \
  --enable-session \
  --enable-simplexml \
  --enable-sysvmsg \
  --enable-sysvsem \
  --enable-sysvshm \
  --enable-sockets \
  --enable-tokenizer \
  --enable-xml \
  --enable-xmlreader \
  --enable-xmlwriter \
  --with-curl \
  --with-freetype \
  --with-gmp \
  --with-iconv \
  --with-jpeg \
  --with-libedit \
  --with-libxml \
  --with-mhash \
  --with-openssl \
  --with-password-argon2 \
  --with-pcre-jit \
  --with-pgsql \
  --with-pdo-pgsql \
  --with-sodium=shared \
  --with-zip \
  --with-zlib \
  --with-webp \
 && make -j "$(nproc)" && make install

# ===== build modules once (reuse for FPM + CLI) =====
FROM build-cli AS build-modules
ARG CCACHE_PHP_MAJOR
ARG PHP_VERSION=8.4.11
WORKDIR /usr/local/src/modules

# Clone once
RUN git clone -q https://github.com/igbinary/igbinary.git igbinary \
 && git clone -q https://github.com/phpredis/phpredis.git --branch 6.1.0 phpredis \
 && git clone -q https://github.com/krakjoe/apcu.git apcu \
 && git clone -q https://github.com/php/pecl-file_formats-yaml.git yaml

# Build & install modules against /opt/php
WORKDIR /usr/local/src/modules/igbinary
RUN /opt/php/bin/phpize \
 && ./configure CFLAGS="-O2 -g" --enable-igbinary --with-php-config=/opt/php/bin/php-config \
 && make -j "$(nproc)" && make install

WORKDIR /usr/local/src/modules/phpredis
RUN /opt/php/bin/phpize \
 && ./configure --enable-redis --enable-redis-igbinary --with-php-config=/opt/php/bin/php-config \
 && make -j "$(nproc)" && make install

WORKDIR /usr/local/src/modules/apcu
RUN /opt/php/bin/phpize \
 && ./configure --with-php-config=/opt/php/bin/php-config \
 && make -j "$(nproc)" && make install

WORKDIR /usr/local/src/modules/yaml
RUN /opt/php/bin/phpize \
 && ./configure --with-yaml --with-php-config=/opt/php/bin/php-config \
 && make -j "$(nproc)" && make install

# Collect built artifacts for reuse (+ shared ini set)
RUN set -eux; \
    EXT_DIR="$(/opt/php/bin/php -r 'echo ini_get("extension_dir");')"; \
    mkdir -p /mods/ext /mods/conf.d; \
    cp -a "$EXT_DIR"/apcu.so "$EXT_DIR"/igbinary.so "$EXT_DIR"/redis.so "$EXT_DIR"/yaml.so /mods/ext/; \
    printf "expose_php=0\nmemory_limit=256M\nmax_execution_time=120\n" > /mods/conf.d/10-base.ini; \
    printf "opcache.enable=1\nopcache.jit=tracing\nopcache.jit_buffer_size=100M\n" > /mods/conf.d/20-opcache.ini; \
    echo "extension = apcu.so"     > /mods/conf.d/15-apcu.ini; \
    echo "extension = yaml.so"     > /mods/conf.d/15-yaml.ini; \
    echo "extension = igbinary.so" > /mods/conf.d/15-igbinary.ini; \
    echo "extension = redis.so"    > /mods/conf.d/15-redis.ini

# ===== runtime base =====
FROM debian:bookworm-slim AS runtime
RUN apt-get update && apt-get install -y --no-install-recommends \
  libcurl4 \
  libonig5 \
  libedit2 \
  libsodium23 \
  libssl3 \
  libargon2-1 \
  libpq5 \
  librabbitmq4 \
  libzip4 \
  libxml2 \
  libpng16-16 \
  libjpeg62-turbo \
  libwebp7 \
  libfreetype6 \
  libyaml-0-2 \
  ca-certificates \
  tini \
 && rm -rf /var/lib/apt/lists/*
# non-root user matches pool
RUN groupadd -r app && useradd -r -g app -s /usr/sbin/nologin app

RUN mkdir -p /etc/php/conf.d

ENV PATH="/opt/php/bin:/opt/php/sbin:${PATH}"
ENV PHP_INI_DIR=/etc/php

FROM build-fpm AS strip-fpm
ARG CCACHE_PHP_MAJOR
RUN set -eux; \
  find /opt/php -type f -name '*.a'  -delete; \
  find /opt/php -type f -name '*.la' -delete; \
  rm -rf /opt/php/include /opt/php/php/man /opt/php/lib/php/build \
         /opt/php/lib/php/.channels /opt/php/lib/php/.depdb* \
         /opt/php/lib/php/.filemap /opt/php/lib/php/.lock || true; \
  apt-get update && apt-get install -y --no-install-recommends binutils && rm -rf /var/lib/apt/lists/*; \
  find /opt/php -type f -perm -111 -exec strip --strip-unneeded {} + || true; \
  find /opt/php -type f -name '*.so' -exec strip --strip-unneeded {} + || true


# ===== target: fpm =====
FROM runtime AS fpm
ARG PHP_VERSION=8.4.11
ARG CCACHE_PHP_MAJOR

# PHP from FPM build
COPY --from=strip-fpm /opt/php /opt/php
COPY --from=build-fpm /out/php-fpm.conf /etc/php-fpm.conf
COPY --from=build-fpm /out/etc/php-fpm.d /etc/php-fpm.d

# Add only fpm-specific runtime dep
RUN apt-get update && apt-get install -y --no-install-recommends libfcgi-bin && rm -rf /var/lib/apt/lists/*

# Bring in modules (so + ini) built once
COPY --from=build-modules /mods /mods

RUN set -eux; \
    EXT_DIR="$(/opt/php/bin/php -r 'echo ini_get("extension_dir");')"; \
    mkdir -p "$EXT_DIR"; \
    cp -a /mods/ext/*.so "$EXT_DIR"/; \
    cp -a /mods/conf.d/*.ini /etc/php/conf.d/; \
    rm -rf /mods


WORKDIR /app
USER app
EXPOSE 9000
HEALTHCHECK --interval=10s --timeout=3s --retries=3 CMD \
  SCRIPT_NAME=/ping SCRIPT_FILENAME=/ping REQUEST_METHOD=GET \
  cgi-fcgi -bind -connect 127.0.0.1:9000 || exit 1
ENTRYPOINT ["/usr/bin/tini","--"]
CMD ["php-fpm","--nodaemonize","--fpm-config","/etc/php-fpm.conf"]


FROM build-cli AS strip-cli
ARG CCACHE_PHP_MAJOR
RUN set -eux; \
  find /opt/php -type f -name '*.a'  -delete; \
  find /opt/php -type f -name '*.la' -delete; \
  rm -rf /opt/php/include /opt/php/php/man /opt/php/lib/php/build \
         /opt/php/lib/php/.channels /opt/php/lib/php/.depdb* \
         /opt/php/lib/php/.filemap /opt/php/lib/php/.lock || true; \
  apt-get update && apt-get install -y --no-install-recommends binutils && rm -rf /var/lib/apt/lists/*; \
  find /opt/php -type f -perm -111 -exec strip --strip-unneeded {} + || true; \
  find /opt/php -type f -name '*.so' -exec strip --strip-unneeded {} + || true

# ===== target: cli =====
FROM runtime AS cli
ARG PHP_VERSION=8.4.11
ARG CCACHE_PHP_MAJOR

# PHP from CLI build
COPY --from=strip-cli /opt/php /opt/php

# Bring in modules (so + ini) built once
COPY --from=build-modules /mods /mods

RUN set -eux; \
    EXT_DIR="$(/opt/php/bin/php -r 'echo ini_get("extension_dir");')"; \
    mkdir -p "$EXT_DIR"; \
    cp -a /mods/ext/*.so "$EXT_DIR"/; \
    cp -a /mods/conf.d/*.ini /etc/php/conf.d/; \
    rm -rf /mods


WORKDIR /app
USER app
ENTRYPOINT ["/usr/bin/tini","--"]

# ===== target: fpm-dev (xdebug/pcov/tools) =====
FROM fpm AS fpm-dev
ARG PHP_VERSION=8.4.11
ARG CCACHE_PHP_MAJOR

USER root
RUN apt-get update && apt-get install -y --no-install-recommends git curl unzip vim && rm -rf /var/lib/apt/lists/*

COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

RUN /opt/php/bin/pecl install xdebug pcov || true && \
    echo "zend_extension=xdebug.so" > /etc/php/conf.d/80-xdebug.ini && \
    echo "xdebug.mode=debug,develop" >> /etc/php/conf.d/80-xdebug.ini && \
    echo "xdebug.start_with_request=trigger" >> /etc/php/conf.d/80-xdebug.ini && \
    echo "xdebug.client_host=host.docker.internal" >> /etc/php/conf.d/80-xdebug.ini && \
    echo "opcache.validate_timestamps=1" > /etc/php/conf.d/95-dev.ini && \
    echo "display_errors=1" >> /etc/php/conf.d/95-dev.ini
USER app

FROM cli AS cli-dev
ARG CCACHE_PHP_MAJOR
USER root
RUN apt-get update && apt-get install -y --no-install-recommends git curl unzip vim && rm -rf /var/lib/apt/lists/*

COPY --from=composer:latest /usr/bin/composer /usr/bin/composer
USER app