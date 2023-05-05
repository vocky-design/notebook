[Docker使用入门（一篇就够了） - 星光Starsray - 博客园 (cnblogs.com)](https://www.cnblogs.com/starsray/p/16067276.html)



/var/lib/docker目录存放着Docker镜像、容器以及容器的配置。

所有的容器都保存在/var/lib/docker/containers目录下。

###### 查看Docker运行状态

**<u>docker info</u>**

###### 镜像相关

- **<u>docker images</u>** 所有的镜像

- <u>**docker build -t [the-img-name] -f [dockerfile-name] .**</u>
  - -t 指定最终镜像名称
  - .  只是docker build命令在什么地方寻找Dockerfile



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

