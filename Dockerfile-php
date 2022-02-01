FROM ubuntu:20.04

MAINTAINER Chitushka <chitushka@yahoo.com>

ARG DEBIAN_FRONTEND=noninteractive

ENV PHP php7.4
ENV PHP_INSTALL $PHP $PHP-mysqlnd $PHP-bcmath $PHP-redis $PHP-curl $PHP-dev $PHP-gd $PHP-intl $PHP-interbase $PHP-ldap $PHP-mbstring $PHP-odbc $PHP-pgsql $PHP-sqlite3 $PHP-xml $PHP-xmlrpc $PHP-soap $PHP-dba $PHP-zip $PHP-gmp $PHP-opcache $PHP-apcu
ENV PACKAGE_INSTALL apt-utils pkg-config sqlite3 curl unzip vim
ENV SAMBA_INSTALL samba-client libsmbclient libsmbclient-dev
ENV SQLSRV_INSTALL unixodbc-dev
ENV TZ=Europe/Moscow
ENV APACHE_CONFDIR /etc/apache2

# Обновление пакетов ОС
RUN set -eux; echo 'Package update:'
RUN set -eux; apt-get update; apt-get upgrade -y

# Устанавлизаем временную зону
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Установка необходимых пакетов
RUN set -eux; echo 'Package install:'
RUN set -eux; apt-get install -y $PACKAGE_INSTALL

# Установка php и php библиотек
RUN set -eux; echo 'PHP install:'
RUN set -eux; apt-get install -y $PHP_INSTALL

RUN set -eux; pecl update-channels

# Установка пакета samba и библиотеки smbclient для php
RUN set -eux; echo 'samba install:'
RUN set -eux; apt-get install -y $SAMBA_INSTALL
RUN set -eux; pecl install smbclient
RUN set -eux; echo 'extension=smbclient' > /etc/php/7.4/mods-available/smbclient.ini; \
	ln -s /etc/php/7.4/mods-available/smbclient.ini /etc/php/7.4/apache2/conf.d/20-smbclient.ini; \
	ln -s /etc/php/7.4/mods-available/smbclient.ini /etc/php/7.4/cli/conf.d/20-smbclient.ini

# Установка библиотеки для работы с БД MSSQL
RUN set -eux; echo 'SQLSRV install:'
RUN set -eux; apt-get install -y $SQLSRV_INSTALL
RUN set -eux; pecl install sqlsrv; pecl install pdo_sqlsrv
RUN set -eux; echo 'extension=sqlsrv.so' > /etc/php/7.4/mods-available/sqlsrv.ini; \
	echo 'extension=pdo_sqlsrv.so' > /etc/php/7.4/mods-available/pdo_sqlsrv.ini; \
	ln -s /etc/php/7.4/mods-available/sqlsrv.ini /etc/php/7.4/apache2/conf.d/20-sqlsrv.ini; \
	ln -s /etc/php/7.4/mods-available/pdo_sqlsrv.ini /etc/php/7.4/apache2/conf.d/20-pdo_sqlsrv.ini; \
	ln -s /etc/php/7.4/mods-available/sqlsrv.ini /etc/php/7.4/cli/conf.d/20-sqlsrv.ini; \
	ln -s /etc/php/7.4/mods-available/pdo_sqlsrv.ini /etc/php/7.4/cli/conf.d/20-pdo_sqlsrv.ini

# Установка расширения OCI8 для работы с Oracle
RUN set -eux; apt-get install libaio1; mkdir -p /opt/oracle; cd /opt/oracle; \
	curl -O https://download.oracle.com/otn_software/linux/instantclient/1912000/instantclient-sdk-linux.x64-19.12.0.0.0dbru.zip; \
	curl -O https://download.oracle.com/otn_software/linux/instantclient/1912000/instantclient-basic-linux.x64-19.12.0.0.0dbru.zip
RUN set -eux; cd opt/oracle; unzip instantclient-sdk-linux.x64-19.12.0.0.0dbru.zip; unzip instantclient-basic-linux.x64-19.12.0.0.0dbru.zip
RUN set -eux; \
	export ORACLE_HOME=/opt/oracle/instantclient_19_12; \
	echo "export LD_LIBRARY_PATH=$ORACLE_HOME" >> /etc/apache2/envvars; \
	echo "export ORACLE_HOME=$ORACLE_HOME" >> /etc/apache2/envvars; \
	echo "$ORACLE_HOME" > /etc/ld.so.conf.d/oracle-instantclient.conf; \
	ldconfig; \
	echo "LD_LIBRARY_PATH=$ORACLE_HOME" >> /etc/environment; \
	\	
	echo "instantclient,$ORACLE_HOME" | pecl install oci8-2.2.0; \
	echo 'extension=oci8.so' > /etc/php/7.4/mods-available/oci8.ini; \
	ln -s /etc/php/7.4/mods-available/oci8.ini /etc/php/7.4/apache2/conf.d/50-oci8.ini; \
	ln -s /etc/php/7.4/mods-available/oci8.ini /etc/php/7.4/cli/conf.d/50-oci8.ini

RUN set -eux; rm -rf /tmp/pear ~/.pearrc; rm -rf /var/lib/apt/lists/*

# Установка composer
RUN set -eux; curl -o composer-setup.php http://getcomposer.org/installer; php composer-setup.php; mv composer.phar /usr/local/bin/composer; rm composer-setup.php
RUN set -eux; php -m; php -v

# Запуск apache2
RUN set -eux; \
	export APACHE_RUN_DIR=/var/run/apache2; \
	echo "export APACHE_RUN_DIR=$APACHE_RUN_DIR" >> /etc/apache2/envvars; \
	echo "ServerName 127.0.0.1" >> /etc/apache2/apache2.conf; \
	. /etc/apache2/envvars

WORKDIR /var/www/html

EXPOSE 80

CMD ["apachectl", "-DFOREGROUND"]
