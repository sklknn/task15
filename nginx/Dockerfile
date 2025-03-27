FROM ubuntu:latest

RUN apt update && apt -y upgrade

WORKDIR /app

RUN apt -y install curl gnupg2 ca-certificates lsb-release ubuntu-keyring

RUN curl https://nginx.org/keys/nginx_signing.key | gpg --dearmor \
    | tee /usr/share/keyrings/nginx-archive-keyring.gpg >/dev/null

RUN echo "deb [signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] \
http://nginx.org/packages/ubuntu `lsb_release -cs` nginx" \
    | tee /etc/apt/sources.list.d/nginx.list

RUN echo "Package: *\nPin: origin nginx.org\nPin: release o=nginx\nPin-Priority: 900\n" \
    | tee /etc/apt/preferences.d/99nginx

RUN apt update && apt -y install nginx nginx-module-image-filter php libapache2-mod-php php-cli gettext

RUN rm /etc/nginx/nginx.conf

RUN mkdir ssl

COPY ./cert .

RUN openssl req -x509 -config ./cert -nodes -days 365 -newkey rsa:2048 -keyout ./ssl/nginx-selfsigned.key -out ./ssl/nginx-selfsigned.crt

COPY ./ngxhtml ./nginx

COPY ./nginx /etc/nginx

ENTRYPOINT ["/bin/bash","-c","php -S 127.0.0.1:8001 -t nginx/blue & php -S 127.0.0.1:8000 -t nginx/red/ & envsubst < /etc/nginx/nginx.conf.template > /etc/nginx/nginx.conf & nginx -g 'daemon off;'"]

