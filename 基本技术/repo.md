```shell
repo init --repo-url ssh://git@www.rockchip.com.cn/repo/rk/tools/repo -u ssh://git@www.rockchip.com.cn/linux/rockchip/platform/manifests -b linux -m rk3588_linux_release.xml
```

[Repo工作原理和使用介绍_Android_脚本之家 (jb51.net)](https://www.jb51.net/article/232003.htm#:~:text=Repo工作原理和使用介绍 1 1. 概要 repo是Android为了方便管理多个git库而开发的Python脚本。 repo的出现，并非为了取代git，而是为了让Android开发者更为有效的利用git。 Android源码包含数百个git库，仅仅是下载这么多git库就是一项繁重的任务，所以在下载源码时，Android就引入了repo。 Android官方推荐下载repo的方法是通过Linux,通过集中维护所有git库的清单，repo可以方便的从清单中获取git库的信息。 ... 3 3. 使用介绍 repo命令的使用格式如下所示： 1 )

###### 创建目录和修改环境变量

```shell
mkdir ~/bin
PATH=~/bin:$PATH
```

###### 下载repo代码

```shell
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod a+x ~/bin/repo
```

###### 初始化

```shell
#repo init -u <URL> [<OPTIONS>]
repo init -u https://android.googlesource.com/platform/manifest -m 123.xml -b release-1.0 
```

repo init

- -u 指定mainfest仓库地址
- -m 选择仓库中某个mainfest文件，如果没有设置，就使用default.xml
- -b 指定一个分支或修正版本

repo init 生成一个.repo文件夹

| 文件或目录名         | 说明                           |
| -------------------- | ------------------------------ |
| manifests            |                                |
| manifests.git        |                                |
| manifest.xml         | 工作树的主配置文件，不要编辑它 |
| project.list         | 项目列表                       |
| projects/            | 每个git工程的.git目录          |
| repo/                | repo的python源码               |
| project-objects/     |                                |
| copy-link-files.json |                                |

###### 拉代码

```shell
#repo sync
#repo sync PROJECT0 PROJECT1 PROJECT2
```

repo sync

- -j <numbers> 多任务
- -c 只下载当前分支代码
- -d 让工程回退到manifest指定的版本
- -f 如果某个工程同步失败，继续同步

###### 切换分支

```shell
#切换分支
repo start BRANCH_NAME PROJECT_NAME
#查看分支
repo branches
#提交
repo upload
#安全删除不需要的分支
repo prune
```



###### 基本工作流程

1. repo start：Use to start a new topic branch.
2. git commit：Use git add to stage changes.
3. repo upload 上传修改