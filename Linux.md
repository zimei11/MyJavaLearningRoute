# Linux

## 升级

升级

```
sudo apt-get update
sudo apt-get upgrade -y
sudo apt-get dist-upgrade -y
```

## 宝塔

[宝塔面板下载，免费全能的服务器运维软件 (bt.cn)](https://www.bt.cn/new/download.html)

## 镜像源

### sudo apt-get

[ubuntu | 镜像站使用帮助 | 清华大学开源软件镜像站 | Tsinghua Open Source Mirror](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/)

[Linux换国内镜像源(Ubuntu、Debian等)_Raywit的博客-CSDN博客_linux换镜像源](https://blog.csdn.net/qq_40520596/article/details/110194439)

### pypi

设置国内镜像站

使用
```
sudo pip config set global.index-url  https://mirrors.bfsu.edu.cn/pypi/web/simple
```

或者清华

```
mkdir -p /root/.pip
cat > /root/.pip/pip.conf <<EOF
[global]
index-url=https://pypi.tuna.tsinghua.edu.cn/simple
trusted-host = pypi.tuna.tsinghua.edu.cn
EOF
```

或者豆瓣

```
[global]
index-url = http://pypi.douban.com/simple
[install]
trusted-host=pypi.douban.com
```

## ssh登录

### 基本用法

远程登录服务器：

```
ssh user@hostname 
```

*   `user`: 用户名
*   `hostname`: IP地址或域名

第一次登录时会提示：

```
The authenticity of host '123.57.47.211 (123.57.47.211)' can't be established.
ECDSA key fingerprint is SHA256:iy237yysfCe013/l+kpDGfEG9xxHxm0dnxnAbJTPpG8.
Are you sure you want to continue connecting (yes/no/[fingerprint])? 
```

输入`yes`，然后回车即可。  
这样会将该服务器的信息记录在`~/.ssh/known_hosts`文件中。

然后输入密码即可登录到远程服务器中。

* * *

默认登录端口号为22。如果想登录某一特定端口：

```
ssh user@hostname -p 22 
```

* * *

### 配置文件

创建文件 `~/.ssh/config`。

然后在文件中输入：

```
Host myserver1
    HostName IP地址或域名
    User 用户名

Host myserver2
    HostName IP地址或域名
    User 用户名 
```

之后再使用服务器时，可以直接使用别名`myserver1`、`myserver2`。

* * *

### 密钥登录

创建密钥：

```
ssh-keygen 
```

然后一直回车即可。

执行结束后，`~/.ssh/`目录下会多两个文件：

*   `id_rsa`：私钥
*   `id_rsa.pub`：公钥

* * *

之后想免密码登录哪个服务器，就将公钥传给哪个服务器即可。

例如，想免密登录`myserver`服务器。则将公钥中的内容，复制到`myserver`中的`~/.ssh/authorized_keys`文件里即可。

也可以使用如下命令一键添加公钥：

```
ssh-copy-id myserver 
```

* * *

### 执行命令

命令格式：

```
ssh user@hostname command 
```

例如：

```
ssh user@hostname ls -a 
```

或者

```
 ssh myserver 'for ((i = 0; i < 10; i ++ )) do echo $i; done' 
```

或者

```
 ssh myserver "for ((i = 0; i < 10; i ++ )) do echo $i; done" 
```

## 配置

使用scp配置其他服务器的vim和tmux

```
scp ~/.vimrc ~/.tmux.conf myserver:
```



docker教程
--------

### 部署

> 先配置好本机apt-get镜像源！！！，尤其是腾讯云的服务器，阿里云不用。

Ubuntu部署Docker

[Install Docker Engine on Ubuntu | Docker Documentation](https://docs.docker.com/engine/install/ubuntu/)

![image-20220816120520850](https://img.zimei.fun/202208161205944.png)复制粘贴操作到

。。。

![image-20220816120613526](https://img.zimei.fun/202208161206579.png)

输入下面代码，显示Docker版本即成功安装

```shell
docker --version
# or
docker -v
```

[黑马程序员Docker容器化技术，从零学会Docker教程_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1CJ411T7BK?spm_id_from=333.337.search-card.all.click&vd_source=ac53754f6533097757863a1d248f5406)

```
cat /etc/docker/daemon.json 

{
 "registry-mirrors": ["http://hub-mirror.c.163.com"],
 "insecure-registries":["120.48.20.150:5000"]
} 

```



### 命令

#### 将当前用户添加到`docker`用户组

为了避免每次使用`docker`命令都需要加上`sudo`权限，可以将当前用户加入安装中自动创建的`docker`用户组(可以参考[官方文档](https://docs.docker.com/engine/install/linux-postinstall/))：

```
sudo usermod -aG docker $USER 
```

执行完此操作后，需要退出服务器，再重新登录回来，才可以省去`sudo`权限。

* * *

#### 镜像（images）

1.  `docker pull ubuntu:20.04`：拉取一个镜像
2.  `docker images`：列出本地所有镜像
3.  `docker image rm ubuntu:20.04` 或 `docker rmi ubuntu:20.04`：删除镜像`ubuntu:20.04`
4.  `docker [container] commit CONTAINER IMAGE_NAME:TAG`：创建某个`container`的镜像
5.  `docker save -o ubuntu_20_04.tar ubuntu:20.04`：将镜像`ubuntu:20.04`导出到本地文件`ubuntu_20_04.tar`中
6.  `docker load -i ubuntu_20_04.tar`：将镜像`ubuntu:20.04`从本地文件`ubuntu_20_04.tar`中加载出来

* * *

#### 容器(container)

1.  `docker [container] create -it ubuntu:20.04`：利用镜像`ubuntu:20.04`创建一个容器。
2.  `docker ps -a`：查看本地的所有容器
3.  `docker [container] start CONTAINER`：启动容器
4.  `docker [container] stop CONTAINER`：停止容器
5.  `docker [container] restart CONTAINER`：重启容器
6.  `docker [contaienr] run -itd ubuntu:20.04`：创建并启动一个容器
7.  `docker [container] attach CONTAINER`：进入容器
    *   先按`Ctrl-p`，再按`Ctrl-q`可以挂起容器
8.  `docker [container] exec CONTAINER COMMAND`：在容器中执行命令
9.  `docker [container] rm CONTAINER`：删除容器
10.  `docker container prune`：删除所有已停止的容器
11.  `docker export -o xxx.tar CONTAINER`：将容器`CONTAINER`导出到本地文件`xxx.tar`中
12.  `docker import xxx.tar image_name:tag`：将本地文件`xxx.tar`导入成镜像，并将镜像命名为`image_name:tag`
13.  `docker export/import`与`docker save/load`的区别：
    *   `export/import`会丢弃历史记录和元数据信息，仅保存容器当时的快照状态
    *   `save/load`会保存完整记录，体积更大
14.  `docker top CONTAINER`：查看某个容器内的所有进程
15.  `docker stats`：查看所有容器的统计信息，包括CPU、内存、存储、网络等信息
16.  `docker cp xxx CONTAINER:xxx` 或 `docker cp CONTAINER:xxx xxx`：在本地和容器间复制文件
17.  `docker rename CONTAINER1 CONTAINER2`：重命名容器
18.  `docker update CONTAINER --memory 500MB`：修改容器限制

### 实战

进入AC Terminal，然后：

```
scp /var/lib/acwing/docker/images/docker_lesson_1_0.tar server_name:  # 将镜像上传到自己租的云端服务器
ssh server_name  # 登录自己的云端服务器

docker load -i docker_lesson_1_0.tar  # 将镜像加载到本地
docker run -p 20000:22 --name my_docker_server -itd docker_lesson:1.0  # 创建并运行docker_lesson:1.0镜像

docker attach my_docker_server  # 进入创建的docker容器
passwd  # 设置root密码 
```

去云平台控制台中修改安全组配置，放行端口`20000`。

返回AC Terminal，即可通过`ssh`登录自己的`docker`容器：

```
ssh root@xxx.xxx.xxx.xxx -p 20000  # 将xxx.xxx.xxx.xxx替换成自己租的服务器的IP地址 
```

然后，可以仿照上节课内容，创建工作账户`acs`。

最后，可以参考[4\. ssh——ssh登录](https://www.acwing.com/file_system/file/content/whole/index/content/2898263/)配置`docker`容器的别名和免密登录。

## 保存和加载tar.gz

**保存镜像**

```
`docker save <myimage>:<tag> | gzip > <myimage>_<tag>.tar.gz`
```

**加载镜像**

```
`gunzip -c 文件名.tar.gz | docker load`
```

## 问题集

### 图片文字中文乱码

操作环境Ubuntu22.04 LTS版本，go-cqhttp：v1.0.0-rc3，NoneBot2：2.0.0-beta.5，HarukaBot：v1.4.0

```
sudo apt install fonts-noto-cjk
```

### scripts非官方安装包

[WARNING: The scripts ... are installed in /home/.../.local/bin which is not on PATH. 警告之解决_香饽々的博客-CSDN博客](https://blog.csdn.net/iprobobo/article/details/123306771)

### NbBot重装脚手架

```text
pip install nb-cli
```

