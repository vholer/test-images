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

# Install all other services
RUN \
    apk update && \
    apk add runit apache2 apache2-ctl apache2-proxy && \
    mkdir -p /etc/service/enabled && \
    cd /etc/service/enabled && \
    mkdir -p httpd && \
        echo '#!/bin/sh'                     >httpd/run && \
        echo 'set -e'                       >>httpd/run && \
        echo 'ln -s -f /mnt/cfg/httpd-olin.conf /etc/apache2/httpd.conf' >>httpd/run && \
        echo 'exec httpd -DFOREGROUND "$@"' >>httpd/run && \
        chmod 755 httpd/run && \
    mkdir -p php-fpm && \
        echo '#!/bin/sh'                     >php-fpm/run && \
        echo 'set -e'                       >>php-fpm/run && \
        echo 'ln -s -f /mnt/cfg/php-fpm-www.conf /usr/local/etc/php-fpm.d/www.conf' >> php-fpm/run && \
        echo 'exec php-fpm'                 >>php-fpm/run && \
        chmod 755 php-fpm/run && \
    mkdir -p sshd && \
        echo '#!/bin/sh'                     >sshd/run && \
        echo 'set -e'                       >>sshd/run && \
        echo 'source /etc/init.d/sshd'      >>sshd/run && \
        echo 'generate_host_keys'           >>sshd/run && \
        echo 'echo "PermitEmptyPasswords yes" >>/etc/ssh/sshd_config' >>sshd/run && \
        echo 'echo "AllowUsers user www-data" >>/etc/ssh/sshd_config' >>sshd/run && \
        echo 'passwd -u www-data || :'      >>sshd/run && \
        echo "sed -i -e 's,\(www-data.*:\)/sbin/nologin,\1/bin/ash,' /etc/passwd || :" >>sshd/run && \
        echo 'exec /usr/sbin/sshd -D -e -p 22' >>sshd/run && \
        chmod 755 sshd/run && \
    rm -rf /var/cache/apk/*

RUN touch /.php-ready

ENTRYPOINT ["/sbin/runsvdir", "-P", "/etc/service/enabled"]
