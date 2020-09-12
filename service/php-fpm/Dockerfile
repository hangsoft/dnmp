FROM php:7.2-fpm

# 设置中国时区
RUN cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && \
    echo 'Asia/Shanghai' > /etc/timezone && \
    echo "date.timezone=Asia/Shanghai" >> /usr/local/etc/php/conf.d/timezone.ini

# 设置软件阿里源 并安装依赖
RUN cp /etc/apt/sources.list /etc/apt/sources.list.bak && \
    sed -i s@/deb.debian.org/@/mirrors.aliyun.com/@g /etc/apt/sources.list && \
    apt-get update && \
    apt-get install -y \
            libfreetype6-dev \
            libjpeg62-turbo-dev \
            libpng-dev \
            wget \
            zip \
            vim \
            git \
            supervisor

# 安装php扩展
RUN docker-php-source extract \
    && docker-php-ext-install bcmath pdo_mysql gd \
    && pecl install redis \
    && pecl install swoole \
    && docker-php-ext-enable redis swoole \
    && echo "swoole.use_shortname = 'Off'" >> /usr/local/etc/php/conf.d/docker-php-ext-swoole.ini

# 安装composer
RUN php -r "copy('https://install.phpcomposer.com/installer', 'composer-setup.php');" && \
    php composer-setup.php && \
    chmod u+x composer.phar && \
    mv composer.phar /usr/local/bin/composer && \
    composer config -g repo.packagist composer https://mirrors.aliyun.com/composer

# 启动supervisor
CMD ["supervisord","-n","-c","/etc/supervisor/supervisord.conf"]
