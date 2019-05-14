#docker原生网络
docker原生网络通过`docker run`中`--network`字段配置，总共有三类，分别为none、host和bridge。

###none网络
容器内部只有lo网络，单机网络，没有内外网络环境，多用于安全要求高且不需要联网的应用。
###host网络
容器网络完全复制host网络的所有网卡。需要考虑端口冲突，通过损失灵活性，提升性能，在对网络传输要求高的时候应用。
同时可以让容器配置host网络，可以解决跨host网络方案、管理iptables等问题。
###bridge网络
容器默认的网络，在Docker安装时host会创建一个docker0的Linux bridge(可以通过brctl进行管理)。新创建的容器网卡被连接到host的虚拟网桥上，网关为host主机在虚拟网桥上的地址。容器bridge网络可以通过`docker network inspect bridge`指令查看容器bridge信息。


#docker自定义网络