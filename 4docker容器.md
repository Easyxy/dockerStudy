#容器运行
容器启动时指令
- CMD指令
- ENTRYPOINT指令
- docker run 指令 `docker run 镜像名 指令`

容器持久运行
- docker run后台运行while指令 `docker run -d 镜像名 /bin/bash -c "while true;do sleep 1;done"`
- dock