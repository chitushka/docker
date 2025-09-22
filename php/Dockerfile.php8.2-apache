FROM php:8.2-apache  

MAINTAINER Chitushka <chitushka@yahoo.com>

RUN apt update && apt install --no-install-recommends -y wget unzip curl git vim \
libzip-dev \
libpq-dev \
libpng-dev \
libfreetype6-dev libjpeg62-turbo-dev \
libicu-dev \
libldap2-dev \
libaio1 \
libxml2-dev \
unixodbc-dev unixodbc \
libsmbclient libsmbclient-dev \
libxslt-dev \
libengine-gost-openssl \
libc-client-dev libkrb5-dev \
libtidy-dev \
libffi-dev \
libfbclient2 \
firebird-dev \
libpspell-dev \
libmagickwand-dev

RUN rm -rf /var/lib/apt/lists/*

RUN docker-php-ext-install zip pdo pdo_mysql mysqli pdo_pgsql pgsql bcmath iconv intl ldap soap sockets opcache pcntl sysvmsg sysvsem sysvshm tidy shmop exif ffi calendar xsl gettext pspell

RUN docker-php-ext-configure gd --with-freetype --with-jpeg \
 && docker-php-ext-install gd

# Устанавливаем Oracle InstantClient и расширение OCI8
RUN mkdir /tmp/oracle \
 && mkdir /opt/oracle

COPY oracle/*.zip /tmp/oracle/

RUN unzip /tmp/oracle/instantclient-basic-linux.x64-21.11.0.0.0dbru.zip -d /opt/oracle \
 && unzip /tmp/oracle/instantclient-sqlplus-linux.x64-21.11.0.0.0dbru -d /opt/oracle \
 && unzip /tmp/oracle/instantclient-sdk-linux.x64-21.11.0.0.0dbru.zip -d /opt/oracle

RUN rm -rf /tmp/oracle/

RUN mv /opt/oracle/instantclient_21_11 /opt/oracle/instantclient \
 && echo /opt/oracle/instantclient > /etc/ld.so.conf.d/oci.conf \
 && ldconfig \
 && docker-php-ext-configure pdo_oci --with-pdo-oci=instantclient,/opt/oracle/instantclient \
 && docker-php-ext-install pdo_oci

RUN echo 'instantclient,/opt/oracle/instantclient' | pecl install oci8 \
 && docker-php-ext-enable oci8

RUN pecl install sqlsrv \
 && docker-php-ext-enable sqlsrv

RUN pecl install pdo_sqlsrv \
 && docker-php-ext-enable pdo_sqlsrv

RUN docker-php-ext-configure pdo_odbc --with-pdo-odbc=unixODBC,/usr \
 && docker-php-ext-install pdo_odbc

RUN pecl install smbclient \
 && docker-php-ext-enable smbclient

RUN pecl install mongodb \
 && docker-php-ext-enable mongodb

# Устанавливаем IBM MQ
RUN mkdir /tmp/ibmmq \
 && mkdir /tmp/php_pecl

COPY ibmmq/*.tar.gz /tmp/ibmmq/

RUN cd /tmp/ibmmq \
 && tar -xzf /tmp/ibmmq/9.3.3.1-IBM-MQC-UbuntuLinuxX64.tar.gz \
 && cd MQClient \
 && ./mqlicense.sh -accept \
 && dpkg -i ibmmq-runtime_9.3.3.1_amd64.deb \
 && dpkg -i ibmmq-sdk_9.3.3.1_amd64.deb \
 && dpkg -i ibmmq-gskit_9.3.3.1_amd64.deb \
 && dpkg -i ibmmq-client_9.3.3.1_amd64.deb \
 && dpkg -i ibmmq-msg-ru_9.3.3.1_amd64.deb

COPY ibmmq/*.tgz /tmp/php_pecl/

RUN cd /tmp/php_pecl \
 && tar -zxf mqseries-0.15.0.tgz \
 && cd mqseries-0.15.0/mqseries-0.15.0 \
 && phpize \
 && ./configure --with-libdir=lib64 \
 && make \
 && make install \
 && echo 'extension=mqseries.so' > /usr/local/etc/php/conf.d/mqseries.ini

RUN rm -rf /tmp/ibmmq/ \
 && rm -rf /tmp/php_pecl/

# Установка ГОСТ алгоритмов в openssl
ARG PREFIX_SSL="/etc/ssl"
RUN sed -i '74i openssl_conf=openssl_def' ${PREFIX_SSL}/openssl.cnf \
 && echo "" >> ${PREFIX_SSL}/openssl.cnf \
 && echo "# OpenSSL default section" >> ${PREFIX_SSL}/openssl.cnf \
 && echo "[openssl_def]" >> ${PREFIX_SSL}/openssl.cnf \
 && echo "engines = engine_section" >> ${PREFIX_SSL}/openssl.cnf \
 && echo "" >> ${PREFIX_SSL}/openssl.cnf \
 && echo "# Engine scetion" >> ${PREFIX_SSL}/openssl.cnf \
 && echo "[engine_section]" >> ${PREFIX_SSL}/openssl.cnf \
 && echo "gost = gost_section" >> ${PREFIX_SSL}/openssl.cnf \
 && echo "" >> ${PREFIX_SSL}/openssl.cnf \
 && echo "# Engine gost section" >> ${PREFIX_SSL}/openssl.cnf \
 && echo "[gost_section]" >> ${PREFIX_SSL}/openssl.cnf \
 && echo "engine_id = gost" >> ${PREFIX_SSL}/openssl.cnf \
 && echo "dynamic_path = /usr/lib/x86_64-linux-gnu/engines-3/gost.so" >> ${PREFIX_SSL}/openssl.cnf \
 && echo "default_algorithms = ALL" >> ${PREFIX_SSL}/openssl.cnf \
 && echo "CRYPT_PARAMS = id-Gost28147-89-CryptoPro-A-ParamSet" >> ${PREFIX_SSL}/openssl.cnf \
 && echo "" >> ${PREFIX_SSL}/openssl.cnf


RUN pecl install igbinary \
 && docker-php-ext-enable igbinary

RUN echo 'yes' | echo 'yes' | echo 'no' | pecl install redis \
 && docker-php-ext-enable redis


RUN docker-php-ext-configure imap --with-kerberos --with-imap-ssl \
 && docker-php-ext-install imap

RUN echo 'no' | pecl install apcu \
 && docker-php-ext-enable apcu

RUN wget https://github.com/FirebirdSQL/php-firebird/releases/download/v3.0.1/php_8.2.0-interbase-3.0.1-linux-x64.so -O /usr/local/lib/php/extensions/no-debug-non-zts-20220829/interbase.so \
 && echo 'extension=interbase.so' > /usr/local/etc/php/conf.d/interbase.ini \
 && docker-php-ext-install pdo_firebird

RUN echo 'yes' | pecl install memcache \
 && docker-php-ext-enable memcache

RUN pecl install imagick \
 && docker-php-ext-enable imagick

# Установка composer
RUN curl -o composer-setup.php http://getcomposer.org/installer; php composer-setup.php; mv composer.phar /usr/local/bin/composer; rm composer-setup.php

RUN rm -rf /tmp/pear
