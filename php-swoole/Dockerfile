ARG PHP_VERSION
ARG PHP_TYPE

FROM baoziyoo/base-images:php-${PHP_TYPE}-${PHP_VERSION}
LABEL maintainer="ogg"

ARG SWOOLE_VERSION
ENV SWOOLE_VERSION ${SWOOLE_VERSION}
ENV DEBIAN_FRONTEND noninteractive

RUN set -ex \
    # && sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories \
    && apk update \
    && apk add --no-cache \
        m4 autoconf gcc g++ make tar \
        openssl curl libstdc++ \
        openssl-dev curl-dev \
    
    && cd /tmp \
     && if [[ "['7.1']" == *"$(php -r "echo sprintf('%.1f',PHP_VERSION);")"* ]]; then \
        curl -SL "https://github.com/swoole/swoole-src/archive/v2.2.0.tar.gz" -o swoole.tar.gz; \
    else \
        curl -SL "https://github.com/swoole/swoole-src/archive/v${SWOOLE_VERSION}.tar.gz" -o swoole.tar.gz; \
    fi \
    && mkdir -p swoole \
    && tar -xf swoole.tar.gz -C swoole --strip-components=1 \
    && cd swoole \
    && phpize \
    && ./configure --enable-openssl --enable-http2 --enable-swoole-curl --enable-swoole-json \
    && make -s -j$(nproc) \
    && make install \

    && echo "extension=swoole.so" > /usr/local/etc/php/conf.d/00_swoole.ini \
    && echo "swoole.use_shortname='Off'" >> /usr/local/etc/php/conf.d/00_swoole.ini \
    && echo "opcache.enable_cli='On'" > /usr/local/etc/php/conf.d/00_opencache.ini \

    && apk del --purge *-dev m4 autoconf gcc g++ make tar \
    && rm -rf /var/cache/apk/* /tmp/* /usr/share/man /usr/share/php7 ~/.pearrc \
    
    && php --ini \
    && php -v \
    && php -m \
    && php --ri swoole \
    && echo 'Build Completed'