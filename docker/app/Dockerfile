FROM composer:2.2 AS COMPOSER_BASE

WORKDIR /var/www

COPY . /var/www

RUN docker-php-ext-install pdo_mysql bcmath

RUN composer install --ignore-platform-reqs --no-interaction --prefer-dist --optimize-autoloader
#RUN composer install --ignore-platform-reqs --no-interaction --no-plugins --no-scripts --prefer-dist
#RUN composer require rene-roscher/pterodactyl-api-addon --ignore-platform-reqs --no-interaction

FROM node:16-alpine AS NODE_BASE

WORKDIR /var/www

COPY --from=COMPOSER_BASE /var/www .

RUN yarn install --frozen-lockfile

RUN yarn run build:production

FROM ghcr.io/rene-roscher/php-docker:php-8.2 AS APP_BASE

WORKDIR /var/www

COPY --from=NODE_BASE /var/www .

#RUN echo "" > /var/www/storage/logs/laravel.log

#RUN chmod -R gu+w storage/ && chmod -R guo+w storage/ && chmod -R gu+w bootstrap/cache/ && chmod -R guo+w bootstrap/cache/

#RUN chown -R www-data:www-data /var/www

# Clear Laravel logs and set permissions
RUN echo "" > /var/www/storage/logs/laravel.log && \
    chmod -R gu+w storage/ && \
    chmod -R guo+w storage/ && \
    chmod -R gu+w bootstrap/cache/ && \
    chmod -R guo+w bootstrap/cache/ && \
    chown -R www-data:www-data /var/www

COPY ./docker/supervisord.conf /etc/supervisord.conf

CMD ["supervisord", "-c", "/etc/supervisord.conf"]
