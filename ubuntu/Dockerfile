FROM ubuntu:18.04

RUN apt-get update --fix-missing && apt-get install -y software-properties-common language-pack-en-base \
    && LC_ALL=en_US.UTF-8 add-apt-repository -y ppa:ondrej/php \
    && LC_ALL=en_US.UTF-8 apt-add-repository -y ppa:nginx/stable \
    && LC_ALL=en_US.UTF-8 apt-add-repository -y universe \
    && apt-get update --fix-missing -y

ADD install_tzdata.sh /install_tzdata.sh
RUN sh /install_tzdata.sh

# install php7.2
RUN apt-get update --fix-missing -y && apt-get install -y php7.2 php7.2-fpm php7.2-mysql php7.2-gd curl vim \
       php7.2-curl php-redis php7.2-mbstring sendmail supervisor php-memcached \
       php7.2-tidy php7.2-xml php7.2-zip \
    && mkdir /run/php

# install mcrypt
RUN apt-get install -y re2c php7.2-dev libmcrypt-dev
RUN pecl install mcrypt-1.0.1

# modify config files
RUN sed -i -e 's/;daemonize = yes/daemonize = no/g' /etc/php/7.2/fpm/php-fpm.conf

# php: config mcrypt module
RUN echo 'extension = mcrypt.so' >> /etc/php/7.2/mods-available/mcrypt.ini \
    && phpenmod mcrypt

# install nginx
RUN apt-get install -y nginx;
# remove defaults and map customized config files
RUN rm -Rf /etc/nginx/nginx.conf
ADD nginx/nginx.conf /etc/nginx/nginx.conf
ADD nginx/conf/custom-default.conf /etc/nginx/conf.d/default.conf

RUN mkdir -p /etc/nginx/sites-enabled/ 
RUN rm -Rf /var/www/* && mkdir -p /var/www/html/

ADD src/ /var/www/html/
VOLUME /var/log/nginx;


# copy customized config files
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf
# COPY www-env.conf /etc/php/7.2/fpm/pool.d/www-env.conf


WORKDIR /var/www/html

EXPOSE 80 443

CMD ["/usr/bin/supervisord"]