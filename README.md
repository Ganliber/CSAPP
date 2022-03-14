# Abstract



### Docker

***

* `docker`和`virtualizan`的区别与联系：

  ![docker_and_virtualization](https://github.com/Ganliber/CSAPP/blob/main/images/docker_and_virtualization.png)

  * 虚拟化会使用虚拟机监控程序模拟硬件，从而使多个操作系统能够并行运行。但这不如容器轻便。事实上，在仅拥有容量有限的有限资源时，您需要能够进行密集部署的轻量级应用。Linux 容器在本机操作系统上运行，与所有容器共享该操作系统，因此应用和服务能够保持轻巧，并行化快速运行。

* `docker` 概念：

  * **镜像**（`Image`）
  * **容器**（`Container`）
  * **仓库**（`Repository`）



### Environment Configuration

***

* Docker 配置

  ![ini_env](https://github.com/Ganliber/CSAPP/blob/main/images/ini_env.png)

```
images : 镜像
containers/App : 容器
	1.Host Path : 本地文件夹
	2.Container Path : 本地文件夹挂载到容器中的文件夹
    3.Container Name : 容器名称
```

* 更新apt软件源

```
apt-get update
```

* 安装sudo

```
apt-get install sudo
```

* 安装c/c++编译环境

```
sudo apt-get install build-essential
```

* 补充gcc的完整环境

```
sudo apt-get install gcc-multilib
```

* 安装gdb

```
sudo apt-get install gdb
```













