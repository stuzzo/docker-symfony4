ARG PHP_VERSION
FROM php:${PHP_VERSION}-fpm

MAINTAINER Alfredo <stuzzo> AIELLO <stuzzo@gmail.com>

#############################################################
################### COMMANDS AS ROOT ########################
#############################################################

# Add system programs
RUN apt-get update && apt-get install -y \
    git \
    unzip \
    nano

# Set timezone
ARG TIMEZONE
RUN rm /etc/localtime && ln -s /usr/share/zoneinfo/${TIMEZONE} /etc/localtime && echo ${TIMEZONE} > /etc/timezone

# Type docker-php-ext-install to see available extensions
RUN apt-get install -y \
		libfreetype6-dev \
		libjpeg62-turbo-dev \
		libmcrypt-dev \
		libpng12-dev \
	&& docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
	&& docker-php-ext-install -j$(nproc) gd iconv mcrypt pdo_mysql

# Install globally PHP-CS-FIXER
RUN curl -L http://cs.sensiolabs.org/download/php-cs-fixer-v2.phar -o php-cs-fixer
RUN chmod a+x php-cs-fixer
RUN mv php-cs-fixer /usr/local/bin/php-cs-fixer

# Install xdebug and set to use port 9000
RUN pecl install xdebug
RUN docker-php-ext-enable xdebug
RUN echo "error_reporting = E_ALL" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "display_startup_errors = On" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "display_errors = On" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "xdebug.remote_enable=1" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "xdebug.remote_connect_back=1" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "xdebug.idekey=\"PHPSTORM\"" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
RUN echo "xdebug.remote_port=9000" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

# Add user's project
ARG PROJECT_USER
RUN mkdir -p /home/${PROJECT_USER} /home/${PROJECT_USER}/.ssh
RUN useradd -d /home/${PROJECT_USER} --user-group --shell /bin/bash --uid ${PROJECT_USER} ${PROJECT_USER}
RUN chown -R ${PROJECT_USER}: /home/${PROJECT_USER} /var/www/${PROJECT_NAME}
RUN su ${PROJECT_USER}

#############################################################
################### COMMANDS AS USER ########################
#############################################################

# Install Composer only for user's project to avoid run composer as root
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN composer global require "hirak/prestissimo:^0.3"

# Add some usuful bash tips
ARG PROJECT_NAME
RUN echo "alias sf4=\"php /var/www/${PROJECT_NAME}/bin/console\"" >> /home/${PROJECT_USER}/.bashrc
RUN echo 'alias ll="ls -alF"' >> /home/${PROJECT_USER}/.bashrc
RUN echo 'alias la="ls -A"' >> /home/${PROJECT_USER}/.bashrc
RUN echo 'alias l="ls -CF"' >> /home/${PROJECT_USER}/.bashrc

WORKDIR /var/www/${PROJECT_NAME}