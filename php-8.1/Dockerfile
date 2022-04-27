ARG PHP_VERSION=8.1

FROM docker.io/php:${PHP_VERSION}-fpm-alpine

# Essential tools
RUN \
    apk update && \
    apk add busybox-extras openssh-server openssh-sftp-server rsync && \
    rm -rf /var/cache/apk/*

# Composer
RUN \
    cd /tmp && \
    wget -O composer-setup.php https://getcomposer.org/installer && \
    php composer-setup.php && \
    mv composer.phar /usr/local/bin/composer && \
    rm composer-setup.php

# PHP Extensions
RUN \
    apk update && \
    apk add libzip-dev curl-dev libpng-dev icu-dev gmp-dev && \
    docker-php-ext-install pdo_mysql mysqli zip curl gd intl gmp fileinfo && \
    rm -rf /var/cache/apk/* && \
    touch /.php-ready
