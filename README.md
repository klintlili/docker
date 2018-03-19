# docker
这里都是装的docker的一些东西，比如dockerfile啥的

dockerCentos里是基于Centos镜像下载安装了nginx。<BR>
# nginx镜像使用方法
进入dockerCentos目录查看Dockerfile可知,已经暴露了内部的80端口,下面执行如下命令构建镜像：
> 快慢看网速一般小5分钟<BR>
```
docker build -t nginx_01 . 注意后面的点
```
运行镜像为容器，这里~/www为宿主机里的php程序目录<BR>
```
	docker run --name nginx -v ~/www:/data/web -p 80:80 -d nginx_01
```
如果觉得配置满足不了你的需求，可以进入容器自己配置<BR>
```
	docker exec -it <容器ID or 容器名>  /bin/bash
```
修改配置后记得重启容器，或者在容器内部重启nginx也可，启动命令是<br>
```
docker start  <容器ID or 容器名>
```
# php镜像
php镜像试验与nginx类似，先构建镜像然后运行<BR>
**但是构建时间一般得半个多小时吧**，所以可以去官方镜像库搜索我曾经构建好的镜像pull下来就行<BR>
```
docker pull masterliu/php-fpm
```

启动php容器
```
docker run --name php71_13 -v ~/www:/data/web  -d php_01
```
php不必暴露9000端口

# php5.6.34镜像
这是基于centos6.9构建的，构建出来的镜像大小是1.53G。
不知道为啥这么大呢？
我是不是得换一种方式构建我的镜像库呢？

# 容器互联
docker服务器自己维护有一个网桥，且ip好像都是172.17.0.0/16网段的。<BR>
nginx和php两个容器的网络互联，在docker高版本时都是默认自动可以互联的，也可以自己配置。
nginx容器只需配置fastcgi_pass为php的ip地址:9000应该就行了。<BR>
比如php容器启动后是172.17.0.3，那么nginx容器里的配置就是<BR>
```
	fastcgi_pass 172.17.0.3:9000
```
还有，php容器里使用PDO连接mysql容器，记得要用mysql的容器ip，<br>因为它们在不同的容器就好比是不同的电脑一样，访问时都按照远程访问就是了。
# 还有一点要注意，非常重要
因为我们使用了nginx容器和php容器，这是两个独立的容器。那么对于程序目录，也就是php程序的目录确保在两个容器一样。如果不知道怎么搞的话，
那就每个容器中，一样的目录里都拷贝一份就行。本例中nginx容器和php容器的php程序目录都已经设置成/data/web了，记得两个容器的/data/web目录都有才行。<BR>
**还有一种办法**：那就是把宿主机的php程序目录挂载到两个容器相同的路径下，这也是开发时最好的方法
```
docker run --name php71_13 -v ~/www:/data/web  -d php_01
docker run --name nginx -v ~/www:/data/web -p 80:80 -d nginx_01
```
大家看到了吗，-v指定的参数都一样才行。

# 我的镜像仓库
我在dockerhub上的账号是masterliu
，上述镜像都已经构建好，如果懒得构建的话可以去下载直接用

# push我的本地镜像到hub上
```
docker tab 本地镜像  masterliu/php5634

docker login 
Username (masterliu):xxxxxx
Password:xxxxx
Login Succeeded

#push到库里即可
docker push masterliu/php5634
```