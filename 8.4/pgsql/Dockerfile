ARG ALPINE_VERSION=3.21
# FROM alpine:${ALPINE_VERSION}
FROM php:8.4-fpm-alpine${ALPINE_VERSION} AS base
LABEL Maintainer="Wesley Schons dos Santos <wesley@sgbr.com.br>"
LABEL Description="Lightweight container with Nginx 1.22 & PHP 8.4 based on Alpine Linux."
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
  php84 \
  php84-bcmath \
  php84-ctype \
  php84-curl \
  php84-dom \
  php84-fileinfo \
  php84-fpm \
  php84-ftp \
  php84-gd \
  php84-iconv \
  php84-intl \
  php84-json \
  php84-mbstring \
  php84-mysqli \
  php84-opcache \
  php84-tokenizer \
  php84-openssl \
  php84-pcntl \
  php84-pdo \
  php84-pdo_pgsql \
  php84-phar \
  php84-posix \
  php84-session \
  php84-soap \
  php84-simplexml \
  php84-xml \
  php84-xmlreader \
  php84-xmlwriter \
  php84-zip \
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
COPY config/fpm-pool.conf /etc/php84/php-fpm.d/www.conf
COPY config/php.ini /etc/php84/conf.d/custom.ini

# Configure supervisord
COPY config/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# Make sure files/folders needed by the processes are accessable when they run under the nobody user
# RUN chown -R nobody.nobody /var/www/app /run /var/lib/nginx /var/log/nginx /tmp
RUN chown -R www-data:www-data /var/www/app && \
  chown -R nginx:nginx /run /var/lib/nginx /var/log/nginx

# Switch to use a non-root user from here on
# USER nobody
USER www-data

# Add application
# COPY --chown=nobody src/ /var/www/app/
COPY --chown=www-data src/ /var/www/app/

# Expose the port nginx is reachable on
EXPOSE 8080

# Let supervisord start nginx & php-fpm
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]

# Configure a healthcheck to validate that everything is up&running
HEALTHCHECK --timeout=10s CMD curl --silent --fail http://127.0.0.1:8080/fpm-ping
