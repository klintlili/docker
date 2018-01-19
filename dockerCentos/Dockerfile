FROM centos
MAINTAINER by masterliu
WORKDIR /usr/local/src

#设置nginx版本
ENV VERSION="1.12.2"

#安装wget,下载nginx并解压
RUN yum install -y  wget \
	&& wget http://nginx.org/download/nginx-$VERSION.tar.gz  \
	&& tar -zxvf nginx-$VERSION.tar.gz

#去解压目录编译安装
WORKDIR nginx-$VERSION

#编译安装nginx
RUN yum install -y gcc make pcre-devel zlib-devel \
	&& ./configure   --prefix=/usr/local/nginx   --with-pcre \
	&& make && make install \
	#删除安装包
	&& rm -rf /usr/local/src/

#配置文件目录
COPY  nginx.conf /usr/local/nginx/conf/
COPY  conf.d /usr/local/nginx/conf/conf.d/
COPY  web  /data/web

#docker中的服务，都不要在后台运行
#RUN /usr/local/nginx/sbin/nginx -g "daemon off;"

#EXPOSE 22
EXPOSE 80

#最后的CMD  entrypoint指令不可少，-g设置全局指令,使得所有的nginx配置文件都生效
ENTRYPOINT ["/usr/local/nginx/sbin/nginx","-g","daemon off;"]

