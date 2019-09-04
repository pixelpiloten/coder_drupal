FROM php:7.2-apache

ENV APACHE_RUN_USER=www-data
ENV APACHE_RUN_GROUP=www-data
ENV COMPOSER_HOME=/home/coder/.composer
ENV PATH=$PATH:vendor/bin

# Install php extensions and dependencies.
RUN apt-get update && apt-get install -y \
    wget \
    gzip \
    tar \
    curl \
    git \
    rsync \
    zip \
    mariadb-client \
    libjpeg-dev \
    libpng-dev \
    libpq-dev && \
    docker-php-ext-configure gd --with-png-dir=/usr --with-jpeg-dir=/usr && \
    docker-php-ext-install -j "$(nproc)" \
    gd \
    opcache \
    pdo_mysql \
    pdo_pgsql \
    zip

# Enable apache modules.
RUN a2enmod rewrite

# Copy apache configurations.
COPY ./.docker/apache/config/ports.conf /etc/apache2/ports.conf
COPY ./.docker/apache/config/000-default.conf /etc/apache2/sites-available/000-default.conf

# Copy custom php.ini file.
COPY ./.docker/apache/config/999-coder-php.ini /usr/local/etc/php/conf.d/999-coder-php.ini

RUN usermod -u 1000 www-data && \
    groupmod -g 1000 www-data

# Install composer.
RUN cd /tmp && \
    php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && \
    php composer-setup.php && \
    php -r "unlink('composer-setup.php');" && \
    mv composer.phar /usr/local/bin/composer

WORKDIR /home/coder/project/code-server

RUN chown -R 1000:1000 /home/coder/project/code-server

USER www-data