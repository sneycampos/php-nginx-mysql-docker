## PHP INSTALLATION
ARG PHP_VERSION
FROM php:${PHP_VERSION}-fpm

# apt-get install
RUN set -ex && \
    apt-get update && \
    apt-get install -yqq --no-install-recommends \
        wget \
        curl \
        zip \
        unzip \
        sqlite3 \
        autoconf \
        automake \
        autotools-dev \
        libtool \
        libzip-dev \
        libpng-dev \
        libonig-dev \
        libxml2-dev \
        libmagickwand-dev \
        libpq-dev \
        libcurl3-gnutls \
        libtiff5 \
        libcurl4-gnutls-dev \
        libsqlite3-dev \
        libtiff-dev \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libwebp-dev

# Install PHP extensions
RUN docker-php-ext-install pdo pdo_mysql pdo_pgsql pgsql mbstring zip exif pcntl bcmath

RUN docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql && \
    printf "\n" | pecl install imagick && \
    docker-php-ext-enable imagick

RUN docker-php-ext-configure gd --with-freetype --with-jpeg

RUN docker-php-ext-install gd

#Install xdebug
ARG INSTALL_XDEBUG
RUN if [ ${INSTALL_XDEBUG} = true ]; then \
    pecl install xdebug-2.9.8 && docker-php-ext-enable xdebug \
;fi

#Install postgis
ARG INSTALL_POSTGIS
RUN if [ ${INSTALL_POSTGIS} = true ]; then \
    apt-get update && apt-get -y install postgis \
;fi

# Install nodejs
ARG INSTALL_NODEJS
RUN if [ ${INSTALL_NODEJS} = true ]; then \
    curl -sL https://deb.nodesource.com/setup_14.x -o nodesource_setup.sh && bash nodesource_setup.sh && apt-get -q -y install nodejs \
;fi

# proj4
ENV PROJ_VERSION master
ENV PROJ_GIT_HASH 28aa4f13095eb69438d47f30663f13bd8780c0c1

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
    && rm -fr /usr/src/proj

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

# Clear cache
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Get latest Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Install nodejs
RUN curl -sL https://deb.nodesource.com/setup_14.x -o nodesource_setup.sh && bash nodesource_setup.sh && apt-get -q -y install nodejs

# Add user for laravel application
RUN groupadd -g 1000 www && useradd -u 1000 -ms /bin/bash -g www www

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
