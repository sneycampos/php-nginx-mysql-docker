## PHP INSTALLATION
ARG PHP_VERSION
FROM php:${PHP_VERSION}-fpm

# apt-get install
RUN set -ex && \
    apt-get update && \
    apt-get install -yqq --no-install-recommends \
        curl \
        libpng-dev \
        libonig-dev \
        libxml2-dev \
        zip \
        unzip \
        libzip-dev \
        libmagickwand-dev \
        libpq-dev

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

# Install python and pip
RUN apt-get -y install python3 python3-pip && \
    update-alternatives --install /usr/bin/python python /usr/bin/python3 1 && \
    update-alternatives --set python /usr/bin/python3 && \
    python -m pip install --upgrade pip

# Install GDAL
RUN apt-get install -y gdal-bin libgdal-dev
RUN pip install GDAL==$(gdal-config --version) --global-option=build_ext --global-option="-I/usr/include/gdal"

# updates the lib file headers to gdal
RUN ldconfig

# Clear cache
RUN apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Get latest Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

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
