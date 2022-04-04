FROM php:8.1-alpine

RUN set -eux ; \
  apk add --no-cache --virtual .composer-rundeps \
    p7zip \
    bash \
    coreutils \
    git \
    make \
    mercurial \
    openssh-client \
    patch \
    subversion \
    tini \
    unzip \
    zip

RUN printf "# composer php cli ini settings\n\
date.timezone=UTC\n\
memory_limit=-1\n\
" > $PHP_INI_DIR/php-cli.ini

ENV INSTALL_PHP_EXTENSIONS_VERSION 1.5.7
ENV INSTALL_PHP_EXTENSIONS_SHA512 85c9f513f5bd340d97e378ccf2b140b7f7927462697e04289deec4a88d80fc36864cb8b60a95479996210eb7e6c34c1fc16a7c23789c3095dc74e937fd26da88

ENV PHP_SCOPER_VERSION 0.17.1
ENV PHP_SCOPER_SHA512 25cfec9d411a75352d73c6e1907425f1f2f5ffcfcbd894dd270736f5286c71ba0dcf92675301074170a3011a9b7cb227de59e35d890e0560787adf8cd249fdad

RUN set -eux ; \
  # install https://github.com/mlocati/docker-php-extension-installer
  curl \
    --silent \
    --fail \
    --location \
    --retry 3 \
    --output /usr/local/bin/install-php-extensions \
    --url https://github.com/mlocati/docker-php-extension-installer/releases/download/${INSTALL_PHP_EXTENSIONS_VERSION}/install-php-extensions \
  ; \
  echo ${INSTALL_PHP_EXTENSIONS_SHA512} /usr/local/bin/install-php-extensions | sha512sum --strict --check ; \
  chmod +x /usr/local/bin/install-php-extensions ; \
  # install necessary/useful extensions not included in base image
  install-php-extensions \
    bz2 \
    intl \
    zip \
  ; \
  # install public keys for snapshot and tag validation, see https://composer.github.io/pubkeys.html
  curl \
    --silent \
    --fail \
    --location \
    --retry 3 \
    --output /usr/bin/php-scoper \
    --url https://github.com/humbug/php-scoper/releases/download/${PHP_SCOPER_VERSION}/php-scoper.phar \
  ; \
  echo ${PHP_SCOPER_SHA512} /usr/bin/php-scoper | sha512sum --strict --check ; \
  chmod +x /usr/bin/php-scoper

COPY docker-entrypoint.sh /docker-entrypoint.sh

WORKDIR /app

ENTRYPOINT ["/docker-entrypoint.sh"]

CMD ["php-scoper"]
