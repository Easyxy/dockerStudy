#容器运行
###容器启动时指令
- CMD指令
- ENTRYPOINT指令
- docker run 指令 `docker run 镜像名 指令`

###容器持久运行
- docker run后台运行while指令 `docker run -d 镜像名 /bin/bash -c "while true;do sleep 1;done"`

###进入容器的方法

- docker attach ID attach到容器中端
attach直接进入容器启动命令的终端，不启动新进程，可用于查看启动命令的输出

- docker exec -it ID bash 交互模式运行bash(-it 交互模式)
在容器打开新的终端，可以启动新的进程，不能查看启动命令的输出。
`docker logs` 可以查看启动命令的输出，`docker logs -f` 类似tail -f

##最佳实践
- 服务类容器：以deamon运行对外提供服务，通过docker run -d以后台方式运行，通过docker exec -it 进入容器排查错误
- 工具类容器：一个临时的工作环境，通过docker run -it 运行容器后进入，exit退出终端，终止容器。

#容器生命周期管理
###stop

- docker stop 向容器进程发送SIGTERM信号
- docker kill 向容器进程发送SIGKILL信号

###start

- docker start 启动停止状态的容器，保留容器第一次启动时所有参数


###restart

- docker restart 重启容器，即依次进行docker stop和docker start
- docker run --restart 可以在容器退出时候自动重启。
	- --restart=always什么原因都立刻重启，
	- --restart=on-failure:3启动进程退出代码非0，重启容器，最多重启3次

###pause

- docker pause 暂停容器，暂停的容器不会占用CPU资源，多用于对容器文件系统快照或者host需要使用CPU资源的场景 

###unpause

- docker unpause 恢复暂停的容器

###rm

- docker rm 删除host上已经退出的容器
	- docker rm -v $(docker ps -aq -f startus=exited) 删除所有退出状态的容器