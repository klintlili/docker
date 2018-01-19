# docker
这里都是装的docker的一些东西，比如dockerfile啥的

dockerCentos下载下来,在dockerCentos目录下里执行：
	docker build -t nginx_01 .
已经暴露了内部的80端口
	docker run --rm -p 80:80 -d nginx_01


其他的类似
nginx和php两个容器的网络互联，在docker高版本时都是默认自动可以互联的，且ip好像都是172.17.0.0/16网段的
只需配置fastcgi_pass应该就行了。
还有注意，程序目录，也就是php程序的目录确保在两个容器一样。如果不知道怎么搞的话，
那就每个容器中，一样的目录里都拷贝一份就行。