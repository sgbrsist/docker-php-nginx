ARG ALPINE_VERSION=3.19
FROM alpine:${ALPINE_VERSION}
LABEL Maintainer="Wesley Schons dos Santos <wesley@sgbr.com.br>"
LABEL Description="Lightweight container with Nginx 1.22 & PHP 8.1 based on Alpine Linux."
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
  php82 \
  php82-bcmath \
  php82-ctype \
  php82-curl \
  php82-dom \
  php82-fileinfo \
  php82-fpm \
  php82-ftp \
  php82-gd \
  php82-iconv \
  php82-intl \
  php82-json \
  php82-mbstring \
  php82-mysqli \
  php82-opcache \
  php82-tokenizer \
  php82-openssl \
  php82-pcntl \
  php82-pdo \
  php82-pdo_pgsql \
  php82-phar \
  php82-posix \
  php82-session \
  php82-soap \
  php82-simplexml \
  php82-xml \
  php82-xmlreader \
  php82-xmlwriter \
  php82-zip \
  supervisor \
  tzdata

# Configure nginx - http
COPY config/nginx.conf /etc/nginx/nginx.conf
# Configure nginx - default server
COPY config/conf.d /etc/nginx/conf.d/
COPY config/openssl.conf /etc/ssl/openssl.cnf

# Configure PHP-FPM
COPY config/fpm-pool.conf /etc/php82/php-fpm.d/www.conf
COPY config/php.ini /etc/php82/conf.d/custom.ini

# Configure supervisord
COPY config/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# Make sure files/folders needed by the processes are accessable when they run under the nobody user
RUN chown -R nobody.nobody /var/www/app /run /var/lib/nginx /var/log/nginx

# Switch to use a non-root user from here on
USER nobody

# Add application
COPY --chown=nobody src/ /var/www/app/

# Expose the port nginx is reachable on
EXPOSE 8080

# Let supervisord start nginx & php-fpm
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/conf.d/supervisord.conf"]

# Configure a healthcheck to validate that everything is up&running
HEALTHCHECK --timeout=10s CMD curl --silent --fail http://127.0.0.1:8080/fpm-ping
