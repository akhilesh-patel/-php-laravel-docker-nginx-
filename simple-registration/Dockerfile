# Use official PHP 8.3 FPM image
FROM php:8.3-fpm
# Set working directory
WORKDIR /var/www
# Install system dependencies and required PHP extensions in a single RUN
RUN apt-get update && apt-get install -y \
    zip unzip git curl build-essential \
    libpng-dev libjpeg-dev libfreetype6-dev \
    libonig-dev libxml2-dev zip libzip-dev \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install gd mbstring pdo pdo_mysql zip bcmath exif pcntl fileinfo opcache \
    && apt-get clean && rm -rf /var/lib/apt/lists/*
# Install Composer globally
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer
# Copy Laravel project files
COPY . .
# Create necessary directories with correct permissions
RUN mkdir -p /var/www/storage/framework/{sessions,views,cache} \
    && chown -R www-data:www-data /var/www \
    && chmod -R 775 /var/www/storage /var/www/bootstrap/cache
# Switch to non-root user
USER www-data
# Install Laravel dependencies
RUN composer install --no-interaction --prefer-dist --optimize-autoloader
# Pre-cache configurations for performance
RUN php artisan config:cache && php artisan route:cache && php artisan view:cache
# Expose PHP-FPM port
EXPOSE 9000
# Start PHP-FPM
CMD ["php-fpm"]
