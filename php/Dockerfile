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
    
    && if [ $(php -r "echo sprintf('%.1f',PHP_VERSION);") = 7.4 ]; then \
            docker-php-ext-configure gd --with-jpeg=/usr/include/ --with-freetype=/usr/include/; \
        else \
            docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ --with-png-dir=/usr/include/; \
        fi \
    && docker-php-ext-install -j$(nproc) gd \
    && if [ $(php -r "echo sprintf('%.1f',PHP_VERSION);") = 7.3 ] || [ $(php -r "echo sprintf('%.1f',PHP_VERSION);") = 7.4 ]; then \
            docker-php-ext-configure zip; \
        else \
            docker-php-ext-configure zip --with-libzip; \
        fi \
    && docker-php-ext-install zip sockets bcmath bz2 opcache \
    && COPY ./opcache.ini /usr/local/etc/php/conf.d/00_opcache.ini \

    && pecl install amqp redis yaml \
    && echo "extension=amqp" > /usr/local/etc/php/conf.d/00_amqp.ini \
    && echo "extension=redis" > /usr/local/etc/php/conf.d/00_redis.ini \
    && echo "extension=yaml" > /usr/local/etc/php/conf.d/00_yaml.ini \

    && echo "upload_max_filesize=128M" > /usr/local/etc/php/conf.d/00_default.ini \
    && echo "post_max_size=128M" >> /usr/local/etc/php/conf.d/00_default.ini \
    && echo "memory_limit=1G" >> /usr/local/etc/php/conf.d/00_default.ini \

    && apk del --purge *-dev \
    && rm -rf /var/cache/apk/* /tmp/* /usr/share/man /usr/share/php7 \
    
    && php --ini \
    && php -v \
    && php -m \
    && echo 'Build Completed'