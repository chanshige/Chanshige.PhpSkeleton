FROM php:8.3-fpm-alpine3.20
LABEL maintainer="dev@shigeki.tokyo"

COPY ./docker/development/php/php.ini $PHP_INI_DIR
COPY ./docker/development/php/conf.d/* $PHP_INI_DIR/conf.d/
COPY ./docker/development/php/php-fpm.d/zz-docker.conf /usr/local/etc/php-fpm.d/
COPY ./docker/development/php/docker-healthcheck.sh /usr/local/bin/docker-healthcheck
RUN chmod +x /usr/local/bin/docker-healthcheck

HEALTHCHECK --interval=10s --timeout=3s --retries=3 CMD ["docker-healthcheck"]

RUN apk upgrade --update \
  && apk --no-cache add --virtual .build-dependencies \
  g++ \
  gcc \
  make \
  autoconf \
  wget \
  unzip \
  git \
  tzdata \
  oniguruma-dev \
  postgresql-dev \
  linux-headers \
  && apk --no-cache add \
  fcgi \
  postgresql-client \
  libzip-dev \
  icu-dev \
  libpng-dev \
  libjpeg-turbo-dev \
  libwebp-dev \
  freetype-dev \
  exiftool \
  strace \
  && pecl install apcu xdebug \
  && docker-php-ext-enable apcu xdebug \
  && docker-php-ext-configure gd --with-freetype --with-jpeg --with-webp \
  && docker-php-ext-configure pgsql --with-pgsql=/usr/local/pgsql \
  && docker-php-ext-install -j$(nproc) \
  bcmath \
  gd \
  opcache \
  pgsql \
  pdo_pgsql \
  zip \
  intl \
  opcache \
  exif \
  && cp /usr/share/zoneinfo/Asia/Tokyo /etc/localtime \
  && apk del .build-dependencies \
  && pecl clear-cache \
  && rm -rf /usr/lib/mysqld* \
  && rm -rf /usr/bin/mysql* \
  && rm -rf /var/cache/apk/*

# install gnu-libiconv and set LD_PRELOAD env to make iconv work fully on Alpine image.
# see https://github.com/docker-library/php/issues/240#issuecomment-763112749
ENV LD_PRELOAD /usr/lib/preloadable_libiconv.so

RUN addgroup -S www-user \
    && adduser -S www-user -G www-user \
    && addgroup www-user www-data
#    && echo "www-user ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers \
#    && echo 'www-user:www-user' | chpasswd

COPY --from=composer /usr/bin/composer /usr/bin/composer

ENV COMPOSER_ALLOW_SUPERUSER 1
ENV COMPOSER_NO_INTERACTION 1
ENV COMPOSER_MEMORY_LIMIT -1
# TODO: homedirのパス設定を考える
ENV COMPOSER_HOME /home/www-user/.composer
ENV COMPOSER_CACHE_DIR /home/www-user/.caches/composer
ENV PATH /home/www-user/.composer/vendor/bin:$PATH

USER www-user
WORKDIR /var/www
