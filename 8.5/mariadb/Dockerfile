ARG ALPINE_VERSION=3.23
# FROM alpine:${ALPINE_VERSION}
FROM php:8.5-fpm-alpine${ALPINE_VERSION} AS base
LABEL Maintainer="Wesley Schons dos Santos <wesley@sgbr.com.br>"
LABEL Description="Lightweight container with Nginx 1.22 & PHP 8.5 based on Alpine Linux."
# Setup document root
WORKDIR /var/www/app

# Install packages and remove default server definition
RUN apk add --no-cache \
  curl \
  ca-certificates \
  nginx \
  zip \
  unzip \
  libxml2-dev \
  libpng-dev \
  oniguruma-dev \
  libxml2-utils \
  php85 \
  php85-bcmath \
  php85-ctype \
  php85-curl \
  php85-dom \
  php85-fileinfo \
  php85-fpm \
  php85-ftp \
  php85-gd \
  php85-intl \
  php85-json \
  php85-mbstring \
  php85-mysqli \
  php85-tokenizer \
  php85-openssl \
  php85-pdo \
  php85-pdo_mysql \
  php85-phar \
  php85-session \
  php85-soap \
  php85-simplexml \
  php85-xml \
  php85-xmlreader \
  php85-xmlwriter \
  php85-zip \
  php85-calendar \
  supervisor \
  tzdata \
  libxext \
  libxrender \
  fontconfig \
  gcompat

# Configure nginx - http
COPY config/nginx.conf /etc/nginx/nginx.conf
# Configure nginx - default server
COPY config/conf.d /etc/nginx/conf.d/
COPY config/openssl.conf /etc/ssl/openssl.cnf

# Configure PHP-FPM
COPY config/fpm-pool.conf /etc/php85/php-fpm.d/www.conf
COPY config/php.ini /etc/php85/conf.d/custom.ini

# Configure supervisord
COPY config/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# Make sure files/folders needed by the processes are accessable when they run under the nobody user
RUN chown -R nobody:nobody /var/www/app /run /var/lib/nginx /var/log/nginx
# RUN chown -R www-data:www-data /var/www/app && \
#   chown -R www-data:www-data /run /var/lib/nginx /var/log/nginx

# Switch to use a non-root user from here on
USER nobody
# USER www-data

# Add application
COPY --chown=nobody src/ /var/www/app/
# COPY --chown=www-data src/ /var/www/app/

# Expose the port nginx is reachable on
EXPOSE 8080

# Let supervisord start nginx & php-fpm
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]

# Configure a healthcheck to validate that everything is up&running
HEALTHCHECK --timeout=10s CMD curl --silent --fail http://127.0.0.1:8080/fpm-ping
