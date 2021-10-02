### 基本操作

#### 删除容器

docker rm 容器id 删除指定的容器，不能删除正在运行的容器，强删rm -f

docker rm -f $(docker ps -aq) 删除所有的容器

docker ps -a -q |xargs docker rm 删除所有容器

#### 启动和停止容器

docker start   容器id  启动容器

docker resart id    重启容器

docker stop id  停止

docker kill id 强制停止





#### 后台启动容器

```shell
docker run -d 镜像名

#问题
#docker ps 发现docker停止
#坑 docker 容器后台运行，就必须有一个前台进程，docker 发现没有应用，就会自动停止
#nginx 问题
```



#### 查看日志

```shell
docker logs -tf id
```



#### 查看容器中进程id

```shell
docker top id
```



#### 查看镜像元数据

```shell
docker inspect id
```



#### 进入当前正在运行的容器

```shel
方式1 进入容器后开启的=一个新的终端，可在里面操作
docker exec -it id bashshell

方式2 进入容器正在执行的终端，不会启动新的进程
docker attach id
```



#### 容器拷贝到主机上

```shell\
docker cp id:容器内路径 目的主机路径
```





#### 启动nginx

```shell
[root@hello ~]# docker run -d --name nginx01 -p 3344:80 nginx
-d 后端启动 命名nginx01  暴露端口3344 docker80 宿主机端口:容器内部端口  
 
 curl localhost:3344
 查看本机3344端口
```



#### 启动tomcat

```sh
docker run -it -rm tomcat
用完即删，一般测试用
```



#### 查看cpu状态

```shell
docker stats
```





#### 可视化

portainer





Rancher(CI/CD) 



### commit 镜像

```shell
docker commit 提交容器为一个新的版本

#命令与git相似
dockercommit -m="提交的信息描述" -a="作者" 目标镜像名：[TAG]

```

##### 实战

```shell
# 1 启动默认tomcat
# 2 发现这个tomcat是没有webapps应用，镜像的原因，官网下默认webapps下面是没有文件的
# 3 可自己拷贝文件进webapps
# 4 将我们的容器通过commit提交为一个新的镜像
　
```



#### 容器数据卷

##### docker理论回顾

将应用和环境打包成一个镜像

数据放入容器中，容器删除，数据丢失   ==数据持久化==

容器之间需要数据共享，docker数据同步到本地

目录挂载:将容器内目录，挂载到虚拟机上

总结：容器的持久化和同步操作，容器间数据共享



##### 使用数据卷

> 直接使用命令挂载 -v 

```shell
docker run -it -v  主机目录:容器目录   双向绑定
```

```shell
docker inspect id  查看容器信息
```



##### 实战 mysql

```shell
docker search mysql 
docker pull mysql
#启动mysql
docker run -d  -p 3310:3306 
-v /home/mysql/conf:/etc/mysql/conf.d 
-v /home/mysql/data:/var/lib/mysql 
-e MYSQL_ROOT_PASSWORD=?
--name mysql01  mysql
#后台运行 暴露端口3310 挂载  -e环境配置mysql的密码 命名为mysql01
```



##### 具名和匿名挂载

```shell
#匿名 -v 容器内路径     没有容器外路径
#具名 -v 卷名：容器内路径  
#指定路径挂载  -v 、宿主机路径：容器内路径
```



###### 拓展

```shell
# 通过 -v容器内路径：ro rw 改变读写权限
read only read write
ro说明这个路径只能通过宿主机来操作，容器内部无法操作
```



##### 初识Dockerfile

> Dockerfile 就是用来构建docker镜像的文件 命令脚本
>
> 通过脚本生成镜像，镜像是一层一层的，每个命令都是一层

```shell
FROM centos
#挂载 volume 每个命令都是镜像的一层
VOLUME {"volume01","volume02"}
CMD echo "----end----"
CMD　/bin/bash
```



```shell
# -t targetdocker build -f file -t /test/centos .
```



```shell
docker run -d -p 3310:3306 -e MYSQL_ROOT_PASSWORD=password--name mysql02--volumes-from mysql01 mysql#实现两个容器数据同步#mysql1数据被删mysql2仍然存在，相当于备份
```



#### 结论

容器之间配置信息的传递，数据容器卷的生命周期一直持续到没有容器使用为止，但一旦持久化到本地，本地数据不会被删除



#### Dockerfile

dockerfile 是用来构建docker镜像的文件，命令参数脚本

构建步骤：

1 编写一个dockerfile文件

2 docker build 构建成为一个镜像

3 docker run 运行镜像

4 docker push 发布镜像（DockerHub，阿里云镜像仓库）

##### 基础知识

1 每个保留关键字（指令）都必须是大写字母

2 执行指令都是从上向下构成的

3 #表示注释

4  每一个指令都会提交一个新的镜像，并提交

###### docker三部曲 开发，部署，运维	

DockerFile 构建文件，定义了一切的步骤，源代码

DockerImages 通过DockerFIle构建生成的镜像，最终发布和运行产品，原来是jar war 包

Docker容器 容器就是镜像运行起来提供服务器的

#### Dockerfile 

![dockerfile命令 的图像结果](https://tse4-mm.cn.bing.net/th/id/OIP-C.p3NmHHlewBvLwukFPGudFgHaFV?w=241&h=180&c=7&r=0&o=5&dpr=1.12&pid=1.7)

#### [Docker网络]([(72条消息) Docker学习（五）docker网络（Docker0、evth-pair 技术、--link、docker network）_Pluto.的博客-CSDN博客](https://blog.csdn.net/weixin_45677119/article/details/108187604?ops_request_misc=%7B%22request%5Fid%22%3A%22163309592216780366529199%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=163309592216780366529199&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-108187604.first_rank_v2_pc_rank_v29&utm_term=evth-pair&spm=1018.2226.3001.4187))

桥接

none

-link

veth-pair都是成对存在的



#### [docker搭建redis集群]([Docker搭建Redis Cluster集群 - 云+社区 - 腾讯云 (tencent.com)](https://cloud.tencent.com/developer/article/1838120))

#### 构建springboot项目



