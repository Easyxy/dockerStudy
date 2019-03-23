#Docker客户端:Client
docker指令：用于在Host上构建和运行容器。
RESTAPI与服务器通信
#Docker服务器:Docker daemon
Docker daemon是服务器端组件，以后台服务方式运行。
1.默认配置文件`/etc/systemd/system/multi-user.target.wants/docker.service`

    ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0
2.重启docker daemon

    systemctl daemon-reload
    systemctl restart docker.service

3.info子命令用于查看Docker服务器信息
docker info
#Docker镜像:Image
Docker镜像是只读模板，用于创建Docker容器
镜像生成方式：1.从无到有创建镜像。2.下载并使用别人创建好的镜像。3.在现有镜像基础上创建新的镜像。
镜像内容和创建步骤描述在文本文件中，被称为DockerFile。通过执行`docker build <docker-file>`构建Docker镜像
#Registry
存放Docker镜像的仓库，分为公有和私有。
DockerHub是默认的Registry，由Docker公司维护的公有仓库。
也可以自己搭建私有仓库。
通过`docker pull`命令从Registry下载镜像
`docker run`命令先下载镜像而后启动容器
#Docker容器:Container
Docker镜像的运行实例
通过CLI或者API启动、停止、移动、删除容器。
镜像是软件生命周期的构建和打包阶段；容器是启动运行阶段。
