ARG PHP_VERSION
ARG PHP_TYPE

FROM php:${PHP_VERSION}-${PHP_TYPE}-alpine

LABEL maintainer="ogg"

ENV DEBIAN_FRONTEND noninteractive

RUN set -ex \
    # && sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories \
    && apk update \
    && apk add --no-cache m4 autoconf gcc g++ make composer git vim \
        bzip2-dev freetype-dev libjpeg-turbo-dev jpeg-dev libpng-dev libzip-dev rabbitmq-c rabbitmq-c-dev \
    
    && docker-php-ext-install bz2 \
    && if [ $(php -r "echo sprintf('%.1f',PHP_VERSION);") = 7.4 ]; then \
            docker-php-ext-configure gd --with-jpeg=/usr/include/ --with-freetype=/usr/include/; \
        else \
            docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ --with-png-dir=/usr/include/; \
        fi \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-install bcmath \
    && if [ $(php -r "echo sprintf('%.1f',PHP_VERSION);") = 7.3 ] || [ $(php -r "echo sprintf('%.1f',PHP_VERSION);") = 7.4 ]; then \
            docker-php-ext-configure zip; \
        else \
            docker-php-ext-configure zip --with-libzip; \
        fi \
    && docker-php-ext-install zip \
    && pecl install amqp \
    && docker-php-ext-enable amqp \
    && docker-php-ext-install sockets \
    && if [ $(php -r "echo sprintf('%.1f',PHP_VERSION);") = 7.1 ]; then \
        pecl install swoole-2.2.0 --enable-openssl --enable-swoole-json --enable-swoole-curl --enable-cares; \
    else \
        pecl install swoole --enable-openssl --enable-swoole-json --enable-swoole-curl --enable-cares; \
    fi \
    && docker-php-ext-enable swoole \
    && pecl install redis \
    && docker-php-ext-enable redis \

    && apk del --purge *-dev \
    && rm -rf /var/cache/apk/* /tmp/* /usr/share/man /usr/share/php7 \
    
    && php --ini \
    && php -v \
    && php -m \
    && echo 'Build Completed'