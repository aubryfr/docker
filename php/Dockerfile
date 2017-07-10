ARG FROM_FQIN
FROM ${FROM_FQIN}

ARG COMPOSER_VERSION
ARG PRESTISSIMO_VERSION
ARG PHPCSFIXER_VERSION
ARG APCU_VERSION
ARG MEMCACHED_VERSION
ARG REDIS_VERSION
ARG IMAGICK_VERSION

ENV COMPOSER_HOME=/.composer \
    PATH="$PATH:/.composer/vendor/bin"

RUN env \
    && printf "\
date.timezone=\"UTC\"\n\
request_order=GP\n\
expose_php=Off\n\
enable_dl=Off\n\
short_open_tag=Off\n\
zend.assertions=-1\n\
" > /usr/local/etc/php/php.ini \
    && docker-php-source extract \
    && apt-get update \
    && apt-get install -y git-core libicu-dev zlib1g-dev libpq-dev libxml2-dev libc-client-dev libkrb5-dev libmemcached-dev libmagickwand-dev \
    && docker-php-ext-install opcache \
    && printf "\
opcache.max_accelerated_files=16229\n\
opcache.enable_file_override=1\n\
opcache.log_verbosity_level=0\n\
opcache.fast_shutdown=1\n\
" >> /usr/local/etc/php/conf.d/00-opcache.ini \
    && docker-php-ext-install \
        intl \
        zip \
        bcmath \
        pcntl \
        pgsql \
        pdo_pgsql \
        soap \
    && docker-php-ext-configure imap --with-kerberos --with-imap-ssl \
    && docker-php-ext-install imap \
    && printf "\n" |  printf "\n" | pecl install apcu-${APCU_VERSION} \
    && docker-php-ext-enable --ini-name 100-apc.ini apcu apcu \
    && printf "\n" | pecl install memcached-${MEMCACHED_VERSION} \
    && docker-php-ext-enable --ini-name 100-memcached.ini memcached \
    && apt-get install -y libmagickwand-dev \
    && printf "\n" | pecl install imagick-${IMAGICK_VERSION} \
    && docker-php-ext-enable --ini-name 100-imagick.ini imagick \
    && pecl install redis-${REDIS_VERSION} \
    && docker-php-ext-enable --ini-name 100-redis.ini redis \
    && apt-get remove -y libicu-dev icu-devtools zlib1g-dev libpq-dev libxml2-dev libc-client-dev libkrb5-dev libmemcached-dev libmagickwand-dev \
    && apt-get install -y libicu52 libmagickwand-6.q16-2 libc-client2007e libmemcached11 libmemcachedutil2 libpq5 \
    && docker-php-source delete \
    && mkdir -p /var/app ${COMPOSER_HOME} \
    && curl -sL https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer --version=${COMPOSER_VERSION} \
    && composer global require hirak/prestissimo:${PRESTISSIMO_VERSION} \
    && composer global require friendsofphp/php-cs-fixer:${PHPCSFIXER_VERSION} \
    && rm -rf \
        ${COMPOSER_HOME}/cache \
        /tmp/* \
        /usr/share/doc \
        /usr/share/man \
        /usr/share/doc \
        /var/cache/apk/* \
    && apt-get -y autoremove \
    && apt-get clean -y \
    && php -m

WORKDIR /app
