FROM ubuntu:20.04

MAINTAINER Chitushka <chitushka@yahoo.com>

ENV DEBIAN_FRONTEND noninteractive
ENV TZ=Europe/Moscow

ENV V=7.4

# Обновление пакетов ОС
RUN set -eux; echo 'Package update:'
RUN set -eux; apt-get update; apt-get upgrade -y

# Устанавлизаем временную зону
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Установка необходимых пакетов
RUN set -eux; echo 'Package install:'
RUN set -eux; apt-get install -y apt-utils autoconf automake bison build-essential flex gcc git pkg-config re2c vim wget curl cmake
RUN set -eux; apt-get install -y freeglut3 freeglut3-dev

# Java - https://openjdk.org
RUN set -eux; apt-get install -y default-jdk # 11 - version
#RUN set -eux; apt-get install -y openjdk-17-jdk
RUN set -eux; apt-get install -y maven

# Rust Language - https://rust-lang.org
# curl --proto '=https' --tlsv1.3 https://sh.rustup.rs -sSf | sh
RUN set -eux; apt-get install -y rustc 

# PHP - https://php.net
RUN set -eux; apt-get install -y php$V php$V-fpm php$V-common php$V-mysql php$V-odbc php$V-pgsql php$V-sqlite3 php$V-xml php$V-intl php$V-curl php$V-gd php$V-cli php$V-mbstring php$V-zip php$V-soap php$V-imagick
RUN set -eux; apt-get install -y apache2

# Dart language - https://dart.dev
RUN set -eux; wget https://storage.googleapis.com/dart-archive/channels/stable/release/latest/linux_packages/dart_2.18.7-1_amd64.deb; dpkg -i dart_2.18.7-1_amd64.deb

# Free Pascal - https://www.freepascal.org
RUN set -eux; apt-get install -y fpc

# Node JS - https://nodejs.org
RUN set -eux; apt-get install -y npm

# The Julia Programming Language - https://julialang.org
RUN set -eux; apt-get install -y julia

WORKDIR /home

# EXPOSE 80

CMD ["bash"]
