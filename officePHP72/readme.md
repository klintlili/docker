官方的php-fpm都是基于debian的，没见过centos或者Ubuntu的，因为对debian发行版不太熟悉，所以一开始并不喜欢使用官方的php镜像，而是基于官方centos自己写Dockerfile安装php。但是随着时间的推移发现这官方镜像centos上安装了php后镜像大小都超过了1G，有的1.5G。
在《docker从入门到实践》里看到镜像相关知识后才知道这么做镜像非常臃肿。于是不得不回来使用基于debian发行版的官方php镜像

# 安装扩展问题
使用官方fpm的容器时发现，竟然没有gd库和pdo_mysql驱动。哎，如何增加php扩展呢？
官方给出了docker-php-ext-configure,docker-php-ext-install两个工具，但是为了安装这些php扩展而需要的一些依赖需要自己解决

# 换源问题
用上述两个工具发现apt-get update真是慢得很，于是想更换国内源，可是当我搜索到阿里云和163的源时，发现容器里vi,vim等文本编辑器都没有。哎，当时想用sed命令来替换sources.list文件，弄了一夜没有成功。

# 没有vi,vim nano,gedit,emacs文本编辑器问题
第二天想起来可以利用容器的数据卷功能，把sources.list拷贝到宿主机上用phpstorm打开编辑然后再拷贝回容器去，这可以解决换源的问题


# 安装依赖问题
apt-get install -y libxxx的时候，竟然也出现了问题，apt-get虽然可以解决依赖问题，但是依赖的版本问题解决不了。哎，继续百度找到了aptitude install  命令
它可以帮你找到需要安装依赖的正确名称并解决依赖问题，尤其是各个依赖之间的版本要求问题，甚至有可能降低某个依赖的版本来迎合其他低版本的依赖。但是，**aptitude在php容器上也是默认没有的，需要用apt-get来安装**，而且应该发生在换国内源之前。因为好像换源之后再安装aptitude就不那么容易了。看来apt-get不行呀，yum会不会也有依赖之间要求版本不一致的问题呢？
```
aptitude install 
libjpeg62  libjpeg-dev
libfreetype6 libfreetype6-dev 
libpng-dev

#在用docker内置工具
docker-php-ext-install iconv mcrypt pdo_mysql
docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ 
docker-php-ext-install gd pdo_mysql
```
# 安装后修改php.ini问题
安装了gd，pdo_mysql后就会在
/usr/local/lib/php/extensions/no-debug-zts-20151012/下生成gd.so,pdo_mysql.so。接下来你可能以为该在php.ini的extension添加然后重启php了，**但是事实上你不必再操作php.ini了，**因为它已经为你在(usr/local/etc/php/conf.d/)扩展目录下生成了各个扩展的ini文件，比如docker-php-ext-gd.ini,docker-php-ext-pdo_mysql.ini等等。
而你只需重启php-fpm即可<BR>
感叹一下，这种配置机制非常好，在nginx,apache中是遇到过的。对于动态的增加扩展非常方便，把一整块的配置文件分成单独地一个个文件，交给主配置文件使用enclude包含进来。
# 安装后重启php-fpm
在容器内重启
```
kill -usr2 1
```
因为fpm容器里php-fpm进程号一般都是1。<BR>
如果是容器外重启，最好docker stop然后docker start就行。

# 安装扩展后的问题
安装扩展后为了以后可以重复使用，可以提交这次在容器里的更改为新的镜像，这样以后其他地方再使用时，直接pull下去run就行

来源：https://docker_practice.gitee.io/image/commit.html
```
docker commit 
--author "masterliu" 
--message "在官方php7.0.28基础上增加了gd，pdo_mysql扩展"
php70
php70:v2
```
这样以后就不要使用官方镜像了，也不必那么费劲地再次扩展编译换源问题了，一劳永逸!

# push到远程镜像库
push之前需要先tag一下，才能推送到dockerhub上（push到其他比如阿里云暂不知），否则会报错。
官方默认的前缀地址是docker.io
比如把php70:v2tag为masterliu/php7028:v2
```
docker tag php70:v2 masterliu/php7028:v2
#阿里云的镜像库也可以
docker tag php70:v2 registry.cn-hangzhou.aliyuncs.com/masterliu/php:fpm70v2
```
tag完后再次push就行了
```
docker push masterliu/php7028:v2
```
当然push到官网需要先登录才行