FROM php:7.4-fpm-alpine

# Import extension installer
COPY --from=mlocati/php-extension-installer /usr/bin/install-php-extensions /usr/bin/

# Install extensions
RUN install-php-extensions bcmath pdo_mysql redis

# Install Composer
COPY --from=composer:latest /usr/bin/composer /usr/local/bin/composer

# Install extra packages
RUN apk --no-cache add bash mysql-client mariadb-connector-c-dev

# non-root user
RUN apk add shadow && usermod -u 1000 www-data && groupmod -g 1000 www-data && \
    chown -R www-data:www-data /var/www
