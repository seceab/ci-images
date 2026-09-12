# syntax=docker/dockerfile:1.7

FROM node:24-trixie AS node
FROM composer:2 AS composer

FROM php:8.5-cli-trixie

LABEL org.opencontainers.image.source="https://github.com/seceab/ci-images"
LABEL org.opencontainers.image.description="Shared Laravel CI runtime for seceab"
LABEL org.opencontainers.image.vendor="SECE"

ENV DEBIAN_FRONTEND=noninteractive \
    COMPOSER_ALLOW_SUPERUSER=1 \
    COMPOSER_HOME=/tmp/composer \
    COMPOSER_PROCESS_TIMEOUT=0

RUN set -eux; \
    apt-get update; \
    apt-get install -y --no-install-recommends \
        $PHPIZE_DEPS \
        ca-certificates \
        curl \
        wget \
        git \
        git-lfs \
        openssh-client \
        jq \
        rsync \
        unzip \
        zip \
        xz-utils \
        procps \
        sqlite3 \
        default-mysql-client \
        postgresql-client \
        redis-tools \
        imagemagick \
        libcurl4-openssl-dev \
        libicu-dev \
        libonig-dev \
        libxml2-dev \
        libzip-dev \
        libsqlite3-dev \
        libpq-dev \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
        libwebp-dev \
        libmagickwand-dev; \
    docker-php-ext-configure gd \
        --with-freetype \
        --with-jpeg \
        --with-webp; \
    docker-php-ext-install -j"$(nproc)" \
        bcmath \
        exif \
        gd \
        intl \
        mysqli \
        pcntl \
        pdo_mysql \
        pdo_pgsql \
        sockets \
        zip; \
    yes '' | pecl install imagick; \
    yes '' | pecl install redis; \
    docker-php-ext-enable imagick redis; \
    git lfs install --system; \
    git config --system --add safe.directory '*'; \
    rm -rf /var/lib/apt/lists/* /tmp/pear

COPY --from=node /usr/local/bin/node /usr/local/bin/node
COPY --from=node /usr/local/lib/node_modules /usr/local/lib/node_modules

RUN set -eux; \
    ln -sf /usr/local/lib/node_modules/npm/bin/npm-cli.js /usr/local/bin/npm; \
    ln -sf /usr/local/lib/node_modules/npm/bin/npx-cli.js /usr/local/bin/npx; \
    node --version; \
    npm --version

COPY --from=composer /usr/bin/composer /usr/local/bin/composer

RUN set -eux; \
    printf '%s\n' \
        'memory_limit=-1' \
        'max_execution_time=0' \
        'date.timezone=UTC' \
        > /usr/local/etc/php/conf.d/99-ci.ini; \
    php --version; \
    composer --version; \
    php -m

WORKDIR /workspace

CMD ["bash"]