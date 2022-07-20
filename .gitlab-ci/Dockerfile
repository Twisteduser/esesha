FROM rewardenv/php-fpm:7.4-magento2-web-debian

ARG GITHUB_USER
ARG GITHUB_TOKEN
ARG BITBUCKET_PUBLIC_KEY
ARG BITBUCKET_PRIVATE_KEY
ARG MAGENTO_PUBLIC_KEY
ARG MAGENTO_PRIVATE_KEY
ARG MAGENTO_LANGUAGES
# It's good to use but pre 2.2 this flag is not implemented.
ARG MAGENTO_STATIC_CONTENT_DEPLOY_FORCE=true
ARG MAGENTO_DEV_STATIC_SIGN=true
ARG MAGENTO_DEV_JS_MERGE=true
ARG MAGENTO_DEV_JS_MINIFY=true
ARG MAGENTO_DEV_JS_BUNDLING=false
ARG MAGENTO_DEV_CSS_MERGE=true
ARG MAGENTO_DEV_CSS_MINIFY=true
ARG MAGENTO_DEV_HTML_MINIFY=false

USER www-data

COPY --chown=www-data:www-data . .

RUN set -eux \
    && composer config http-basic.repo.magento.com ${MAGENTO_PUBLIC_KEY} ${MAGENTO_PRIVATE_KEY} \
    && composer config github-oauth.github.com ${GITHUB_TOKEN} \
    && composer config bitbucket-oauth.bitbucket.org ${BITBUCKET_PUBLIC_KEY} ${BITBUCKET_PRIVATE_KEY} \
    && composer install --no-interaction --no-progress --prefer-dist --no-cache \
    && composer clear-cache \
    && bin/magento setup:di:compile \
    && composer dump-autoload -o \
    && mkdir -p /var/www/html/var/composer_home  \
    && cp -a auth.json /var/www/html/var/composer_home/ \
#    && cp -a composer.json /var/www/html/var/composer_home/ \
    # fix: https://github.com/magento/magento2/issues/33802
    && rm -f /var/www/html/app/etc/env.php \
    && mkdir -p /var/www/html/pub/static \
    && printf "<?php\nprint(\"build-date: $(date '+%Y/%m/%d %H:%M:%S')\");\n?>\n" > pub/version.php
#    && bin/magento app:config:import --no-interaction \
#    && bin/magento deploy:mode:set production --skip-compilation \
#    && if [ "${MAGENTO_STATIC_CONTENT_DEPLOY_FORCE}" = "true" ]; \
#      then bin/magento setup:static-content:deploy --jobs=$(nproc) -fv ${MAGENTO_LANGUAGES:-}; \
#      else bin/magento setup:static-content:deploy --jobs=$(nproc) -v ${MAGENTO_LANGUAGES:-}; \
#    fi

# TODO: Remove composer auth file from containers and add Variables in runtime.
