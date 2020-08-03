FROM node:8-alpine

MAINTAINER JamesAdams <jamesadams@noemail.com>

ARG VERSION

ENV DAEMON_VERSION=v0.6.13 \
    UID=500
    
ENV STARTUP_TIMEOUT=5 \
    PANEL_VERSION=v0.7.18

WORKDIR /srv/daemon

RUN \
 apk add zip \
 && apk add unzip \
 && apk --update --no-cache add coreutils curl openssl make gcc g++ python gnupg tar \
 && curl -Lo daemon.tar.gz https://github.com/Pterodactyl/Daemon/archive/${DAEMON_VERSION}.tar.gz \
 && tar --strip-components=1 -xzvf daemon.tar.gz \
 && rm -f daemon.tar.gz \
 && npm install --production \
 && addgroup -S -g ${UID} pterodactyl && adduser -S -D -H -G pterodactyl -u ${UID} -s /bin/false pterodactyl \
 && apk del curl make gcc g++ python gnupg \
 && rm -rf /root/.npm /root/.node-gyp /root/.gnupg \
 /var/cache/apk/* /tmp/*
 
 
WORKDIR /var/www/html

RUN \
 apk --update add curl gettext nginx php7 php7 php7-bcmath php7-common php7-dom php7-fileinfo \
 php7-fpm php7-gd php7-memcached php7-mbstring php7-openssl php7-pdo php7-phar php7-json \
 php7-pdo_mysql php7-session php7-simplexml php7-tokenizer php7-ctype php7-zlib php7-zip tini \
 && mkdir -p /var/www/html /run/nginx


RUN curl -Lo panel.tar.gz https://github.com/Pterodactyl/Panel/archive/v0.6.13.tar.gz
RUN tar --strip-components=1 -xzvf panel.tar.gz 
RUN rm panel.tar.gz 
RUN chmod -R 755 storage/* bootstrap/cache 
RUN find storage -type d > .storage.tmpl 
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer 
RUN cp .env.example .env 
RUN composer install --ansi --no-dev 
RUN rm .env ./storage -rf 
RUN chown nginx:nginx * -R

COPY ./ /

VOLUME [ "/data" ]

EXPOSE 8080 80 443

ENTRYPOINT [ "/sbin/tini", "--", "/entrypoint.sh" ]

CMD [ "p:start" ]

CMD [ "npm", "start" ]



