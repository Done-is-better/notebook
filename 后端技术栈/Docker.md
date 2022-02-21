> 1. 用户使用Docker Client与 Docker Daemon 建立通信，斌发送请求给后者
> 2. Docker Daemon 作为Docker架构中的主体部分，首先提供Docker Server的功能使其可以接受Docker Client的请求
> 3. Docker Engine执行Docker 内部的一系列工作，每一项工作都是以一个Job的形式的存在
> 4. Job的运行过程中，当需要容器镜像时，就从Docker Registory中下载，并通过镜像管理驱动Graph driver将下载的镜像以Graph的形式存储。
> 5. 当需要为Docker 创建网络环境时，通过网络管理驱动Network driver创建并配置Docker容器网络环境
> 6. 当需要限制Docker 容器运行资源或执行用户指令等操作时，就通过Exec driver来完成
> 7. Libcontainer是一项独立的容器管理包，Network driver以及Exec driver都是通过Libcontainer来实现具体对容器进行的操作。

>  docker run -p 8080:8080 -t waylau/docker-spring-boot
>
> systemctl stop docker  停止服务
>
> systemctl start docker 启动
>
> systemctl restart docker
>
> systemctl status docker  查看状态
>
> systemctl enable docker 开机自启动
>
> docker info  查看信息
>
> yum remove docker-ce docker-ce-cli containerd.io
>
> rm -rf /var/lib/docker
>
> rm -rf /var/lib/containerd

#### 镜像命令

> **<a>docker images</a>** 列出本地主机上的镜像 -a 所有镜像，含历史映像层   -q  只显示镜像ID
>
> **<a>docker search 镜像名</a>**  从远程库搜索镜像  --limit 5 xx 限制
>
> <a>**docker pull 镜像名**</a>  拉取 docker pull xx:n.n.n 指定版本
>
> **<a>docker system df</a>**  查看镜像/容器/数据卷占用空间
>
> **<a>docker rmi 镜像ID/名</a>** 删除镜像 docker rmi -f xxx  强制删除   docker rmi -f 镜像名1: TAG 镜像名2:TAG   删除多个  <span style="color:red">docker rmi -f $(docker images -qa) 全部删除</span>

**df-h : 查看Linux空间占用**

虚悬镜像：仓库名和标签名都是\<none> 的镜像

#### 容器命令

> **<a>docker run [OPTION] IMAGE [COMMAND]\[ARG]</a>** 启动交互式容器
>
> ​		*OPTION：* --name="容器新名字"  为容器制定一个名称，不指定时系统随机生成一个NAME
>
> ​							-d:  后台运行容器并返回容器ID，即启动守护式容器
>
> ​							 -i:  以交互式运行容器，常配合-t 使用
>
> ​							 -t:  为容器重新分配一个伪输入终端，即启动交互式容器 (前后台有伪终端，等待交互)
>
> ​							 -P:  随机端口映射
>
> ​							 -p:  指定端口映射

``` shell
docker run -it ubuntu /bin/bash     #运行Ubuntu
docker run -d name ##启动守护式容器  容器运行的命令如果不是那些一直挂起的命令（如top,tail),会自动退出
```

> **<a>docker ps [OPTIONS]</a>**  列出正在运行的容器实例
>
> *OPTIONS:* 	-a:	列出当前所有正在运行的容器+历史上运行过的
>
> ​					  -l:	显示最近创建的容器
>
> ​					 -n:	显示最近n个容器
>
> ​					 -q:	静默模式
>
> 

```shell
exit   #退出容器，容器停止
ctrl+p+q  #退出容器，容器不停止
docker start 容器ID或容器名	#启动已停止的容器
docker restart 容器ID或容器名 #重启容器
docker stop id/name  #停止容器
docker kill id/name  #强制停止容器
docker rm id/name  #删除已停止的容器
docker rm -f $(docker ps -aq) #强制删除所有容器
docker ps -aq | xargs docker rm #强制删除所有容器
 
docker inspect name/id  #查看容器内部结构
```

> **进入正在运行的容器并以命令行交互**
>
> <a>docker exec -it id/name bash</a> 
>
> <a>docker attach id/name</a>  
>
> **比较** 	attach 直接进入容器启动命令终端，不会启动新的进程，用exit退出会导致容器的停止
>
> ​			 exec  是在容器中打开新的终端，并且可以启动新的进程，用exit退出不会导致容器的停止

> **拷贝容器内容到主机**
>
> docker cp id/name:容器内路径  目的主机路径

​    

> **导入和导出容器**
>
> docker export name/id > xx.tar
>
> cat 文件名.tar | docker import - 镜像用户/镜像名: 镜像版本号	导为镜像

> **commit** 
>
> <a>docker commit</a>  提交的容器副本成为一个新的镜像
>
> <a>docker commit -m="提交的描述信息" -a="作者" id/name 要创建的目标镜像名[:TAG]</a> 

#### 远程镜像库

## 1. 登录阿里云Docker Registy

```
$ docker login --username=借蛋生鸡 registry.cn-hangzhou.aliyuncs.com
```

用于登录的用户名为阿里云账号全名，密码为开通服务时设置的密码。

您可以在访问凭证页面修改凭证密码。

## 2. 从Registry中拉取镜像

```
$ docker pull registry.cn-hangzhou.aliyuncs.com/my2022-2-1/my-ubuntu:[镜像版本号]
```

## 3. 将镜像推送到Registry

```
$ docker login --username=借蛋生鸡 registry.cn-hangzhou.aliyuncs.com
$ docker tag [ImageId] registry.cn-hangzhou.aliyuncs.com/my2022-2-1/my-ubuntu:[镜像版本号]
$ docker push registry.cn-hangzhou.aliyuncs.com/my2022-2-1/my-ubuntu:[镜像版本号]
```

请根据实际镜像信息替换示例中的[ImageId]和[镜像版本号]参数。

## 4. 选择合适的镜像仓库地址

从ECS推送镜像时，可以选择使用镜像仓库内网地址。推送速度将得到提升并且将不会损耗您的公网流量。

如果您使用的机器位于VPC网络，请使用 registry-vpc.cn-hangzhou.aliyuncs.com 作为Registry的域名登录。

## 5. 示例

使用"docker tag"命令重命名镜像，并将它通过专有网络地址推送至Registry。

```
$ docker imagesREPOSITORY                                                         TAG                 IMAGE ID            CREATED             VIRTUAL SIZEregistry.aliyuncs.com/acs/agent                                    0.7-dfb6816         37bb9c63c8b2        7 days ago          37.89 MB$ docker tag 37bb9c63c8b2 registry-vpc.cn-hangzhou.aliyuncs.com/acs/agent:0.7-dfb6816
```

使用 "docker push" 命令将该镜像推送至远程。

```
$ docker push registry-vpc.cn-hangzhou.aliyuncs.com/acs/agent:0.7-dfb6816
```





#### 创建私有库

##### 拉取构建工具

> $ docker pull registry

##### 运行私有库

```
docker run -d -p 5000:5000 --privileged=true registry
```

##### 提交本地镜像

```
docker commit -m "msg" -a "author" id/name name:版本号
```

##### 复制镜像

```
 docker tag pubuntu:latest 192.168.198.139:5000/pubuntu:latest
```

##### 配置daemon.json 使支持http

```
"insecure-registries":["192.168.198.139:5000"] # docker默认不允许http方式发送镜像
```

##### 推送镜像到私服库

```
 docker push 192.168.198.139:5000/pubuntu:latest
```



### 容器数据卷

> Docker挂载主机目录访问如果出现cannot open directory: Permission denied
>
> 解决办法: 在挂载目录后加一个 <a>--privileged=true</a> 即可

##### 运行一个带容器卷存储功能的容器实例

> <a>docker run -it --privileged=true -v/宿主机绝对路径:/容器内目录  镜像名</a>
>
> 特点：
>
> 1. 数据卷可以在容器之间共享或重用数据
> 2. 卷中的更改可以直接实时生效
> 3. 数据卷中的更改不会包含在镜像的更新中
> 4. 数据卷的声明周期一致持续到没有容器使用它为止

##### 宿主机和容器之间映射添加容器卷

> <a>docker run -it --privileged=true -v/宿主机绝对路径目录:/容器内目录   镜像名</a> 
>
> 
