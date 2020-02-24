# Docker
	它是一种容器虚拟化技术
	主要解决的问题：程序的可移植性
	需要配置镜像加速器，我使用aliyun
## Docker三剑客
### 仓库
### 镜像
### 容器
## 一些指令
```
docker images
docker run -it --name
docker exec -t 容器id 指令
docker attach 容器id
docker run -d
...
```
## 镜像（images）
	UnionFS
	方便共享资源
	可以通过docker commit -a "" -m "" 创造一个自定义的镜像
## 容器数据卷
	第一，可以数据持久化；第二，可以容器间数据共享、
### 添加数据卷的方式
- 添加命令
```
docker run -it -v 宿主机内绝对路径:容器内绝对路径:[ro] image
带ro参数的话，只能宿主机进行写操作，容器内无法写只能读
```
- DockerFile添加
```
#volume test
FROM centos
VOLUME ["/dataVolumeContainer1","/dataVolumeContainter2"]
CMD echo "finish"
CMD /bin/bash
docker build -f xxx -t xxx/xxx .
docker run -it xxx/xxx
```
### 数据卷容器
	--volumes-from