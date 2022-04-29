FROM php:7.3-apache
# FROM php:7.1.20-apache

ARG USER_ID=1000
ARG GROUP_ID=1000

RUN userdel -f www-data &&\
    if getent group www-data ; then groupdel www-data; fi
RUN groupadd -g ${GROUP_ID} www-data &&\
    useradd -l -u ${USER_ID} -g www-data www-data;
RUN install -d -m 0755 -o www-data -g www-data /home/www-data
RUN chown -R --from=33:33 ${USER_ID}:${GROUP_ID} /home/www-data
RUN chmod -R 777 /var/www/html

# RUN apt-get update && apt-get install -y apt-transport-https

RUN apt-get -y update --fix-missing
RUN apt-get upgrade -y

# Install useful tools
RUN apt-get -y install apt-utils nano wget dialog

# Install important libraries
RUN apt-get -y install --fix-missing apt-utils build-essential git curl libcurl4 libcurl3-dev zip openssl

# Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Install xdebug
RUN pecl install xdebug-2.9.6
RUN docker-php-ext-enable xdebug 

# sendmail config
#################
# install
RUN apt-get install -q -y msmtp mailutils
# config
COPY msmtprc /etc/msmtprc
RUN chmod 600 /etc/msmtprc
RUN chown www-data:www-data /etc/msmtprc
COPY msmtp.log /var/log/msmtp.log
RUN chown www-data:www-data /var/log/msmtp.log
RUN chmod 777 /var/log/msmtp.log
# Set up php sendmail config
RUN echo "sendmail_path=/usr/bin/msmtp -t" >> /usr/local/etc/php/conf.d/php-sendmail.ini


# Install redis
RUN pecl install redis-4.0.1
RUN docker-php-ext-enable redis

# Other PHP7 Extensions
# mysql-client
RUN apt-get -y install libsqlite3-0 libsqlite3-dev default-mysql-client
RUN apt-get install -y sqlite3 
RUN apt-get install -y libxslt-dev
RUN apt-get install -y libmcrypt-dev
# RUN apt-get install -y transport-https
# RUN apt-get install -y libmcrypt
RUN docker-php-ext-install bcmath
RUN docker-php-ext-install xsl
RUN docker-php-ext-install soap
RUN docker-php-ext-install pdo_mysql 
RUN docker-php-ext-install pdo_sqlite
RUN docker-php-ext-install mysqli
# mcrypt
# RUN docker-php-ext-install mcrypt-1.0.1 
RUN docker-php-ext-install sockets
RUN docker-php-ext-install curl
RUN docker-php-ext-install tokenizer
RUN docker-php-ext-install json

# zlib1g-dev
RUN apt-get -y install libzip-dev 
RUN docker-php-ext-install zip

RUN apt-get -y install libicu-dev
RUN docker-php-ext-install -j$(nproc) intl

RUN docker-php-ext-install mbstring
RUN docker-php-ext-install gettext

RUN apt-get install -y libfreetype6-dev libjpeg62-turbo-dev libpng-dev
RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ 
RUN docker-php-ext-install -j$(nproc) gd

# Enable apache modules
RUN a2enmod rewrite headers
