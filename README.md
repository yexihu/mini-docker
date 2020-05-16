
## 资料地址
- [掌握这些 Docker 原理知识，出去吹牛逼再也不担心了](https://juejin.im/post/5ea844225188256d657b3b27)
- [手把手教你写Docker](https://mp.weixin.qq.com/s?__biz=MzIxMjAzMDA1MQ==&mid=2648946038&idx=1&sn=a020e98c8dcc3ab69af02db3b5312ba4&chksm=8f5b527ab82cdb6c381276bafe504b56be4e91fb9aa9a0729c34fdbb79e12d4ce52ecf45ae7f)
- [手把手教你写Docker（下）](https://mp.weixin.qq.com/s?__biz=MzIxMjAzMDA1MQ==&mid=2648946055&idx=1&sn=e08c8b060693ed6ee04b4c979afde6d7&chksm=8f5b528bb82cdb9d5d923259e6db2d572061770000e64b7aa256f808c9a522ea9314413d85b1#rd)



## Docker 原理知识

![图](https://mmbiz.qpic.cn/mmbiz_png/nfxUjuI2HXj75nLVG358NN7GLic11vXvBVgBlSmXUDdnHYicBAjpeebSZxlqic8j9APIjNzSXJdLtTicic4GpzJF15g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

这张图中蕴含了一个经常被忽视的细节——容器是共享内核的，它们属于多个进程同时运行在一个内核上，只不过是利用namespace把它们隔离开，用cgroup限制可用资源。而虚拟机是共享“硬件”的，每个虚拟机都有自己独立的操作系统。所以，虚拟机是可引导的、绝对安全的隔离技术；而容器是非常脆弱的，不安全的隔离技术。

### Docker 资源隔离：Linux Namespace

Linux Namespace（Linux 命名空间）是 Linux 内核（Kernel）提供的功能，它可以隔离一系列的系统资源，如 PID（进程 ID，Process ID）、User ID、Network、文件系统等。

Linux 提供了 3 个系统 API 方便我们使用 Namespace：
- clone () 创建新进程，根据系统调用 flags 来决定哪种类型 Namespace 将会被创建，而该进程的子进程也会包含这些 Namespace。
- setns () 将进程加入到已存在的 Namespace 中。
- unshare () 将进程移出某个 Namespace。


分离文件系统

Docker提供的有Ubuntu、Centos的镜像，其实这些并不是严格意义上的镜像，它们准确的叫法应该是——根文件系统（root filesystem）。

容器是共享内核的，所以无论是Ubuntu、Centos它们里面都使用Host的内核，如果你在Docker中通过uname查看会发现无论什么镜像它们的内核版本都和Host一摸一样。所以，不同“操作系统”Docker镜像其实就是不同的根文件系统。

分离文件系统分为三个步骤，首先我们建立容器里面的/proc文件系统，很多Linux命令都是读取这个文件系统下的内容（比如top中显示的进程列表）；其次我们要把现在的用户和容器里面的用户做映射，否则会提示权限不足；最后我们要通过pivot_root 函数把“切换”根文件系统。


### Docker 资源限制：Linux Cgroups

### Docker 分层结构：Union File System

## A mini container 一个模拟Docker的container系统

![截图](./screenshot/v01.gif)

```mkdir /tmp/rootfs && tar -Jxf centos-7-docker.tar.xz -C /tmp/rootfs && git clone https://github.com/fireflyc/mini-docker.git
cd mini-docker/ && pipenv shell && python setup.py develop
```
