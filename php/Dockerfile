ARG PHP_VERSION
ARG PHP_TYPE

FROM php:${PHP_VERSION}-${PHP_TYPE}-alpine

LABEL maintainer="ogg"

ENV DEBIAN_FRONTEND noninteractive

RUN set -ex \
    # && sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories \
    && apk update \
    && apk add --no-cache \
        m4 autoconf gcc g++ make linux-headers \
        git vim \
        bzip2 zip unzip freetype libjpeg-turbo libwebp jpeg libpng rabbitmq-c yaml \
        bzip2-dev libzip-dev freetype-dev libjpeg-turbo-dev libwebp-dev jpeg-dev libpng-dev rabbitmq-c-dev yaml-dev \
    
    && if [[ "['7.4', '8.0', '8.1', '8.2']" == *"$(php -r "echo sprintf('%.1f',PHP_VERSION);")"* ]]; then \
            docker-php-ext-configure gd --with-freetype --with-jpeg --with-webp; \
        else \
            docker-php-ext-configure gd --with-freetype-dir=/usr/lib/ --with-jpeg-dir=/usr/lib/ --with-png-dir=/usr/lib/; \
        fi \
    && if [[ "['7.3', '7.4', '8.0', '8.1', '8.2']" == *"$(php -r "echo sprintf('%.1f',PHP_VERSION);")"* ]]; then \
            docker-php-ext-configure zip; \
        else \
            docker-php-ext-configure zip --with-libzip; \
        fi \
    && docker-php-ext-install -j$(nproc) gd zip sockets bcmath bz2 opcache pcntl pdo_mysql \

    && php -r "copy('https://install.phpcomposer.com/installer', 'composer-setup.php');" \
    && php composer-setup.php \
    && mv composer.phar /usr/local/bin/composer \

    && pecl install amqp redis yaml  \
    && echo "extension=amqp.so" > /usr/local/etc/php/conf.d/00_amqp.ini \
    && echo "extension=redis.so" > /usr/local/etc/php/conf.d/00_redis.ini \
    && echo "extension=yaml.so" > /usr/local/etc/php/conf.d/00_yaml.ini \

    && echo "upload_max_filesize=128M" > /usr/local/etc/php/conf.d/00_default.ini \
    && echo "post_max_size=128M" >> /usr/local/etc/php/conf.d/00_default.ini \
    && echo "memory_limit=1G" >> /usr/local/etc/php/conf.d/00_default.ini \

    && apk del --purge *-dev m4 autoconf gcc g++ make \
    && apk add --no-cache libzip-dev \
    && rm -rf /var/cache/apk/* /tmp/* /usr/share/man /usr/share/php7 ~/.pearrc ./composer-setup.php \
    
    && php --ini \
    && php -v \
    && php -m \
    && echo 'Build Completed'