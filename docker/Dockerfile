FROM node:22-alpine3.20 as npm-build

COPY . .

RUN npm install \
    && npm install sharp

RUN npm run build .

FROM alpine:3.20

RUN apk add --no-cache bash curl git \
    less tzdata zip gettext sed \
    nginx certbot-nginx ca-certificates \
    supervisor

RUN mkdir -p /run/nginx

# copy configuration files
COPY docker/default.conf /etc/nginx/http.d/default.conf
COPY docker/nginx.conf /etc/nginx/nginx.conf

COPY --chmod=755 docker/init_cert.sh /usr/local/init_cert.sh
COPY --chmod=755 docker/init_react_envs.sh /usr/local/init_react_envs.sh

# configure supervisor
COPY docker/supervisord.conf /etc/supervisord.conf
# copy build files
COPY --from=npm-build out/ /usr/share/nginx/html/

WORKDIR /usr/share/nginx/html

STOPSIGNAL SIGINT
ENTRYPOINT ["/usr/bin/supervisord","-c","/etc/supervisord.conf"]