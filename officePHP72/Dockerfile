FROM php:7.2.3-fpm
#先更新下已安装的
#RUN apt-get update \
	 # 相关依赖必须手动安装
	 RUN apt-get install -y libfreetype6-dev libjpeg62-turbo-dev    libmcrypt-dev  libpng-dev \
	 #php的编译配置
     && docker-php-ext-configure   --prefix=/usr/local/php \
	 --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
	 --enable-fpm --with-mcrypt --enable-mbstring  \ 
	 --enable-inline-optimization --with-bz2  --with-zlib --enable-sockets  \
	 --enable-sysvsem --enable-sysvshm --enable-pcntl --enable-mbregex  \
	 --with-mhash --enable-zip --with-pcre-regex --with-mysqli  \
	 --with-gd --with-jpeg-dir --with-freetype-dir --with-openssl --with-pcre-regex --with-pdo-mysql \
	 --enable-opcache --enable-soap --enable-xml \
	 #php的扩展安装
	 && docker-php-ext-install -j$(nproc) gd opcache  soap mcrypt
	

#配置文件目录
COPY  php.ini /usr/local/etc/php/
COPY  web      /data/web
CMD ["php-fpm"]
