#GET ARGUEMENTS FROM MASTER DOCKER COMPOSE FILE
ARG IMAGE_VERSION

#USE IMAGE
FROM php:$IMAGE_VERSION

#DO IF ANYTHING NEEDS TO BE INSTALLED
RUN apt-get update

# 1. development packages
RUN apt-get install -y \
    git \
    zip \
    curl \
    sudo \
    unzip \
    libicu-dev \
    libbz2-dev \
    libpng-dev \
    libjpeg-dev \
    libmcrypt-dev \
    libreadline-dev \
    libfreetype6-dev \
    g++

ENV APACHE_DOCUMENT_ROOT=/var/www/html/public
RUN sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/sites-available/*.conf
RUN sed -ri -e 's!/var/www/!${APACHE_DOCUMENT_ROOT}!g' /etc/apache2/apache2.conf /etc/apache2/conf-available/*.conf


# 2. mod_rewrite for URL rewrite and mod_headers for .htaccess extra headers like Access-Control-Allow-Origin-
RUN a2enmod rewrite headers


# 3. php exenttions
RUN docker-php-ext-install \
    bz2 \
    intl \
    iconv \
    bcmath \
    opcache \
    pdo_mysql \

# 5. composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# 6. we need a user with the same UID/GID with host user
# so when we execute CLI commands, all the host file's ownership remains intact
# otherwise command from inside container will create root-owned files and directories
ARG UID
RUN useradd -G www-data,root -u $UID -d /home/devuser devuser
RUN mkdir -p /home/devuser/.composer && \
    chown -R devuser:devuser /home/devuser

#SET WORK DIRECTORY
WORKDIR /var/www/html

#COPY PROJECT FILES TO THE VERTUAL PATH AFTER WORK DIRECTORY
COPY . .

#DEFINE PORT TO RUN
EXPOSE 8000
#CMD ["apachectl", "-D", "FOREGROUND"]
CMD cd /var/www/html
CMD php artisan serve --host 0.0.0.0
