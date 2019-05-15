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
docker用户自定义网络有三种驱动：bridge、overlay和macvlan。
bridge类似于原生bridge，overlay和macvlan用于跨host的主机网络。
###bridge
`docker network create --driver bridge --subnet 192.168.11.0/24 --gateway 192.168.11.1 my_net` 创建一个bridge驱动的网络my_net，子网地址是192.168.11.0，掩码24位，网关地址192.168.11.1
只有制定子网地址的docker网络才能配置静态IP发地址。`docker run -it --network my_net --ip 192.168.11.103 ubuntu`交互模式运行容器ubuntu，网络子网my_net，静态ip地址192.168.11.103

###overlay

###macvlan

#容器间通信
容器间通过IP、docker DNS server、joined三种通信方式。

###IP通信
两个容器都在同一个bridge上就可以直接通信。
`docker network connect my_net 容器ID2` 为ID为容器ID2的容器增加一块网卡连接到my_net子网。

###Docker DNS Server
该模式只能在自定义模式下使用，默认bridge不能使用。

	docker run -it --network=user_net --name=ubuntu1 ubuntu
	docker run -it --network=user_net --name=ubuntu2 ubuntu
交互方式创建连个容器，同时加入user_net，名称分别为ubuntu1和ubuntu2。在ubuntu1中ping ubuntu2是可以ping通的

###joined容器

