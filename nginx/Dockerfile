ARG NGINX_VERSION=${PHP_VERSION}
FROM nginx:${NGINX_VERSION}

ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN rm /etc/nginx/nginx.conf
COPY ./conf/nginx.conf /etc/nginx/nginx.conf
RUN mkdir /etc/nginx/vhost
COPY ./conf/vhost /etc/nginx/vhost

ARG PHP_UPSTREAM_CONTAINER=php-fpm
ARG PHP_UPSTREAM_PORT=9000

# Set upstream conf and remove the default conf
RUN echo "upstream php-upstream { server ${PHP_UPSTREAM_CONTAINER}:${PHP_UPSTREAM_PORT}; }" > /etc/nginx/conf.d/upstream.conf \
    && rm /etc/nginx/conf.d/default.conf

ARG APP_CODE_PATH_CONTAINER=/var/www
WORKDIR ${APP_CODE_PATH_CONTAINER}

CMD ["nginx"]

EXPOSE 80