FROM ubuntu:20.04

RUN apt-get update && \
    apt-get -y install sudo dialog apt-utils

RUN echo 'debconf debconf/frontend select Noninteractive' | debconf-set-selections
RUN sudo \
    echo "postfix postfix/mailname string aktiva.co.id" | debconf-set-selections && \
    echo "postfix postfix/main_mailer_type string 'Internet Site'" | debconf-set-selections

#update the apt package manager
RUN apt-get update

#install the package that allows us to add the PHP repository
RUN apt-get install software-properties-common -y --no-install-recommends
RUN add-apt-repository ppa:ondrej/php

#add the nodejs, supervisor, nginx, npm package repostory
RUN apt-get update \
    && apt-get install -y curl vim zip unzip git supervisor nginx \
    php7.4-fpm php7.4-cli php7.4-curl php7.4-fileinfo php7.4-gd \
    php7.4-mysql php7.4-mbstring php7.4-xml php7.1-mcrypt \
    && php -r "readfile('http://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer


ARG WITH_XDEBUG=false
RUN if [ $WITH_XDEBUG = "true" ] ; then \
    apt-get install php7.4-xdebug; \
        echo "xdebug.mode=debug" >> 	/etc/php/7.4/fpm/conf.d/docker-php-ext-xdebug.ini; \
        echo "xdebug.client_host = host.docker.internal" >> /etc/php/7.4/fpm/conf.d/docker-php-ext-xdebug.ini; \
    fi;


RUN apt-get -y autoremove \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# PHP conf
RUN mkdir /run/php
RUN chmod 777 -R /etc/php/7.4/fpm/
ADD docker/php-fpm/www.conf /etc/php/7.4/fpm/pool.d/www.conf
ADD docker/php-fpm/php-fpm.conf /etc/php/7.4/fpm/php-fpm.conf
ADD docker/php-fpm/php.ini /etc/php/7.4/fpm/php.ini
ADD docker/php-fpm/supervisord.conf /etc/supervisor/conf.d/supervisord.conf
ADD docker/nginx/default /etc/nginx/sites-available/default

WORKDIR /var/www/html

COPY . .

RUN composer install

RUN chown www-data:www-data -R .

EXPOSE 80

CMD ["supervisord"]
