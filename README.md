# docker
这里都是装的docker的一些东西，比如dockerfile啥的

dockerCentos里是基于Centos镜像下载安装了nginx。<BR>
# nginx镜像使用方法
进入dockerCentos目录查看Dockerfile可知,已经暴露了内部的80端口,下里执行如下命令构建镜像：<BR>
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
php镜像试验与nginx类似，先构建镜像然后运行
# 容器互联
nginx和php两个容器的网络互联，在docker高版本时都是默认自动可以互联的，且ip好像都是172.17.0.0/16网段的，
nginx容器只需配置fastcgi_pass应该就行了。<BR>
**还有一点要注意**：程序目录，也就是php程序的目录确保在两个容器一样。如果不知道怎么搞的话，
那就每个容器中，一样的目录里都拷贝一份就行。本例中nginx容器和php容器的php程序目录都已经设置成/data/web了，所以如果修改容器的话，记得两个容器的/data/web目录都有才行
