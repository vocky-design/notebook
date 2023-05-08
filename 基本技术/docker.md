[Docker使用入门（一篇就够了） - 星光Starsray - 博客园 (cnblogs.com)](https://www.cnblogs.com/starsray/p/16067276.html)

##### 安装

如果已经安装过旧版本的Docker，需要先移除相关依赖

```shell
sudo apt-get remove docker docker-engine docker.io containerd runc
```

安装网络相关依赖包，用于通过HTTPS来获取仓库

```shell
sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
```

添加 Docker 的官方 GPG 密钥

```shell
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

添加仓库安装源

```shell
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```

更新软件包源并安装Docker

```shell
sudo apt update && sudo apt install -y docker-ce
```

/var/lib/docker目录存放着Docker镜像、容器以及容器的配置。

所有的容器都保存在/var/lib/docker/containers目录下。

##### 操作

###### 查看Docker运行状态

**<u>docker info</u>**

###### 镜像相关

- 创建镜像
  - 使用当前目录的Dockerfile创建镜像
    - <u>**docker build -t [the-img-name] -f [dockerfile-name] .**</u>
    - --tag, -t 镜像的名字或标签，通常name:tag或name格式；可以在一次构建中为一个镜像设置多个标签。
    - -f 指定要使用的Dockerfile名称。
    - .  指示docker build命令在什么地方寻找Dockerfile
  - 使用URL的Dockerfile创建镜像
    - `docker build github.com/creack/Dockerfile`

- **<u>docker images</u>** 所有的镜像



###### 容器相关

- **<u>docker ps</u>** 获取当前运行的容器列表
  - -a 包含停止的和运行的
  - -l 最后一次的
  - -n x x显示最后n个
- <u>**docker stop <the-container-id>**</u> 停止容器
- <u>**docker rm <the-container-id>**</u> 删除容器
  - 在命令中加入 -f 来一次性停止和删除容器 
  - <u>**docker rm -f <the-container-id>**</u>
- docker run 

