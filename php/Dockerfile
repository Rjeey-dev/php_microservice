FROM php:8.0-fpm-alpine3.13

# install deps
RUN set -xe \
     && apk add --no-cache \
         ${PHPIZE_DEPS} \
         git \
         openssh \
         openssl \
         libressl-dev \
         libmemcached-dev \
         pkgconfig \
         zlib-dev \
         cyrus-sasl-dev \
         openjdk7-jre \
         gmp \
         gmp-dev \
         libtool \
         supervisor \
         libxml2-dev \
         icu-dev \
     && export CFLAGS="$PHP_CFLAGS" CPPFLAGS="$PHP_CPPFLAGS" LDFLAGS="$PHP_LDFLAGS" \
     && docker-php-ext-configure  \
         intl  \
     && docker-php-ext-install \
         pcntl \
         pdo_mysql \
         soap \
         gmp \
         intl \
     && pecl install -f \
         memcached \
         mongodb \
         apcu \
         redis \
     && docker-php-ext-enable \
         memcached \
         apcu \
         mongodb \
         redis \
     && rm -rf /tmp/*

COPY ./php/php.ini ${PHP_INI_DIR}/conf.d/
COPY ./php/supervisor/* /etc/supervisor/conf.d/
COPY ./php/supervisord.conf /etc/supervisor

COPY ./php/crontab /etc/cron.d/crontab
RUN chmod 0644 /etc/cron.d/crontab
RUN crontab /etc/cron.d/crontab

CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/supervisord.conf"]

RUN echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.ini \
   && echo "xdebug.remote_enable=on" >> /usr/local/etc/php/conf.d/xdebug.ini \
   && echo "xdebug.remote_autostart=off" >> /usr/local/etc/php/conf.d/xdebug.ini \
   && echo "xdebug.remote_log=/var/log/xdebug.log" >> /usr/local/etc/php/conf.d/xdebug.ini;

WORKDIR /var/www/app