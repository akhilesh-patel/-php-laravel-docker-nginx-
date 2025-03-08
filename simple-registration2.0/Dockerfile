# === Stage 1: Build Stage ===
FROM php:8.3-fpm AS builder

# Set working directory
WORKDIR /var/www

# Install system dependencies and required PHP extensions
RUN apt-get update && apt-get install -y --no-install-recommends \
    zip unzip git curl build-essential \
    libpng-dev libjpeg-dev libfreetype6-dev \
    libonig-dev libxml2-dev zip libzip-dev \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install gd mbstring pdo pdo_mysql zip bcmath exif pcntl fileinfo opcache \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Install Composer globally
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Copy Laravel dependencies for caching
COPY composer.json composer.lock ./



# Copy Laravel project files
COPY . .

# Install Laravel dependencies (optimized for production)
RUN composer install --no-interaction --prefer-dist --no-dev --optimize-autoloader

# Create necessary directories with correct permissions
RUN mkdir -p /var/www/storage/framework/{sessions,views,cache} \
    && chown -R www-data:www-data /var/www \
    && chmod -R 775 /var/www/storage /var/www/bootstrap/cache

# Run cache clearing script (if exists)
RUN if [ -f ./cache-clear.sh ]; then ./cache-clear.sh; fi


# === Stage 2: Final Stage (Smaller Image) ===
FROM php:8.3-fpm AS production

# Set working directory
WORKDIR /var/www

# Copy only the necessary files from the build stage
COPY --from=builder /var/www /var/www

# Set permissions
RUN chown -R www-data:www-data /var/www \
    && chmod -R 775 /var/www/storage /var/www/bootstrap/cache

# Switch to non-root user for better security
USER www-data

# Expose PHP-FPM port
EXPOSE 9000

# Healthcheck for PHP-FPM
HEALTHCHECK CMD curl --fail http://localhost:9000/fpm-ping || exit 1

# Start PHP-FPM
CMD ["php-fpm"]
