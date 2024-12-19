FROM wordpress:6.7.1

# Install dependencies for Composer
RUN apt-get update && apt-get install -y \
    curl \
    unzip \
    wget \
    git \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Install Composer globally
RUN curl -sS https://getcomposer.org/installer | php && mv composer.phar /usr/local/bin/composer

# Install WP-CLI
RUN curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar \
    && chmod +x wp-cli.phar \
    && mv wp-cli.phar /usr/local/bin/wp

# Set proper permissions for WordPress files
RUN chown -R www-data:www-data /var/www/html && chmod -R 755 /var/www/html

# Allow Composer plugins (required for Composer >=2.2)
ENV COMPOSER_ALLOW_SUPERUSER=1

WORKDIR /var/www/html

COPY "composer.json" .
COPY "auth.json" .
COPY . .

CMD ["apache2-foreground"]