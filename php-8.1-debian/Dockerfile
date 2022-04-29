ARG PHP_VERSION=8.1

FROM docker.io/php:${PHP_VERSION}-fpm-bullseye

ARG APCU_VERSION=5.1.21

ENV DEBIAN_FRONTEND=noninteractive

# Essential tools
RUN \
    apt-get update && \
    apt-get install -fy openbsd-inetd openssh-server openssh-sftp-server rsync mariadb-client && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Composer
RUN \
    cd /tmp && \
    curl -o composer-setup.php https://getcomposer.org/installer && \
    php composer-setup.php && \
    mv composer.phar /usr/local/bin/composer && \
    rm composer-setup.php

# ImageMagick
# https://github.com/docker-library/php/issues/1033
RUN \
    apt-get update && \
    apt-get install -fy libgomp1 imagemagick libmagick++-dev && \
    mkdir -p /usr/src/php/ext/imagick && \
    curl -fsSL https://pecl.php.net/get/imagick | tar xvz -C "/usr/src/php/ext/imagick" --strip 1 && \
    docker-php-ext-install imagick && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# APCU
RUN \
    apt-get update && \
    apt-get install -fy autoconf gcc make g++ && \
    pecl install apcu-${APCU_VERSION} && \
    echo "extension=apcu.so" >> /usr/local/etc/php/conf.d/apcu.ini && \
    echo "apc.enable=1"      >> /usr/local/etc/php/conf.d/apcu.ini && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Other PHP Extensions
RUN \
    apt-get update && \
    apt-get install -fy libzip-dev libcurl4-openssl-dev libpng-dev libicu-dev libgmp-dev && \
    docker-php-ext-install pdo_mysql mysqli zip curl gd intl gmp fileinfo exif opcache && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

RUN touch /.php-ready
