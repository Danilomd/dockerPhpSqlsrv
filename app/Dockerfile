FROM --platform=linux/amd64 ubuntu:20.04 

# Configurando timezone
ENV TZ=America/Sao_Paulo
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apt-get update && \
    apt-get install --yes curl ca-certificates 

# Adicionando repositório do PHP

RUN apt-get update && \
    apt-get install --yes software-properties-common && \
    LC_ALL=C.UTF-8 add-apt-repository ppa:ondrej/php

# Instalando PHP + APACHE

RUN apt-get update && \
    apt-get install --yes php8.0 php8.0-dev

# Configurando APACHE

RUN a2enmod rewrite
COPY allow-override.conf /etc/apache2/conf-enabled/allow-override.conf
COPY vhost.conf /etc/apache2/sites-enabled/000-default.conf

# Drivers do SQL Server
RUN curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add - \
    && curl https://packages.microsoft.com/config/ubuntu/20.04/prod.list > /etc/apt/sources.list.d/mssql-release.list
RUN apt-get update && ACCEPT_EULA=Y apt-get install -y msodbcsql17 unixodbc-dev

RUN pecl install sqlsrv
RUN pecl install pdo_sqlsrv
RUN printf "; priority=20\nextension=sqlsrv.so\n" > /etc/php/8.0/mods-available/sqlsrv.ini
RUN printf "; priority=30\nextension=pdo_sqlsrv.so\n" > /etc/php/8.0/mods-available/pdo_sqlsrv.ini
RUN phpenmod -v 8.0 sqlsrv pdo_sqlsrv

# XDEBUG
RUN pecl install xdebug-3.0.4 
COPY xdebug.ini /etc/php/8.0/apache2/conf.d/90-xdebug.ini
COPY xdebug.ini /etc/php/8.0/cli/conf.d/90-xdebug.ini

# php.ini
COPY php.ini /etc/php/8.0/cli/conf.d/php.ini
COPY php.ini /etc/php/8.0/apache2/conf.d/php.ini

# SSL
COPY openssl.cnf /etc/ssl/openssl.cnf

EXPOSE 80

CMD ["apache2ctl", "-DFOREGROUND"]