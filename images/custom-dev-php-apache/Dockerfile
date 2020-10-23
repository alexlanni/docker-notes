FROM php:7.4-apache

## Update package information
RUN apt-get update

##  Install zip libraries and extension
RUN apt-get install --yes git zlib1g-dev libzip-dev
RUN docker-php-ext-install zip

## Configure Apache
RUN a2enmod rewrite
RUN sed -i 's!/var/www/html!/var/www/public!g' /etc/apache2/sites-available/000-default.conf
RUN sed -s -i -e "s/80/8000/" /etc/apache2/ports.conf /etc/apache2/sites-available/*.conf
RUN mv /var/www/html /var/www/public

## Install Composer
RUN curl -sS https://getcomposer.org/installer \
  | php -- --install-dir=/usr/local/bin --filename=composer

## Install Node JS e NPM
#RUN apt-get install --yes nodejs npm
#RUN npm install -g n
#RUN n latest
#RUN npm install -g gulp-cli
#RUN npm install -g node-sass --unsafe

###
## Additional PHP extensions.  To use these extensions uncomment the
## leading `# ` from the RUN commands.  These are provided to show
## examples of including other libraries into the PHP installation.
###

## Install i18n libraries and extensions
# RUN apt-get install --yes libicu-dev
# RUN docker-php-ext-configure intl
# RUN docker-php-ext-install intl

## Install mbstring for i18n string support
# RUN docker-php-ext-install mbstring

## MySQL PDO support
# RUN docker-php-ext-install pdo_mysql

## Redis support.  igbinary and libzstd-dev are only needed based on redis
## pecl options
RUN pecl install igbinary
RUN docker-php-ext-enable igbinary
RUN apt-get install --yes libzstd-dev
RUN pecl install redis xdebug
RUN docker-php-ext-enable redis xdebug

RUN docker-php-ext-install pdo pdo_mysql

RUN useradd -ms /bin/bash -u 1000 worker
RUN usermod -g www-data worker

## SSH KEYS
ARG SSH_PRIVATE_KEY
RUN mkdir /home/worker/.ssh/
RUN echo "${SSH_PRIVATE_KEY}" > /home/worker/.ssh/id_rsa
RUN chown -R worker:worker /home/worker/.ssh
RUN chmod 700 /home/worker/.ssh/id_rsa

RUN apt-get install --yes vim telnet
RUN docker-php-ext-install bcmath sockets
ADD custom-php.ini /usr/local/etc/php/conf.d/
USER worker

WORKDIR /var/www
