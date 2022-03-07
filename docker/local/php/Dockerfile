FROM php:8.0-fpm

# Arguments defined in docker-compose.yml
ARG WWWGROUP
ARG WWWGID
ARG WWWUSER
ARG WWWUID

# Install system dependencies
RUN apt-get update && apt-get install -y \
    git \
    curl \
    libpng-dev \
    libonig-dev \
    libpq-dev \
    librabbitmq-dev \
    libxml2-dev \
    supervisor \
    zip \
    unzip

# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Install PHP extensions
RUN docker-php-ext-install pgsql pdo pdo_pgsql mbstring exif pcntl bcmath gd \
    && docker-php-ext-enable pdo_pgsql

RUN apt-get update && pecl install amqp-1.11.0 \
    && docker-php-ext-enable amqp

# Disable autoupdate
#RUN apt remove -y unattended-upgrades

# Get latest Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Create system user to run Composer and Artisan Commands
RUN groupadd --force -g $WWWGID $WWWUSER
RUN useradd -ms /bin/bash --no-user-group -g $WWWGROUP -u $WWWUID $WWWUSER

COPY php/start-container /usr/local/bin/start-container
RUN chmod +x /usr/local/bin/start-container
COPY php/supervisord.conf /etc/supervisor/conf.d/supervisord.conf
RUN mv "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini"
COPY php/conf.d/docker-php-override.ini $PHP_INI_DIR/conf.d/docker-php-override.ini

# Set working directory
WORKDIR /var/www
RUN chown -R www-data:www-data /var/www
USER $WWWUSER
