# Base image
FROM php:7.1

# Base system dependencies
RUN apt-get update \
  && apt-get install -y \
    curl \
    git

# PHP extension - pdo_mysql
RUN docker-php-ext-install \
  pdo_mysql

# PHP extension - zip
RUN apt-get update \
  && apt-get install -y \
    zlib1g-dev \
  && docker-php-ext-install zip

# Composer
RUN curl -sS https://getcomposer.org/installer | php \
  && mv composer.phar /usr/local/bin/composer

# Application directory
RUN mkdir /application
WORKDIR /application

# Composer dependencies
COPY composer.json /application/composer.json
RUN mkdir /application/database \
  && mkdir /application/tests \
  && php -d memory_limit=-1 /usr/local/bin/composer install

# PHP extension - xdebug
ARG debug="false"
ENV debug=$debug
RUN if $debug -eq "true"; then \
  yes | pecl install xdebug \
  && echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.ini \
  && echo "xdebug.remote_autostart=On" >> /usr/local/etc/php/conf.d/xdebug.ini \
  && echo "xdebug.remote_connect_back=On" >> /usr/local/etc/php/conf.d/xdebug.ini \
  && echo "xdebug.remote_enable=On" >> /usr/local/etc/php/conf.d/xdebug.ini \
  && echo "xdebug.remote_handler=dbgp" >> /usr/local/etc/php/conf.d/xdebug.ini; \
  fi

# Application files
COPY . /application
RUN composer dump-autoload

# Startup
ENTRYPOINT ["php"]
CMD ["-S localhost:8000", "-t /application/public"]
