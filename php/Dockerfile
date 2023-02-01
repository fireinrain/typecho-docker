FROM php:7.4.29-fpm
MAINTAINER fireinrain
RUN apt-get update &&  \
    docker-php-ext-install pdo_mysql &&  \
    echo "output_buffering = 4096" > /usr/local/etc/php/conf.d/php.ini &&  \
    echo "date.timezone = PRC" >> /usr/local/etc/php/conf.d/php.ini
