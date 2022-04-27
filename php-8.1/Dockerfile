ARG PHP_VERSION=8.1

FROM docker.io/php:${PHP_VERSION}-fpm-alpine

RUN \
    touch /.php-ready && \
    docker-php-ext-install pdo_mysql && \
    docker-php-ext-install mysqli
