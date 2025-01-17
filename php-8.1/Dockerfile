ARG PHP_VERSION=8.1

FROM docker.io/php:${PHP_VERSION}-fpm-alpine

ARG APCU_VERSION=5.1.21

# Essential tools
RUN \
    apk update && \
    apk add busybox-extras openssh-server openssh-sftp-server rsync mysql-client && \
    rm -rf /var/cache/apk/*

# Composer
RUN \
    cd /tmp && \
    wget -O composer-setup.php https://getcomposer.org/installer && \
    php composer-setup.php && \
    mv composer.phar /usr/local/bin/composer && \
    rm composer-setup.php

# ImageMagick
# https://github.com/docker-library/php/issues/1033
RUN \
    apk update && \
    apk add libgomp imagemagick imagemagick-dev && \
    mkdir -p /usr/src/php/ext/imagick && \
    curl -fsSL https://pecl.php.net/get/imagick | tar xvz -C "/usr/src/php/ext/imagick" --strip 1 && \
    docker-php-ext-install imagick && \
    rm -rf /var/cache/apk/*

# APCU
RUN \
    apk update && \
    apk add autoconf gcc make g++ && \
    pecl install apcu-${APCU_VERSION} && \
    echo "extension=apcu.so" >> /usr/local/etc/php/conf.d/apcu.ini && \
    echo "apc.enable=1"      >> /usr/local/etc/php/conf.d/apcu.ini && \
    rm -rf /var/cache/apk/*

# Other PHP Extensions
RUN \
    apk update && \
    apk add libzip-dev curl-dev libpng-dev icu-dev gmp-dev && \
    docker-php-ext-install pdo_mysql mysqli zip curl gd intl gmp fileinfo exif opcache && \
    rm -rf /var/cache/apk/*

RUN touch /.php-ready
