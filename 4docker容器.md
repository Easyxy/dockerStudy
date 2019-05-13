#容器运行
###容器启动时指令
- CMD指令
- ENTRYPOINT指令
- `docker run `指令 `docker run 镜像名 指令`

###容器持久运行
- `docker run`后台运行while指令 `docker run -d 镜像名 /bin/bash -c "while true;do sleep 1;done"`

###进入容器的方法

- `docker attach ID attach`到容器中端
attach直接进入容器启动命令的终端，不启动新进程，可用于查看启动命令的输出

- `docker exec -it ID bash` 交互模式运行bash(-it 交互模式)
在容器打开新的终端，可以启动新的进程，不能查看启动命令的输出。
`docker logs` 可以查看启动命令的输出，`docker logs -f` 类似tail -f

##最佳实践
- 服务类容器：以deamon运行对外提供服务，通过`docker run -d`以后台方式运行，通过`docker exec -it` 进入容器排查错误
- 工具类容器：一个临时的工作环境，通过`docker run -it` 运行容器后进入，exit退出终端，终止容器。

#容器生命周期管理
###stop

- `docker stop` 向容器进程发送SIGTERM信号
- `docker kill` 向容器进程发送SIGKILL信号

###start

- `docker start` 启动停止状态的容器，保留容器第一次启动时所有参数


###restart

- `docker restart` 重启容器，即依次进行`docker stop`和`docker start`
- `docker run --restart` 可以在容器退出时候自动重启。
	- `--restart=always`什么原因都立刻重启，
	- `--restart=on-failure:3`启动进程退出代码非0，重启容器，最多重启3次

###pause

- `docker pause` 暂停容器，暂停的容器不会占用CPU资源，多用于对容器文件系统快照或者host需要使用CPU资源的场景 

###unpause

- `docker unpause` 恢复暂停的容器

###rm

- `docker rm` 删除host上已经退出的容器
	- `docker rm -v $(docker ps -aq -f startus=exited)` 删除所有退出状态的容器

###create
- `docker create` 创建容器，创建后的容器为created状态

###start

- `docker start` 以后台方式启动容器

###run

- `docker run` 为`docker create`加上`docker start`

#容器资源限制

###内存
内存配额为固定值`-m`设定内存限额`--memory-swap`设定内存+swap的总限额
- `docker run -m 200M` 内存限额200M
- `docker run --memory-swap` 内存+swap总限额 参数-1时没有限制，参数0或者不配时候为默认，`--memory-swap`值为`-m`的两倍
	- `docker run -m 200M --memory-swap 300M ubuntu` 内存限额200Mswap限额100M运行ubuntu镜像
	- `docker run -m 100M --memory-swap -1 BBB` BBB容器内存限额100M，swap不限额。
	- `docker run -m 1G {--memory-swap 0} SSS` SSSr容器内存限额1G，swap限额1G

###CPU限额
CPU分配为权重值
- `docker run -c 512 A` A权重512 
`docker run -c 512 B` B权重1024，B占用CPU是A的两倍
- `docker run --cpu 1` 设置工作线程数量为1

###磁盘读写限额
磁盘读写限额可以通过`--blkio-weight`限制权重也可以通过`--device`限制读写的数据量和IO次数

- `--blkio-weight` 设定磁盘读写权重默认500 
`docker run -it --name AA --blkio-weight 600 ubuntu` 以交互模式运行名称为AA的ubuntu镜像，磁盘权重为600
`docker run -it --name BB --blkio-weight 300 ubuntu` 以交互模式运行名称为BB的ubuntu镜像，磁盘权重为300，为AA的一半
- bps配置每秒读写的数据量，iops配置每秒io次数。
`docker run -it --device-write-bps /dev/sda:30MB ubuntu` 以交互模式运行ubuntu镜像，容器对host上/dev/sda设备的写速率限制为30MB
	- `--device-read-bps` 限制读设备的bps
	- `--device-write-bps` 限制写设备的bps
	- `--device-read-iops` 限制读设备的bps
	- `--device-write-iops` 限制写设备的bps

- 