FROM ubuntu:latest

RUN apt update && apt -y upgrade

WORKDIR /app
#
# Apache configuration block
#
RUN apt install -y apache2 apache2-utils php libapache2-mod-php php-cli gettext

COPY ./apache2 /etc/apache2

COPY ./apchhtml ./apache

ENTRYPOINT ["/bin/bash","-c","envsubst < /etc/apache2/ports.conf.template > /etc/apache2/ports.conf & envsubst < /etc/apache2/sites-available/000-default.conf.template > /etc/apache2/sites-available/000-default.conf & apache2ctl -D FOREGROUND"]

