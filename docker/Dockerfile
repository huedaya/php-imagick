FROM php:8.0-apache

WORKDIR /app

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Install unzip utility and libs needed by zip PHP extension
RUN apt-get update && apt-get install -y \
    zlib1g-dev \
    libzip-dev \
    unzip
RUN docker-php-ext-install zip

RUN apt-get install -y libicu-dev
RUN docker-php-ext-configure intl
RUN docker-php-ext-install intl
RUN docker-php-ext-enable intl

# Install git
RUN apt-get update && apt-get -y install git

# Install Mysql
RUN docker-php-ext-install pdo pdo_mysql mysqli
RUN a2enmod rewrite

RUN apt-get update --fix-missing && apt-get install -y libmagickwand-dev --no-install-recommends && rm -rf /var/lib/apt/lists/*

# install imagick
# Version is not officially released https://pecl.php.net/get/imagick but following works for PHP 8
RUN mkdir -p /usr/src/php/ext/imagick; \
    curl -fsSL https://github.com/Imagick/imagick/archive/06116aa24b76edaf6b1693198f79e6c295eda8a9.tar.gz | tar xvz -C "/usr/src/php/ext/imagick" --strip 1; \
    docker-php-ext-install imagick;

# Skip Host verification for git
ARG USER_HOME_DIR=/root
RUN mkdir ${USER_HOME_DIR}/.ssh/ \
    && echo "StrictHostKeyChecking no " > ${USER_HOME_DIR}/.ssh/config

# Apache config
ARG APACHE_DOCUMENT_ROOT=/app/public
RUN a2enmod rewrite deflate headers \
    # setup web document root
    && sed -ri -e "s!/var/www/html!${APACHE_DOCUMENT_ROOT}!g" /etc/apache2/sites-available/*.conf \
    && sed -ri -e "s!/var/www/!${APACHE_DOCUMENT_ROOT}!g" /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf \
    # enable sites
    && a2ensite 000-default

#----- Setup php.ini
RUN cp /usr/local/etc/php/php.ini-development /usr/local/etc/php/php.ini \
    && sed -i "s|error_reporting\s=\sE_ALL|error_reporting= E_ALL \| E_STRICT|g" /usr/local/etc/php/php.ini \
    && sed -i "s|memory_limit = 1024M|memory_limit = 2024M|g" /usr/local/etc/php/php.ini \
    && sed -i "s|max_execution_time = 30|max_execution_time = 300|g" /usr/local/etc/php/php.ini