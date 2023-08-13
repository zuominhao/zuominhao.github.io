# Docker

Docker是一个单机容器运行管理工具。用户将应用和应用需要的环境打包为一个镜像，用镜像生成容器来运行应用。每个容器都是一个包含虚拟环境的进程，不同应用运行在不同容器进程中，依赖着自己的运行环境，避免了运行环境冲突并提高了部署和运维效率。注意Docker需要root权限执行。

## 安装

### Ubuntu

方式一：快速安装
```sh
$ curl -fsSL https://get.docker.com -o get-docker.sh
$ sudo sh ./get-docker.sh
```

方式二：添加Docker源安装

```sh
$ sudo apt-get update
$ sudo apt-get install ca-certificates curl gnupg
$ sudo mkdir -m 0755 -p /etc/apt/keyrings
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
$ echo "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
$ sudo apt-get update
$ sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
> 详见[在线文档](https://docs.docker.com/engine/install/ubuntu/)。

### Windows

1. 准备WSL2
   Docker在Windows上是借助WSL2虚拟机来运行的，需要安装并升级到最新的WSL内核。参考WSL2相关文档进行安装。

2. 启用systemd
   添加下面的内容到 `/etc/wsl.conf` ，没有这个文件就创建一个。
    ```ini
    [boot]
    systemd=true
    ```
   `wsl --shutdown`重启虚拟机，使用`systemctl status`命令检查

3. 安装Docker
   在虚拟机内按照Linux下安装的方式安装Docker即可。

> 也可以用Docker官方推荐的Docker Desktop，启动停止都很慢，有时候还会占用100%。


### 执行权限
Docker一般需要root权限执行，可以将Docker加入用户组，并加上root权限。
``` sh
$ sudo groupadd docker
$ sudo usermod -aG docker $USER
```

## 镜像

镜像是一个运行环境的包，包括运行一个应用程序所需的一切。这个镜像告知容器应该如何实例化，决定哪些软件组件将运行以及如何运行。  

### 基本命令

登录：`docker login <网址> --username=<用户名>`     *从私有空间拉取推送镜像需登录*  
拉镜像： `docker pull docker.io/library/ubuntu`   
推送：`docker push <镜像名>`    *需要先到目标网站先建立仓库*  
列出镜像：`docker images`   
删除镜像：`docker rmi <镜像ID>`   
删除全部镜像：`docker rmi $(docker images -q)`   

### 构建镜像

构建镜像需要先编写Dockerfile文件对构建过程进行描述，然后再执行构建。

1. 编写Dockerfile（文本文件）：
    ```dockerfile
    FROM docker.io/dreamacro/clash    # 以一个镜像为基础构建
    COPY /clash /root/.config/clash/  # 构建过程中执行拷贝动作
    ADD packages/*.tar.gz /opt/packages/ # 构建过程中，将压缩包解压到指定路径
    ```
    
2. 执行构建：
    ```sh
    $ cd <Dockerfile路径>  # 移动到Dockerfile所在路径
    $ docker build -t <imageName> . # --force-rm强制覆盖已有的
    ```

> 可以去 https://hub.docker.com 搜寻需要的镜像。
> 镜像只能从描述文件构建，不能直接修改已有的镜像。
> 更换镜像源（已废弃），国内源当前已基本不可用。没有梯子可以从腾讯云上面拉镜像，但是不是最新的。

## 容器

容器是一个依助镜像创建的进程，进程里的程序运行在镜像提供的虚拟运行环境中。 

### 基本命令

启动容器：`docker run --name <容器名> -p <主机端口:容器端口> -v <主机路径:容器路径> -dt <仓库名/镜像ID>`    *-d 后台运行, -t 带tty,  -p -v 可以有多个*  
只创建不启动：`docker create`  
列出容器：`docker ps -a`  
进入容器：`docker exec -it <容器ID> /bin/bash`    *进入后用exit命令退出容器*  
删除容器：`docker rm <容器ID>`   
删除全部容器：`docker rm $(docker ps -aq)`  
关闭容器： `docker stop <容器ID>`   
关闭全部容器：`docker stop $(docker ps -aq)`  
跟踪容器日志：`docker logs -f <容器ID> `  

### 自启动

容器创建后默认自启动，电脑重启后docker会自动把创建过的容器拉起来。创建的时候可以指定自启动策略。

自启动不重启： `docker run -d --restart unless-stopped`   *手动停止后，docker重启不会跟着重启*  
自启动：`docker run -d --restart always`  *手动停止后，docker重启会跟着重启*  
更新为自启动：`docker update --restart always <容器名>`  

## Docker Compose

Docker Compose可以用写好的配置文件启动容器，可以替代命令行参数。

1. 创建`docker-compose.yml`文件
    ``` yaml
    version: '3.6'
    services:
      web:
        image: 'gitlab/gitlab-ce:latest'
        restart: unless-stopped
        hostname: '172.19.37.232'
        environment:
          GITLAB_OMNIBUS_CONFIG: |
            external_url 'http:// 172.19.37.232'
        ports:
          - '8080:80'
          - '8443:443'
          - '8022:22'
        volumes:
          - '/srv/gitlab/config:/etc/gitlab'
          - '/srv/gitlab/logs:/var/log/gitlab'
          - '/srv/gitlab/data:/var/opt/gitlab'
        shm_size: '256m'
    ```

2. 启动容器
    ```sh
    $ cd <docker-compose.yml所在文件夹>
    $ docker compose up -d  # -d在后台执行
    ```



# 一些示例

## ubuntu C++
