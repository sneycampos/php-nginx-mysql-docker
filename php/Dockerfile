ARG PHP_VERSION

FROM php:${PHP_VERSION}-fpm

# apt-get install
RUN set -ex && \
    apt-get update && \
    apt-get install -y --no-install-recommends \
        wget \
        curl \
        zip \
        unzip \
        sqlite3 \
        autoconf \
        automake \
        postgis \
        libtool \
        libtiff5 \
        libpq-dev \
        libzip-dev \
        libpng-dev \
        libonig-dev \
        libmagickwand-dev \
        libcurl4-gnutls-dev \
        libsqlite3-dev

# Clear cache
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Install PHP extensions
RUN docker-php-ext-install pdo_mysql pdo_pgsql mbstring zip exif pcntl bcmath gd && \
    printf "\n" | pecl install imagick && \
    docker-php-ext-enable imagick

# proj4
ENV PROJ_VERSION master
#ENV PROJ_GIT_HASH 28aa4f13095eb69438d47f30663f13bd8780c0c1

RUN set -ex \
    && cd /usr/src \
    && mkdir proj \
    && wget -q https://github.com/OSGeo/PROJ/archive/${PROJ_VERSION}.tar.gz -O - \
        | tar xz -C proj --strip-components=1 \
    && cd proj \
    && ./autogen.sh \
    && ./configure --disable-static \
    && make -j$(nproc) \
    && make install \
    && cd / \
    && rm -fr /usr/src/PROJ

# gdal
ENV GDAL_VERSION master

RUN set -ex \
    && cd /usr/src \
    && mkdir gdal \
    && wget -q https://github.com/OSGeo/gdal/archive/${GDAL_VERSION}.tar.gz -O - \
        | tar xz -C gdal --strip-components=1 \
    && cd gdal/gdal \
    && ./autogen.sh \
    && ./configure --disable-static \
    && make -j$(nproc) \
    && make install \
    && cd / \
    && rm -fr /usr/src/gdal

# Get latest Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Install nodejs
RUN curl -sL https://deb.nodesource.com/setup_14.x -o nodesource_setup.sh && bash nodesource_setup.sh && apt-get -q -y install nodejs

# Add user for laravel application
RUN groupadd -g 1000 www
RUN useradd -u 1000 -ms /bin/bash -g www www

# Copy existing application directory contents
COPY . /var/www

# Copy existing application directory permissions
COPY --chown=www:www . /var/www

# Change current user to www
USER www

# Set working directory
WORKDIR /var/www

# Expose port 9000 and start php-fpm server
EXPOSE 9000
CMD ["php-fpm"]