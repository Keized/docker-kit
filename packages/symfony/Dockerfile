FROM php:8.2-fpm-alpine AS base
WORKDIR /app

# Install build dependencies
RUN set -eux; \
    apk add --no-cache --virtual .build-deps $PHPIZE_DEPS

# Install production dependencies
RUN set -eux; \
    apk add --no-cache bash curl nano rabbitmq-c-dev rabbitmq-c icu-dev openldap-dev postgresql-dev pcre-dev libxslt-dev libzip-dev zlib-dev acl libpng-dev libjpeg-turbo-dev libpq-dev

# Install PHP extensions
RUN set -eux; \
    docker-php-ext-install intl pdo_pgsql pgsql zip opcache \
    sysvsem bcmath xsl ldap

# Install PECL extensions
RUN set -eux; \
    pecl install redis amqp

# Enable PECL extensions
RUN set -eux; \
    docker-php-ext-enable redis amqp

# Cleanup build dependencies
RUN set -eux; \
    apk del -f .build-deps

# Configure PHP
COPY --link ./conf.d/app.ini $PHP_INI_DIR/conf.d/

# Install composer
ENV COMPOSER_ALLOW_SUPERUSER=1
ENV PATH="${PATH}:/root/.composer/vendor/bin"
COPY --from=composer/composer:2-bin --link /composer /usr/bin/composer

FROM base AS prod
ENV APP_ENV=prod
RUN mv "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini"
COPY --link ./conf/app.prod.ini $PHP_INI_DIR/conf.d/
COPY --link composer.* symfony.* ./
RUN set -eux; \
	composer install --no-cache --prefer-dist --no-dev --no-autoloader --no-scripts --no-progress
COPY --link . ./
RUN set -eux; \
	mkdir -p var/cache var/log; \
	composer dump-autoload --classmap-authoritative --no-dev; \
	composer dump-env prod; \
	composer run-script --no-dev post-install-cmd; \
	chmod +x bin/console; sync; \
CMD ["php-fpm"]
EXPOSE 9000

FROM base AS dev
ENV APP_ENV=dev
RUN curl -1sLf 'https://dl.cloudsmith.io/public/symfony/stable/setup.alpine.sh' | bash
RUN apk add --no-cache symfony-cli make
RUN mv "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini"
RUN set -eux; \
    apk add --no-cache $PHPIZE_DEPS linux-headers; \
    pecl install xdebug; \
    docker-php-ext-enable xdebug;
# configure entrypoint
#COPY --link --chmod=755 ./entrypoint.sh /usr/local/bin/docker-entrypoint
#ENTRYPOINT ["docker-entrypoint"]
CMD ["php-fpm"]
EXPOSE 9000
