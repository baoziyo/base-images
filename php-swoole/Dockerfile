ARG PHP_VERSION
ARG PHP_TYPE
ARG SWOOLE_VERSION

FROM baoziyoo/base-images:latest-php-${PHP_TYPE}-${PHP_VERSION}
LABEL maintainer="ogg"

ENV DEBIAN_FRONTEND noninteractive

RUN set -ex \
    # && sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories \
    && apk update \
    && if [ $(php -r "echo sprintf('%.1f',PHP_VERSION);") = 7.1 ]; then \
        pecl install swoole-2.2.0 --enable-openssl --enable-swoole-json --enable-swoole-curl --enable-cares; \
    else \
        pecl install swoole --enable-openssl --enable-swoole-json --enable-swoole-curl --enable-cares; \
    fi \
    && docker-php-ext-enable swoole \
    && echo "swoole.use_shortname='Off'" > /usr/local/etc/php/conf.d/00_swoole.ini \
    && if [ ${PHP_TYPE} = cli ]; then \
        echo "opcache.enable_cli='On'" > /usr/local/etc/php/conf.d/00_opencache.ini; \
    fi \

    && apk del --purge *-dev \
    && rm -rf /var/cache/apk/* /tmp/* /usr/share/man /usr/share/php7 \
    
    && php --ini \
    && php -v \
    && php -m \
    && echo 'Build Completed'