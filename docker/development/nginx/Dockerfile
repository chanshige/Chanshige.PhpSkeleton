FROM nginxinc/nginx-unprivileged:alpine
LABEL maintainer="dev@shigeki.tokyo"

ADD ./docker/development/nginx/ssl /etc/nginx/ssl
COPY ./docker/development/nginx/nginx.conf /etc/nginx/
COPY ./docker/development/nginx/default.conf /etc/nginx/conf.d/

EXPOSE 8080
EXPOSE 8443
