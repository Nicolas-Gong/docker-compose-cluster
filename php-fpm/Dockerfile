ARG PHP_VERSION=${PHP_VERSION}
FROM php:${PHP_VERSION}

# Timezone
ARG TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo ${TZ} > /etc/timezone

# Libs
RUN apt-get update \
    && apt-get install -y \
    curl \
    wget \
    cron \
    procps \
    libz-dev \
    libssl-dev \
    libnghttp2-dev \
    libpcre3-dev \
    && pecl channel-update pecl.php.net \
    && apt-get clean \
    && apt-get autoremove

# Ext
ARG INSTALL_SOAP=false
RUN if [ ${INSTALL_SOAP} = true ]; then \
    apt-get -y install libxml2-dev && \
    docker-php-ext-install soap \
    ;fi

ARG INSTALL_EXIF=false
RUN if [ ${INSTALL_EXIF} = true ]; then \
    docker-php-ext-install exif \
    ;fi

ARG INSTALL_MCRYPT=false
RUN if [ ${INSTALL_MCRYPT} = true ]; then \
    apt-get install -y libmcrypt-dev && \
    pecl install mcrypt-1.0.1 && \
    docker-php-ext-enable mcrypt \
    ;fi

ARG INSTALL_PDO_MYSQL=false
RUN if [ ${INSTALL_PDO_MYSQL} = true]; then \
    docker-php-ext-install pdo_mysql \
    ;fi

ARG INSTALL_BCMATH=false
RUN if [ ${INSTALL_BCMATH} = true ]; then \
    docker-php-ext-install bcmath \
    ;fi

ARG INSTALL_GHOSTSCRIPT=false
RUN if [ ${INSTALL_GHOSTSCRIPT} = true ]; then \
    apt-get install -y \
    poppler-utils \
    zlib1g-dev \
    libpng-dev libxpm-dev \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    ghostscript && \
    docker-php-ext-configure gd \
    --with-gd \
    --with-jpeg-dir \
    --with-png-dir \
    --with-zlib-dir \
    --with-xpm-dir \
    --with-freetype-dir \
    --enable-gd-native-ttf && \
    docker-php-ext-install gd \
    ;fi

ARG INSTALL_IMAGEMAGICK=false
RUN if [ ${INSTALL_IMAGEMAGICK} = true ]; then \
    apt-get install -y libmagickwand-dev imagemagick && \
    pecl install imagick && \
    docker-php-ext-enable imagick \
    ;fi

ARG INSTALL_INTL=false
RUN if [ ${INSTALL_INTL} = true ]; then \
    docker-php-ext-configure intl && \
    docker-php-ext-install intl \
    ;fi

ARG INSTALL_MYSQLI=false
RUN if [ ${INSTALL_MYSQLI} = true ]; then \
    docker-php-ext-install mysqli \
    ;fi

ARG INSTALL_ZIP_ARCHIVE=false
RUN if [ ${INSTALL_ZIP_ARCHIVE} = true ]; then \
    docker-php-ext-install zip \
    ;fi

ARG INSTALL_OPCACHE=false
RUN if [ ${INSTALL_OPCACHE} = true ]; then \
    docker-php-ext-install opcache \
    ;fi

ARG INSTALL_REDIS=false
ARG PHPREDIS_VERSION=4.3.0
RUN if [ ${INSTALL_REDIS} = true ]; then \
    wget http://pecl.php.net/get/redis-${PHPREDIS_VERSION}.tgz -O /tmp/redis.tar.tgz \
    && pecl install /tmp/redis.tar.tgz \
    && rm -rf /tmp/redis.tar.tgz \
    && docker-php-ext-enable redis \
    ;fi

ARG INSTALL_SWOOLE=false
ARG SWOOLE_VERSION=4.3.3
RUN if [ ${INSTALL_SWOOLE} = true ]; then \
    wget https://github.com/swoole/swoole-src/archive/v${SWOOLE_VERSION}.tar.gz -O swoole.tar.gz \
    && mkdir -p swoole \
    && tar -xf swoole.tar.gz -C swoole --strip-components=1 \
    && rm swoole.tar.gz \
    && ( \
    cd swoole \
    && phpize \
    && ./configure --enable-async-redis --enable-mysqlnd --enable-openssl --enable-http2 \
    && make -j$(nproc) \
    && make install \
    ) \
    && rm -r swoole \
    && docker-php-ext-enable swoole \
    ;fi

COPY ./conf/opcache.ini /usr/local/etc/php/conf.d/opcache.ini
#COPY ./conf/tp.ini /usr/local/etc/php/conf.d
#RUN rm /usr/local/etc/php-fpm.d/www.conf
#COPY ./conf/php-fpm.d /usr/local/etc/php-fpm.d
#RUN rm /usr/local/etc/php-fpm.conf
#COPY ./conf/php-fpm.conf /usr/local/etc/php-fpm.conf

ARG APP_CODE_PATH_CONTAINER=/var/www
WORKDIR ${APP_CODE_PATH_CONTAINER}

CMD ["php-fpm"]

EXPOSE 9000