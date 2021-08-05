ARG PHP_VERSION
ARG PHP_TYPE

FROM php:${PHP_VERSION}-${PHP_TYPE}-alpine

LABEL maintainer="ogg"

ENV DEBIAN_FRONTEND noninteractive

RUN set -ex \
    # && sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories \
    && apk update \
    && apk add --no-cache \
        m4 autoconf gcc g++ make \
        composer git vim \
        bzip2 zip unzip freetype libjpeg-turbo libwebp jpeg libpng rabbitmq-c yaml \
        bzip2-dev libzip-dev freetype-dev libjpeg-turbo-dev libwebp-dev jpeg-dev libpng-dev rabbitmq-c-dev yaml-dev \
    
    && if [ $(php -r "echo sprintf('%.1f',PHP_VERSION);") = 7.4 ]; then \
            docker-php-ext-configure gd --with-freetype --with-jpeg --with-webp; \
        else \
            docker-php-ext-configure gd --with-freetype-dir=/usr/lib/ --with-jpeg-dir=/usr/lib/ --with-png-dir=/usr/lib/; \
        fi \
    && if [ $(php -r "echo sprintf('%.1f',PHP_VERSION);") = 7.3 ] || [ $(php -r "echo sprintf('%.1f',PHP_VERSION);") = 7.4 ]; then \
            docker-php-ext-configure zip; \
        else \
            docker-php-ext-configure zip --with-libzip; \
        fi \
    && docker-php-ext-install -j$(nproc) gd zip sockets bcmath bz2 opcache pcntl \

    && pecl install amqp redis yaml  \
    && echo "extension=amqp.so" > /usr/local/etc/php/conf.d/00_amqp.ini \
    && echo "extension=redis.so" > /usr/local/etc/php/conf.d/00_redis.ini \
    && echo "extension=yaml.so" > /usr/local/etc/php/conf.d/00_yaml.ini \

    && echo "upload_max_filesize=128M" > /usr/local/etc/php/conf.d/00_default.ini \
    && echo "post_max_size=128M" >> /usr/local/etc/php/conf.d/00_default.ini \
    && echo "memory_limit=1G" >> /usr/local/etc/php/conf.d/00_default.ini \

    && apk del --purge *-dev m4 autoconf gcc g++ make \
    && rm -rf /var/cache/apk/* /tmp/* /usr/share/man /usr/share/php7 ~/.pearrc \
    
    && php --ini \
    && php -v \
    && php -m \
    && echo 'Build Completed'