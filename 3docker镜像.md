#最小的镜像hello-world
    
	FROM scratch
	COPY hello /
	CMD ["/hello"]

#base镜像