# Linux

* * *

## 镜像源

[Linux换国内镜像源(Ubuntu、Debian等)_Raywit的博客-CSDN博客_linux换镜像源](https://blog.csdn.net/qq_40520596/article/details/110194439)

## 登录

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

## 二、保存和加载tar.gz

**保存镜像**

```
`docker save <myimage>:<tag> | gzip > <myimage>_<tag>.tar.gz`
```

**加载镜像**

```
`gunzip -c 文件名.tar.gz | docker load`
```
