FROM ubuntu:20.04

MAINTAINER Chitushka <chitushka@yahoo.com>

ENV DEBIAN_FRONTEND noninteractive
ENV TZ=Europe/Moscow

# Обновление пакетов ОС
RUN set -eux; echo 'Package update:'
RUN set -eux; apt-get update; apt-get upgrade -y

# Устанавлизаем временную зону
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Установка необходимых пакетов
RUN set -eux; echo 'Package install:'
RUN set -eux; apt-get install -y apt-utils autoconf automake bison build-essential flex gcc git pkg-config re2c vim wget curl cmake

CMD ["bash"]
