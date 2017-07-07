ARG FROM_FQIN
FROM ${FROM_FQIN}

ARG COMPOSER_VERSION
ARG PRESTISSIMO_VERSION
ARG PHPCSFIXER_VERSION
ARG APCU_VERSION

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
    && docker-php-ext-enable --ini-name 00-opcache.ini opcache \
    && sed -i -e 's/extension=/zend_extension=/' /usr/local/etc/php/conf.d/00-opcache.ini \
    && printf "\
opcache.max_accelerated_files=16229\n\
opcache.enable_file_override=1\n\
opcache.log_verbosity_level=0\n\
opcache.fast_shutdown=1\n\
" >> /usr/local/etc/php/conf.d/00-opcache.ini \
    && apt-get update \
    && apt-get install -y  libicu-dev zlib1g-dev \
    && docker-php-ext-install \
        intl \
        zip \
        bcmath \
        pcntl \
    && docker-php-source extract \
    && printf "\n" |  printf "\n" | pecl install apcu-${APCU_VERSION} \
    && docker-php-ext-enable --ini-name 100-apc.ini apcu apcu \
    && addgroup --uid 1000 php \
    && adduser --system php --disabled-password --no-create-home --ingroup php --uid 1000 \
    && mkdir -p /var/app \
    && chown php:php /var/app \
    && mkdir -p ${COMPOSER_HOME} \
    && chown php:php ${COMPOSER_HOME} \
    && curl -sL https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer --version=${COMPOSER_VERSION} \
    && apt-get remove -y libicu-dev zlib1g-dev icu-devtools \
    && apt-get install libicu52 \
    && docker-php-source delete \
    && rm -rf \
        /usr/src \
        /tmp/* \
        /usr/share/doc \
        /usr/share/man \
        /usr/share/doc \
        /var/cache/apk/* \
    && apt-get clean -y

USER php

RUN composer global require hirak/prestissimo:${PRESTISSIMO_VERSION} \
    && composer global require friendsofphp/php-cs-fixer:${PHPCSFIXER_VERSION} \
    && rm -rf ${COMPOSER_HOME}/cache

WORKDIR /app
