#docker镜像概述
docker镜像基于base镜像进行扩展构建，为分层结构。

docker镜像与正常操作系统差异：
- 正常操作系统启动过程优先加载bootfs，而后切根卸载bootfs，而后启动rootfs。docker直接通过Host启动rootfs。
- docker操作系统的kernel为Host的kernel。用户空间中的操作系统版本与发行版一致。

docker分层结构：
- docker所有镜像基于base镜像层制作。只有最上层“容器层”有读写权限，容器层之下的“镜像层”都是只读的。

docker文件————所有层统一成一个文件系统。容器层保存镜像变化，镜像可以被多个容器共享。
- 添加文件：文件被添加到容器层
- 读取文件：自上而下查找，找到后读入内存
- 修改文件：自上而下查找，找到后复制到容器层进行修改
- 删除文件：自上而下查找，找到后记录删除操作。

base镜像：不依赖其他镜像，从scratch构建，其他镜像以base镜像为基础进行扩展。通常为Linux发行版的Docker镜像，为最纯净版（甚至不包括ssh）。

#构建镜像
docker commit构建镜像
- 运行容器 `docker run -it containName #交互方式运行容器`
- 修改容器
- 保存容器为新镜像 `docker commit containName newImageName`

Dockerfile构建镜像

- 制作Dockerfile：制作文件Dockerfile通过各个指令
- docker build `docker builid -t imageName {-f Dockerfile |BuildcontextPath }`

#Docker镜像特性

docker镜像制作过程中运行过相同的指令会直接使用缓存的镜像。
docker分层结构查询`docker history imageName`

#Dockerfile指令
Dockerfile以#为注释符。常用有如下指令
FROM：指定base镜像
MAINTAINER：设置作者，任意字符串
COPY：将文件从build context复制到镜像 `COPY src dest` `COPY["src","dest"]`
ADD:将文件从build context复制到镜像，如果src是压缩文件会自动解压
ENV：设置环境变量
EXPOSE：制定容器进程监听某个端口
VOLUME：将文件或目录生命为volume
WORKDIR：设定RUN、CMD、ENTRYPOINT、ADD、COPY等指令的工作目录
RUN：在容器中运行指令
CMD：容器启动时运行指定指令，可以有多个，只有最后一个生效，可以被docker run之后的参数替换
ENTRYPOINT：容器启动时运行指令，只有最后一个生效。CMD和docker run之后的参数可以传递给ENTRYPOINT

范例：

	# my dockerfile
	FROM busybox
	MAINTAINER easy@home.com
	WORKDIR /test
	RUN touch tmpfile1
	COPY ["temfile2","."]
	ADD ["temfile3.tar.gz","."]
	ENV WELCOME "welcome"

#RUN、CMD、ENTRYPOINT差异
- RUN 执行命令并创建新的镜像层，常用于安装软件包
- CMD 设置容器启动后默认执行的命令和参数，能被docker run 命令行参数替换
- ENTRYPOINT 设置容器启动运行时的命令

###Shell和Exec格式
- Shell格式 `<instruction> <command>`


	RUN apt-get install python3
	CMD echo "Hello world"
	ENTRYPOINT echo "Hello world"

- Exce `<instruction> ["executable","para1","para2",..]`


	RUN ["apt-get","install","python3"]
	CMD ["/bin/echo","Hello,world"]
	ENTRYPOINT ["/bin/echo","Hello,world"]

CMD和ENTRYPOINT推荐使用Exce模式

###最佳实践
- 使用RUN指令安装应用和软件包，构建镜像
- 如果Docker镜像的用途是运行应用程序或服务，有限使用Exec格式的ENTRYPOINT指令，CMD为ENTRYPOINT提供额外的默认参数，docker run命令行替换默认参数
- 为容器设置默认的启动命令，使用CMD指令，在docker run 替换此默认指令

#镜像分发
镜像分发方式:
- 相同的Dockerfile创建镜像
- 镜像上传到公共Registry，Host直接下载使用
- 搭建私有Registry供本地Host

镜像命名
创建镜像 `docker build -t REPOSITORY`
tag：默认镜像名tag值为lastest，每个repository可以有多个tag，多用于标注版本等


	docker tag repo-1.9 repo:1.9
	docker tag repo-1.9 repo:1
	docker tag repo-1.9 repo:lastest
版本更新后
	
	docker tag repo-2 repo:2.0
	docker tag repo-2 repo:2
	docker tag repo-2 repo:lastest

###公共registry
1. DockerHub这册账号
2. `docker login -u 账号名`
3. 修改镜像的repository与dockerHub账号匹配 `docker tag 镜像名  账号名/新镜像名:tag`
4. 上传镜像 `docker push 镜像名  账号名/新镜像名:tag`

###本地registry

官方registry镜像
registry，5000端口

安装私有仓库

	docker run -d -p 5000:5000 -v /myregistry:/var/lib/registry:2

docker tag 打标签

	docker tag 用户名/镜像名:tag registry/用户名/镜像名:tag

上传镜像

	docker push registry/用户名/镜像名:tag

下载镜像

	docker pull registry/用户名/镜像名:tag