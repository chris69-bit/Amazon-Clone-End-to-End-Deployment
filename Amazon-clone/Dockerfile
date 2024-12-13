# Stage 1: Build frontend assets
FROM node:22 AS frontend-builder

# Set the working directory
WORKDIR /app

# Copy frontend dependencies
COPY package.json package-lock.json ./

# Install dependencies
RUN npm install

# Copy the frontend source files
COPY resources resources
COPY vite.config.js postcss.config.js tailwind.config.js ./

# Build the frontend assets
RUN npm run build


# Stage 2: Build the Laravel backend
FROM php:8.2-fpm AS backend

# Install system dependencies
RUN apt-get update && apt-get install -y \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    zip \
    unzip \
    git \
    curl && \
    docker-php-ext-install pdo_mysql mbstring exif pcntl bcmath gd

# Install Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Set the working directory
WORKDIR /var/www/html

# Copy Laravel backend dependencies
COPY composer.json composer.lock ./

# Install PHP dependencies
RUN composer install --no-dev --optimize-autoloader

# Copy the application source code
COPY . .

# Copy frontend assets built in the previous stage
COPY --from=frontend-builder /app/public ./public

# Set permissions for storage and bootstrap/cache
RUN chown -R www-data:www-data storage bootstrap/cache

# Expose port 9000 for PHP-FPM
EXPOSE 9000

# Set the entrypoint
CMD ["php-fpm"]
