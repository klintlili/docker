FROM centos:6.9
#指定centos:6.9来安装php，去掉6.9默认就是:latest最新的镜像
MAINTAINER by masterliu
WORKDIR /usr/local/src

#设置PHP版本，最好去这些网站看看还维护不
ENV VERSION="7.1.13"

#安装wget,更换国内源下载php并解压
RUN yum install -y  wget  \
	&& wget -O php-$VERSION.tar.gz http://cn2.php.net/get/php-$VERSION.tar.gz/from/this/mirror  \
	&& tar -zxvf php-$VERSION.tar.gz


RUN mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup \
	&&  wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo \
	&& yum clean all \
	&& yum makecache

#去解压目录编译安装
WORKDIR php-$VERSION

#php-mcrypt  libmcrypt  libmcrypt-devel这些依赖yum直接安装不了，使用了第三方的yum源atomic才安装的
RUN wget http://www.atomicorp.com/installers/atomic \
&&  sh ./atomic
RUN yum install -y gcc make libxml2 libxml2-devel curl curl-devel \
	  openssl openssl-devel bzip2 bzip2-devel libjpeg libjpeg-devel \
	 freetype freetype-devel libpng libpng-devel php-mcrypt  libmcrypt  libmcrypt-devel \
         &&  ./configure   --prefix=/usr/local/php \
	 --enable-fpm --with-mcrypt --enable-mbstring  \ 
	 --enable-inline-optimization --with-bz2  --with-zlib --enable-sockets  \
	 --enable-sysvsem --enable-sysvshm --enable-pcntl --enable-mbregex  \
	 --with-mhash --enable-zip --with-pcre-regex --with-mysqli  \
	 --with-gd --with-jpeg-dir --with-freetype-dir --with-openssl --with-pcre-regex --with-pdo-mysql \
	 --enable-opcache --enable-soap --enable-xml \
	&& make && make install \
	#删除安装包
	&& rm -rf /usr/local/src/

#配置文件目录
COPY  php-fpm.conf /usr/local/php/etc/
COPY  php-fpm.d /usr/local/php/etc/php-fpm.d/
COPY  web      /data/web

#EXPOSE 9000
#php-fpm默认是后台，使用-F强制前台运行
ENTRYPOINT ["/usr/local/php/sbin/php-fpm"]
