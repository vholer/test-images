ARG PHP_VERSION=8.1

FROM docker.io/php:${PHP_VERSION}-fpm-alpine

# Composer
RUN \
    cd /tmp && \
    wget -O composer-setup.php https://getcomposer.org/installer && \
    php composer-setup.php && \
    mv composer.phar /usr/local/bin/composer && \
    rm composer-setup.php

# PHP Extensions
RUN \
    docker-php-ext-install pdo_mysql && \
    docker-php-ext-install mysqli && \
    touch /.php-ready
