

## 创建容器

```c
docker run -p 20000:22 -p 8000:8000 --name django -itd django_lesson:1.0
```

$django_lesson:1.0$是$django$镜像



## 修改镜像名称

```c++
docker + tag + 镜像ID + 要修改的名称
```





## 停止容器

```c
docker stop name
```

停止容器后一般需要再重启容器，才能恢复



## 重启容器

如果重启服务器，需要重启$docker$容器，命令：

```c
docker restart django
```



## 进入容器

```c
docker attach django
```



## 配置镜像

```c++
创建或修改 /etc/docker/daemon.json

{
    "registry-mirrors": [
        "http://hub-mirror.c.163.com",
        "https://docker.mirrors.ustc.edu.cn",
        "https://registry.docker-cn.com"
    ]
}
```





## 上传到$hub$

登陆：

```c
docker login
orrero
20020217.hzh
```

修改镜像名字

```c
docker tag xxx:latest orrero/new_name:new_tag
```

在$docker\ hub$上创建项目

上传

```c
docker push orrero/new_name:new_tag
```



## 配置$ssh$登陆

```shell
apt update # 更新
apt install vim openssh-server #安装ssh 服务
service ssh start # 运行ssh服务
service ssh status #查看ssh服务状态
```



## 修改时区

```shell
dpkg-reconfigure tzdata
```



# 指令

## 主机文件复制到容器中

```c++
sudo docker cp /home/acs/.vimrc   clash:/home/hzh/
```

## 容器文件复制到主机中

```c++
sudo docker cp vsftpd:/home/vsftpd/hzh/clash-linux-amd64-v1.7.1.gz   /home/acs/clash
```





## $Dockrfile$

## 指令集

```dockerfile
docker images -q #只列出镜像id

docker image rm $(docker images -q)#删除所有镜像
#参数
-p  主机端口:容器端口    #映射端口
-d    #容器后台运行
--name 容器名称  #容器起名

查看日志
docker logs 容器名
-t		加入时间戳
-f		跟随最新日志打印,不退出
--tail	 数字	显示最后的几行

进入容器
docker exec  -it  容器名  bash
退出容器
exit

#主机 与 容器 之间拷贝文件
#容器中文件 拷贝到 主机
docker cp  容器id:容器中路径   主机路径
#主机  拷贝到  容器中文件
docker cp  主机路径  容器id:容器中路径   

#查看容器内进程

docker top 容器名

容器详细信息
docker inspect 容器名


#绑定数据卷
使用绝对路径会在创建容器时 清空容器目录中的原本内容
-v  主机绝对路径 : 容器路径 : ro
ro 只能添加在容器路径之后,且添加之后容器内路径为 只读
使用   别名   默认在 /var/lib/docker/volums/ 下
别名不存在,自动创建    当别名存在,并且有内容的话,会直接替换容器目录中的原本内容
-v  aaa : 容器内路径 



#打包镜像
#将修改过的容器打包成镜像
docker  commit  -m "描述信息"  -a "作者"  容器id   镜像名:tag
#镜像 备份/恢复

#备份:
docker save   镜像名:tag   -o  名称.tar
恢复:
docker  load  -i  名称.tar



#网络
默认容器都连会连接到 docker网桥上
容器与容器直接可以使用ip进行访问
如果想使用 容器 来访问,需要用自定义网桥
查看网络
docker network ls
创建网桥
docker network create  网络名
查看网桥细节
docker network inspect 网络名
删除网桥
docker network rm 网络名
docker network prune   #删除所有没用到的网络
创建容器时指定网络
 --network 网络名 ....
已创建的容器加入到某个网络
docker network connect  网络名  容器id
```

​     











![img](https://i0.hdslb.com/bfs/note/83f718f5f7fec2111909da200fa1a4186fdfb5c1.png@690w_!web-note.webp)

**Dockerflie**

Dockerfile构建镜像

docker build  -t   镜像名:版本号   Dockerflie文件目录

![img](https://i0.hdslb.com/bfs/note/3aae51a58f44bd44890ba03960a621873e0d58f4.png@690w_!web-note.webp)



![img](https://i0.hdslb.com/bfs/note/1d623920600e3f402c2ffc2d93ebf362c1985485.png@690w_!web-note.webp)

RUN  命令

**语法一**

RUN yum install -y vim

**语法二**

RUN ["yum","install","-y","vim"]

EXPOASE 写出容器中使用的端口

VOLUME  写出容器中可以挂在的目录

WORKDIR  进入容器后的默认目录

ADD   将文件添加到镜像中(以Dockerflie文件为基础,来写文件的相对路径)

ADD aa.txt  /apps/data/

ADD 网址 保存路径  #直接从网页下载文件

ENV   key = value  

ENV   a=/apps/data

WORKDIR $a

CMD & ENTRYPOINT 启动时执行命令

CMD 只有最后一个生效

两种输入方式

1 直接跟命令

2 数组方式 [" "," "] (推荐)

![img](https://i0.hdslb.com/bfs/note/d534751cf8d93681a2b72a9caeb3d19cf8329fe1.png@690w_!web-note.webp)



![img](https://i0.hdslb.com/bfs/note/5c8b0a37415ab6fc4e038fcd3d96dccb6e16fc29.png@690w_!web-note.webp)







==================================

Docker Compos

:一次性部署多个可以是相关联的容器

安装

![img](https://i0.hdslb.com/bfs/note/1f5e3cee78bde5765f2713adbd48791e81cd518f.png@690w_!web-note.webp)

写法：

![img](https://i0.hdslb.com/bfs/note/c39a935d591acdd7ae07a90c8b09f321f1374eb4.png@690w_!web-note.webp)



启动项目（在写yml的路径下）

docker-compos up -d # -d 是后台运行

docker-compos dowm (关掉项目）





![img](https://i0.hdslb.com/bfs/note/0618f39403b1f2e11f7cc5f8195837b99b146b18.png@690w_!web-note.webp)
