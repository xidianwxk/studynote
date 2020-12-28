## Part1:基础学习

### docker基础

#### 查看系统内核 

```
uname -r
[root@localhost /]# uname -r
3.10.0-1127.el7.x86_64
```

#### 系统版本

```
[root@localhost /]# cat /etc/os-release 
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"
```

#### 卸载docker

```shell
$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

#### 安装docker

参考网址：https://docs.docker.com/engine/install/centos/

+ 安装相关包

  ```
  yum install -y yum-utils
  ```

+ 设置镜像仓库

  ```shell
   sudo yum-config-manager \
      --add-repo \
      https://download.docker.com/linux/centos/docker-ce.repo  #默认国外
      
      yum-config-manager \
      --add-repo \
      http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
  ```

+ 安装docker

  - 更新yum  软件包索引

    ```
    yum makecache fast
    ```

  - 安装

    ```
    ce  社区
    $ sudo yum install docker-ce docker-ce-cli containerd.io
    
    指定版本
    $ sudo yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io
    ```

+ 启动docker

  ```
  $ sudo systemctl start docker
  docker version
  ```

  ```shell
   Client: Docker Engine - Community
   Version:           20.10.1
   API version:       1.41
   Go version:        go1.13.15
   Git commit:        831ebea
   Built:             Tue Dec 15 04:37:17 2020
   OS/Arch:           linux/amd64
   Context:           default
   Experimental:      true
  
  Server: Docker Engine - Community
   Engine:
    Version:          20.10.1
    API version:      1.41 (minimum version 1.12)
    Go version:       go1.13.15
    Git commit:       f001486
    Built:            Tue Dec 15 04:35:42 2020
    OS/Arch:          linux/amd64
    Experimental:     false
   containerd:
    Version:          1.4.3
    GitCommit:        269548fa27e0089a8b8278fc4fc781d7f65a939b
   runc:
    Version:          1.0.0-rc92
    GitCommit:        ff819c7e9184c13b7c2607fe6c30ae19403a7aff
   docker-init:
    Version:          0.19.0
    GitCommit:        de40ad0
  ```

  

+ 测试hello world

  ```
  $ sudo docker run hello-world
  ```

  ```shell
  Unable to find image 'hello-world:latest' locally   
  latest: Pulling from library/hello-world
  0e03bdcc26d7: Pull complete 
  Digest: sha256:1a523af650137b8accdaed439c17d684df61ee4d74feac151b5b337bd29e7eec
  Status: Downloaded newer image for hello-world:latest
  
  Hello from Docker!
  This message shows that your installation appears to be working correctly.
  
  To generate this message, Docker took the following steps:
   1. The Docker client contacted the Docker daemon.
   2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
      (amd64)
   3. The Docker daemon created a new container from that image which runs the
      executable that produces the output you are currently reading.
   4. The Docker daemon streamed that output to the Docker client, which sent it
      to your terminal.
  
  To try something more ambitious, you can run an Ubuntu container with:
   $ docker run -it ubuntu bash
  
  Share images, automate workflows, and more with a free Docker ID:
   https://hub.docker.com/
  
  For more examples and ideas, visit:
   https://docs.docker.com/get-started/
  ```

+ 查看下载的hello world镜像 

  ```shell
  [root@localhost /]# docker images
  REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
  hello-world   latest    bf756fb1ae65   11 months ago   13.3kB
  ```

#### 卸载docker

+  卸载依赖

  ```
  $ sudo yum remove docker-ce docker-ce-cli containerd.io
  ```

+ 删除资源路径

  ```
  + sudo rm -rf /var/lib/docker
  ```

#### 阿里云镜像加速

​		登录阿里云账号   ==>   容器服务   ==>    镜像加速器     ==>          配置使用

#### hello world 流程

<img src="docker.assets\image-20201216153957450.png" alt="image-20201216153957450" style="zoom: 67%;" />

#### docker工作原理

#####如何工作

+ Client-Server结构   
+ 守护进程运行在主机上，通过Socket在客户端访问
+ Docker_Server 接收到Docker-Client请求，执行这个命令

<img src="docker.assets\image-20201216154528032.png" alt="image-20201216154528032" style="zoom:67%;" />

##### 为啥比虚拟机快

+  Docker  具有比VM更少的抽象层（虚拟机《=》容器）

  <img src="docker.assets\image-20201216154739966.png" alt="image-20201216154739966" style="zoom:80%;" />

+ Docker（秒级别）利用宿主机的内核（操作系统）  ；  VM  （分钟级别）需要Guest OS需要重新加载一个操作系统内核

  <img src="docker.assets\image-20201216155056718.png" alt="image-20201216155056718" style="zoom:80%;" />

​     

####Docker常用命令

#####帮助命令

```shell
docker  version
docker info
docker ~   --help
```

##### 镜像命令

+ 查看所有本地的主机上的镜像docker images  

  ```shell
  [root@localhost /]# docker images 
  REPOSITORY (仓库源)   TAG       IMAGE ID       CREATED         SIZE
  hello-world   latest    bf756fb1ae65   11 months ago   13.3kB
  
  参数
  -a
  -q    只显示id        => -aq
  ```

+ 搜索镜像docker search

  ```shell
  [root@localhost /]# docker search mysql
  NAME                              DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
  mysql                             MySQL is a widely used, open-source relation鈥  10269     [OK]       
  mariadb                           MariaDB is a community-developed fork of MyS鈥  3794
  
  -f  过滤
  # docker search mysql --filter=STARS=3000
  ```

+ 下载镜像docker pull

  ```shell
  [root@localhost /]# docker pull mysql
  Using default tag: latest
  
  docker pull mysql +版本    默认最新
  ```

  分析（分层下载   联合文件系统）

  ```shell
   [root@localhost /]# docker pull mysql
  Using default tag: latest      #默认最新
  latest: Pulling from library/mysql
  6ec7b7d162b2: Pull complete      #分层下载  image 核心
  fedd960d3481: Pull complete 
  7ab947313861: Pull complete 
  64f92f19e638: Pull complete 
  3e80b17bff96: Pull complete 
  014e976799f9: Pull complete 
  59ae84fee1b3: Pull complete 
  ffe10de703ea: Pull complete 
  657af6d90c83: Pull complete 
  98bfb480322c: Pull complete 
  9f2c4202ac29: Pull complete 
  a369b92bfc99: Pull complete 
  Digest:  sha256:365e891b22abd3336d65baefc475b4a9a1e29a01a7b6b5be04367fcc9f373bb7  #签名
  Status: Downloaded newer image for mysql:latest  
  docker.io/library/mysql:latest    #真实地址 
  
  docker pull mysql
  docker pull docker.io/library/mysql:latest 
  ```

+ 指定版本下载

  ```shell
  #指定版本
  docker pull mysql:5.7
  
  [root@localhost /]# docker pull mysql:5.7
  5.7: Pulling from library/mysql
  6ec7b7d162b2: Already exists   #联合文件系统
  fedd960d3481: Already exists 
  7ab947313861: Already exists 
  64f92f19e638: Already exists 
  3e80b17bff96: Already exists 
  014e976799f9: Already exists 
  59ae84fee1b3: Already exists 
  7d1da2a18e2e: Pull complete 
  301a28b700b9: Pull complete 
  979b389fc71f: Pull complete 
  403f729b1bad: Pull complete 
  Digest: sha256:d4ca82cee68dce98aa72a1c48b5ef5ce9f1538265831132187871b78e768aed1
  Status: Downloaded newer image for mysql:5.7
  docker.io/library/mysql:5.7
  
  ```

+ 删除镜像

  ```shell
  [root@localhost /]# docker images
  REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
  mysql         5.7       697daaecf703   4 days ago      448MB
  mysql         latest    ab2f358b8612   4 days ago      545MB
  hello-world   latest    bf756fb1ae65   11 months ago   13.3kB
  
  [root@localhost /]# docker rmi -f 697daaecf703
  Untagged: mysql:5.7
  Untagged: mysql@sha256:d4ca82cee68dce98aa72a1c48b5ef5ce9f1538265831132187871b78e768aed1
  Deleted: sha256:697daaecf703e82e8755034e816282fc3e912151b7818c85af8647fdcdcee517
  Deleted: sha256:5214ea7c0bfb5429533d91b143604067a50042ae7b371dddb6ae53baadd3f7ef
  Deleted: sha256:e9082a53da66fba9c49f51580500919310a36722cc4d3eb0c78e7000ad058655
  Deleted: sha256:8615ae1ee613441540ee54a2c517eb0600a6c83667a79f7ca74acc9ffec4c9a4
  Deleted: sha256:252efab3ecb7891820c5a340645044850d6edc7815c6588450d74b0a743424f4
  
  [root@localhost /]# docker images
  REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
  mysql         latest    ab2f358b8612   4 days ago      545MB
  hello-world   latest    bf756fb1ae65   11 months ago   13.3kB
  
  #递归删除全部
  [root@localhost /]# docker rmi -f $(docker images -aq)
  
  ```

  ##### 容器命令

  **说明：有了镜像才可以创建容器，LINUX    这里下载一个centos镜像进行测试学习**

  + 新建容器并启动

  ```shell
  docker pull centos    #用docker跑一个centos系统
  
  [root@localhost /]# docker pull centos
  Using default tag: latest
  latest: Pulling from library/centos
  7a0437f04f83: Pull complete 
  Digest: sha256:5528e8b1b1719d34604c87e11dcd1c0a20bedf46e83b5632cdeac91b8c04efc1
  Status: Downloaded newer image for centos:latest
  docker.io/library/centos:latest
  ```

  ```shell
  docker run [参数] image  
  --name="Name"   容器名
  -d			后台运行
  -it			交互方式
  -p			指定端口         主机：容器/容器
  -P			随机指定端口
  
  
  #启动并进入容器
  [root@localhost /]# docker run -it centos /bin/bash
  [root@9c0fe1490178 /]# ls        
  bin  etc   lib    lost+found  mnt  proc  run   srv  tmp  var
  dev  home  lib64  media       opt  root  sbin  sys  usr    #容器内的centos  基础版本
  [root@9c0fe1490178 /]# exit
  exit
  [root@localhost /]# ls
  bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
  boot  etc  lib   media  opt  root  sbin  sys  usr
  
  [root@localhost /]# docker ps    列出正在运行的容器   -a  曾经运行的
  												-n=?  最近的几个
  												-q  只显示编号
  ```

+ 退出、启动、停止容器

  ```shell
  exit    停止退出
  Ctrl+P+Q   不停止退出
  
  docker start id
  docker restart id
  docker stop id
  docker kill id
  ```

+ 删除容器

  ```shell
  docker rm id    (不能删除正在运行的容器)
  docker rm -f $(docker ps -aq)     可以
  
  
  #管道方式
  docker ps -a -q | xargs docker rm
  ```

  ##### 常用的其他命令

  + 后台启动

    ```shell
    docker run -d centos    #问题   启动后停止了
    					  # 后台运行需要有一个前台进程
    ```

  + 查看日志  

    ```shell
    docker logs -f -t --tail  num +id
    
    #shell 脚本
    [root@localhost /]# docker run -d centos /bin/sh -c "while true;do echo wangxiangkun;sleep 1;done"765f9bd92aa0355f07fc7c81773bd148ad618659deee4cc7984c968b8882d5f4
    [root@localhost /]# docker ps
    CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS     NAMES
    765f9bd92aa0   centos    "/bin/sh -c 'while true   11 seconds ago   Up 10 seconds             charming_zhukovsky
    
    #显示日志
    [root@localhost /]# docker logs -tf --tail 10 765f9bd92aa0
    2020-12-16T16:55:06.519903986Z wangxiangkun
    2020-12-16T16:55:07.528031039Z wangxiangkun
    2020-12-16T16:55:08.531792495Z wangxiangkun
    2020-12-16T16:55:09.534305258Z wangxiangkun
    2020-12-16T16:55:10.539584380Z wangxiangkun
    2020-12-16T16:55:11.543631578Z wangxiangkun
    ...
    ```

  + 进入当前运行的容器

    ```shell
    1.docker exec -it  id bash shell
    
    [root@localhost /]# docker ps
    CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
    [root@localhost /]# docker run -d centos /bin/bash -c "while true;do echo wangxiangkun;sleep 1;done"
    6756a0c891d00dcecce70e52226825d2630e383b341cf74ef2b0b676057a0a2f
    [root@localhost /]# docker ps
    CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS     NAMES
    6756a0c891d0   centos    "/bin/sh -c 'while true   12 seconds ago   Up 11 seconds             vigilant_varahamihira
    [root@localhost /]# docker exec -it 6756a0c891d0 /bin/bash
    [root@6756a0c891d0 /]# ls
    bin  etc   lib    lost+found  mnt  proc  run   srv  tmp  var
    dev  home  lib64  media       opt  root  sbin  sys  usr
    ```

    ```shell
    docker attach id
    [root@6756a0c891d0 /]# exit
    exit
    [root@localhost /]# docker ps
    CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS              PORTS     NAMES
    6756a0c891d0   centos    "/bin/sh -c 'while t鈥   About a minute ago   Up About a minute             vigilant_varahamihira
    [root@localhost /]# docker attach  6756a0c891d0    
    wangxiangkun
    wangxiangkun
    wangxiangkun
    wangxiangkun
    wangxiangkun
    wangxiangkun
    wangxiangkun
    wangxiangkun
    ...   ##死循环   退出不了
    
    #重连
    [root@localhost ~]# docker rm -f $(docker ps -aq)
    6756a0c891d0
    765f9bd92aa0
    8c0db4832430
    96162c410e4c
    9c0fe1490178
    8a3252ce0ff9
    
    ```

    区别

    - docker exec   进入容器后开启新的终端
      - docker attach   进入容器正在执行的终端   不会启动新进程

  + 查看容器内部的进程信息

    ```
    docker top id
    ```

  + 查看镜像源数据

    ```shell
    docker inspect id
    
    [root@localhost /]# docker inspect 765f9bd92aa0
    [
        {
            "Id": "765f9bd92aa0355f07fc7c81773bd148ad618659deee4cc7984c968b8882d5f4",
            "Created": "2020-12-16T16:54:36.834074584Z",
            "Path": "/bin/sh",
            "Args": [
                "-c",
                "while true;do echo wangxiangkun;sleep 1;done"
            ],
            "State": {
                "Status": "exited",
                "Running": false,
                "Paused": false,
                "Restarting": false,
                "OOMKilled": false,
                "Dead": false,
                "Pid": 0,
                "ExitCode": 137,
                "Error": "",
                "StartedAt": "2020-12-16T16:54:37.228369809Z",
                "FinishedAt": "2020-12-16T16:56:54.81056293Z"
            },
            "Image": "sha256:300e315adb2f96afe5f0b2780b87f28ae95231fe3bdd1e16b9ba606307728f55",
            "ResolvConfPath": "/var/lib/docker/containers/765f9bd92aa0355f07fc7c81773bd148ad618659deee4cc7984c968b8882d5f4/resolv.conf",
            "HostnamePath": "/var/lib/docker/containers/765f9bd92aa0355f07fc7c81773bd148ad618659deee4cc7984c968b8882d5f4/hostname",
            "HostsPath": "/var/lib/docker/containers/765f9bd92aa0355f07fc7c81773bd148ad618659deee4cc7984c968b8882d5f4/hosts",
            "LogPath": "/var/lib/docker/containers/765f9bd92aa0355f07fc7c81773bd148ad618659deee4cc7984c968b8882d5f4/765f9bd92aa0355f07fc7c81773bd148ad618659deee4cc7984c968b8882d5f4-json.log",
            "Name": "/charming_zhukovsky",
            "RestartCount": 0,
            "Driver": "overlay2",
            "Platform": "linux",
            "MountLabel": "",
            "ProcessLabel": "",
            "AppArmorProfile": ""
    ```

  + 从容器内拷贝文件到主机

    <img src="docker.assets\image-20201216171831589.png" alt="image-20201216171831589" style="zoom:80%;" />

    ​	

    ```shell
    docker cp  id:路径   主机路径
    
    [root@localhost ~]# docker ps
    CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
    [root@localhost ~]# docker images
    REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
    mysql         latest    ab2f358b8612   5 days ago      545MB
    centos        latest    300e315adb2f   8 days ago      209MB
    hello-world   latest    bf756fb1ae65   11 months ago   13.3kB
    [root@localhost ~]# docker run -it centos /bin/bash
    [root@a1f7f74979cf /]# [root@localhost ~]# ps
    [root@localhost ~]# docker ps
    CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS          PORTS     NAMES
    a1f7f74979cf   centos    "/bin/bash"   24 seconds ago   Up 23 seconds             eloquent_golick
    [root@localhost ~]# cd /home
    [root@localhost home]# ls
    [root@localhost home]# touch wxk.java
    [root@localhost home]# ls
    wxk.java
    [root@localhost home]# docker ps
    CONTAINER ID   IMAGE     COMMAND       CREATED              STATUS              PORTS     NAMES
    a1f7f74979cf   centos    "/bin/bash"   About a minute ago   Up About a minute             eloquent_golick
    [root@localhost home]# docker attach a1f7f74979cf
    [root@a1f7f74979cf /]# cd /home
    [root@a1f7f74979cf home]# ls
    [root@a1f7f74979cf home]# touch test.java
    [root@a1f7f74979cf home]# ls
    test.java
    [root@a1f7f74979cf home]# exit
    exit
    [root@localhost home]# docker ps
    CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
    [root@localhost home]# docker ps -a
    CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS                      PORTS     NAMES
    a1f7f74979cf   centos    "/bin/bash"   2 minutes ago   Exited (0) 11 seconds ago             eloquent_golick
    [root@localhost home]# docker cp a1f7f74979cf:/home/test.java /home
    [root@localhost home]# ls
    test.java  wxk.java
    ```

    #### 小结

    <img src="docker.assets\image-20201216180340179.png" alt="image-20201216180340179" style="zoom:80%;" />

### docker入门

#### docker部署nginx

+ 下载运行

  ```shell
  docker search nginx    #docker hub 搜索指定版本
  docker pull nginx   #下载镜像
  docker images		#查看下载
  docker run -d(后台运行) --name nginx01（容器名） -p（暴露端口） 3344（宿主机）:80（容器内端口） nginx   #运行
  docker ps  	#查看运行的容器
  ```

+ 端口暴露

  <img src="docker.assets\image-20201217101458480.png" alt="image-20201217101458480" style="zoom: 67%;" />

+ 测试

  ```shell
  [root@localhost ~]# curl localhost:3344
  <!DOCTYPE html>
  <html>
  <head>
  <title>Welcome to nginx!</title>
  <style>
      body {
          width: 35em;
          margin: 0 auto;
          font-family: Tahoma, Verdana, Arial, sans-serif;
      }
  </style>
  </head>
  <body>
  <h1>Welcome to nginx!</h1>
  <p>If you see this page, the nginx web server is successfully installed and
  working. Further configuration is required.</p>
  
  <p>For online documentation and support please refer to
  <a href="http://nginx.org/">nginx.org</a>.<br/>
  Commercial support is available at
  <a href="http://nginx.com/">nginx.com</a>.</p>
  
  <p><em>Thank you for using nginx.</em></p>
  </body>
  </html>
  
  [root@localhost ~]# clear
  [root@localhost ~]# docker ps
  CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                  NAMES
  1e20392bb5c8   nginx     "/docker-entrypoint.鈥   7 minutes ago   Up 7 minutes   0.0.0.0:3344->80/tcp   nginx01
  [root@localhost ~]# docker exec -it nginx01 /bin/bash
  root@1e20392bb5c8:/# whereis nginx
  nginx: /usr/sbin/nginx /usr/lib/nginx /etc/nginx /usr/share/nginx
  root@1e20392bb5c8:/# cd /etc/nginx
  root@1e20392bb5c8:/etc/nginx# ls
  conf.d          koi-utf  mime.types  nginx.conf   uwsgi_params
  fastcgi_params  koi-win  modules     scgi_params  win-utf
  
  root@1e20392bb5c8:/etc/nginx# exit
  exit
  [root@localhost ~]# docker ps
  CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                  NAMES
  1e20392bb5c8   nginx     "/docker-entrypoint.鈥   9 minutes ago   Up 9 minutes   0.0.0.0:3344->80/tcp   nginx01
  [root@localhost ~]# docker stop 1e20392bb5c8
  1e20392bb5c8
  ```

+ 外网访问

  <img src="docker.assets\image-20201217101607689.png" alt="image-20201217101607689" style="zoom:67%;" />

+ 思考：每次修改nginx的配置文件   都要进入容器内部   => 如何在容器外部提供一个映射路径

#### docker部署tomcat

+ 下载运行	

  ```shell
  docker run -it --rm(用完就删除   docker images 还在) tomcat:9.0      ##网络问题
  docker pull tomcat:9.0    ##网络问题
  docker pull tomcat    ##阉割版 （最小镜像 保证最小可运行环境）  webapps空  															webapps.dist  
  #运行
  docker run -d -p 3355:8080 --name tomcat01 tomcat
  #进入容器
  docker exec -it tomcat01 /bin/bash
  
  #发现问题   http://192.168.254.133:3355/  无法访问
  root@78bc9dacae29:/usr/local/tomcat# cp -r webapps.dist/* webapps
  ```

  ![image-20201217103810526](docker.assets\image-20201217103810526.png)

+ 思考：每次部署项目都要进入容器   =>  是否可以在容器外部提供一个映射路径  webapps，在外部放置项目自动同步到内部

#### docker部署ES+kibana

+ 注意：es 暴露端口多；十分耗内存；es的数据一般需要放置到安全目录挂载

+ 下载ES运行

  ```shell
  docker run -d --name elasticsearch --net somenetwork -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node"（集群） elasticsearch:7.6.2
  
  #--net somenetwork   网络配置
  #下载+启动
  docker run -d --name elasticsearch01 -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.6.2
  
  #测试
  curl localhost:9200
  [root@localhost ~]# curl localhost:9200
  {
    "name" : "387604846b08",
    "cluster_name" : "docker-cluster",
    "cluster_uuid" : "zToi_TGlRZ6fmzr7cdcHeQ",
    "version" : {
      "number" : "7.6.2",
      "build_flavor" : "default",
      "build_type" : "docker",
      "build_hash" : "ef48eb35cf30adf4db14086e8aabd07ef6fb113f",
      "build_date" : "2020-03-26T06:34:37.794943Z",
      "build_snapshot" : false,
      "lucene_version" : "8.4.0",
      "minimum_wire_compatibility_version" : "6.8.0",
      "minimum_index_compatibility_version" : "6.0.0-beta1"
    },
    "tagline" : "You Know, for Search"
  }
  
  ##es 十分消耗内存  造成系统卡顿
  ```

+ 启动后发现Linux卡顿   =>如何解决

  ```shell
  #查看内存状态
  docker stats
  CONTAINER ID   NAME              CPU %     MEM USAGE / LIMIT     MEM %     NET I/O      BLOCK I/O        PIDS
  387604846b08   elasticsearch02   1.00%     416.9MiB / 972.3MiB   42.88%    772B / 96B   157MB / 1.01MB   43
  #增加内存限制  修改配置文件  -e  环境配置修改
  docker run -d --name elasticsearch02 -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xmx512m" elasticsearch:7.6.2
  
  #测试
  [root@localhost ~]# curl localhost:9200
  ```

+ Kibana      需要访问ES的地址    =>  通过Linux转发（网络基础）

  <img src="docker.assets\image-20201217111243451.png" alt="image-20201217111243451" style="zoom:67%;" />

####docker 可视化工具

##### portainer

+ 安装

  ```shell
  docker run -d -p 8088:9000 \--restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
  ```

+ 访问测试

  ```shell
  curl localhost:8088
  ```

  ![image-20201217112344329](docker.assets\image-20201217112344329.png)

  ![image-20201217112544421](docker.assets\image-20201217112544421.png)

#####Rancher

```shell

```



#### docker 镜像讲解

+ 镜像：独立的软件包    打包软件的运行环境和基于运行环境开发的软件。所有的应用，直接打包docker镜像就可以直接跑起来
+ 如何获取
  + 远程仓库下载
  + 他人拷贝
  + 自己制作镜像DockerFile

##### 联合文件系统UnionFS

<img src="docker.assets\image-20201217113056043.png" alt="image-20201217113056043" style="zoom: 80%;" />

+ bootfs（开机启动  用完卸载）
+ rootfs(就是一个具体的操作系统)

<img src="docker.assets\image-20201217113202240.png" alt="image-20201217113202240" style="zoom: 80%;" />

##### 分层理解

![image-20201217113645178](docker.assets\image-20201217113645178.png)

```shell
[root@localhost ~]# docker image inspect redis:latest
[
    {
        "Id": "sha256:ef47f3b6dc11e8f17fb39a6e46ecaf4efd47b3d374e92aeb9f2606896b751251",
        "RepoTags": [
            "redis:latest"
        ],
        "RepoDigests": [
            "redis@sha256:0f724af268d0d3f5fb1d6b33fc22127ba5cbca2d58523b286ed3122db0dc5381"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2020-12-11T16:25:00.990749243Z",
        "Container": "6da61e8ec8c774f324d488c6047bd913d6f379855b7abcb08be9f41acee6e3ab",
        "ContainerConfig": {
            "Hostname": "6da61e8ec8c7",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "6379/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOSU_VERSION=1.12",
                "REDIS_VERSION=6.0.9",
                "REDIS_DOWNLOAD_URL=http://download.redis.io/releases/redis-6.0.9.tar.gz",
                "REDIS_DOWNLOAD_SHA=dc2bdcf81c620e9f09cfd12e85d3bc631c897b2db7a55218fd8a65eaa37f86dd"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"redis-server\"]"
            ],
            "Image": "sha256:d9311f71736ce563d01280a6cc23530a3f838f6f6c4c245b2b99919594b67b7b",
            "Volumes": {
                "/data": {}
            },
            "WorkingDir": "/data",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": {}
        },
        "DockerVersion": "19.03.12",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "6379/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "GOSU_VERSION=1.12",
                "REDIS_VERSION=6.0.9",
                "REDIS_DOWNLOAD_URL=http://download.redis.io/releases/redis-6.0.9.tar.gz",
                "REDIS_DOWNLOAD_SHA=dc2bdcf81c620e9f09cfd12e85d3bc631c897b2db7a55218fd8a65eaa37f86dd"
            ],
            "Cmd": [
                "redis-server"
            ],
            "Image": "sha256:d9311f71736ce563d01280a6cc23530a3f838f6f6c4c245b2b99919594b67b7b",
            "Volumes": {
                "/data": {}
            },
            "WorkingDir": "/data",
            "Entrypoint": [
                "docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": null
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 104252176,
        "VirtualSize": 104252176,
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/a72a2d761583ad3a768998c56fad353877beb2399196badfc09251d8ddcb3cb7/diff:/var/lib/docker/overlay2/de17a69727c128c52aa0a764afc713806b5ae9f70d8e74b296b7e1d2d096d6d8/diff:/var/lib/docker/overlay2/cbcbe7e971ebb2c4e28fe2cc4f01d2bba4de159e66b366c51d8fddd321c1bdb5/diff:/var/lib/docker/overlay2/bc0a77ddfbcfb8ca032c64dca91185a9026ad6dc95a0068f7dad39c52ef25dba/diff:/var/lib/docker/overlay2/1d33bf18bdc111b206fffba6e77a3a2525518a86d83d274056de064d9528bbe9/diff",
                "MergedDir": "/var/lib/docker/overlay2/12e186aae82e72b0b5ed9a859896960e8b8b28768e70cab5c09b0e9092eec784/merged",
                "UpperDir": "/var/lib/docker/overlay2/12e186aae82e72b0b5ed9a859896960e8b8b28768e70cab5c09b0e9092eec784/diff",
                "WorkDir": "/var/lib/docker/overlay2/12e186aae82e72b0b5ed9a859896960e8b8b28768e70cab5c09b0e9092eec784/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [    ##分层    文件级的记录
            
            			###  环境复用
                "sha256:87c8a1d8f54f3aa4e05569e8919397b65056aa71cdf48b7f061432c98475eee9",
                "sha256:25075874ce886bd3adb3b75298622e6297c3f893e169f18703019e4abc8f13f0",
                "sha256:caafc8119413c94f1e4b888128e2f337505fb57e217931a9b3a2cd7968340a9e",
                "sha256:e5d940a579ec4a80b6ec8571cb0fecf640dba14ccfd6de352977fd379a254053",
                "sha256:2a1c28c532d20c3b8af8634d72a4d276a67ce5acb6d186ac937c13bd6493c972",
                "sha256:1540b8226044ed5ce19cc0fec7fbfb36a00bb15f4e882d6affbd147a48249574"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]
```

+ 理解

  ![image-20201217113905568](docker.assets\image-20201217113905568.png)

  

  ![image-20201217113936199](docker.assets\image-20201217113936199.png)

  

  ![image-20201217114041632](docker.assets\image-20201217114041632.png)

  ​		![image-20201217114137429](docker.assets\image-20201217114137429.png)

+ 特点

  + 环境复用

  + Docker镜像  都是只读的(远程pull的镜像无法改变)     当容器启动时候（在容器层操作），一个新的可写层被加载到镜像的顶部   =>   提交（commit）自己镜像（后续）

    ![image-20201217114823499](docker.assets\image-20201217114823499.png)

  + 此时这一层  通常说是容器层     容器之下都叫镜像层

    ![image-20201217114609539](docker.assets\image-20201217114609539.png)

#####commit提交镜像

```shell
docker commit
#与Git原理类似
docker commit -m="描述信息" -a="作者" id 目标镜像名：tag
```

+ 测试

  ```sh
  #启动默认的tomcat   webapps目录为空   从webapps.dist拷贝  
  
  root@d623e5c9e754:/usr/local/tomcat# cp -r webapps.dist/* webapps 
  
  [root@localhost ~]# docker ps
  CONTAINER ID   IMAGE     COMMAND             CREATED         STATUS         PORTS                    NAMES
  d623e5c9e754   tomcat    "catalina.sh run"   8 minutes ago   Up 7 minutes   0.0.0.0:8080->8080/tcp   elegant_kirch
  [root@localhost ~]# docker commit -a="wangxiangkun" -m="add webapps applications" d623e5c9e754 mytomcat:1.0
  sha256:22ae1e7bcd43bf50d206f3a32ddc4989997891866838292c4b25d975191a278f
  [root@localhost ~]# 
  
  [root@localhost ~]# docker images
  REPOSITORY            TAG       IMAGE ID       CREATED          SIZE
  mytomcat              1.0       22ae1e7bcd43   18 seconds ago   654MB
  nginx                 latest    ae2feff98a0c   33 hours ago     133MB
  tomcat                latest    6d15a1d68603   4 days ago       649MB
  mysql                 latest    ab2f358b8612   5 days ago       545MB
  redis                 latest    ef47f3b6dc11   5 days ago       104MB
  centos                latest    300e315adb2f   9 days ago       209MB
  portainer/portainer   latest    62771b0b9b09   4 months ago     79.1MB
  elasticsearch         7.6.2     f29a1ee41030   8 months ago     791MB
  hello-world           latest    bf756fb1ae65   11 months ago    13.3
  [root@localhost ~]# 
  ```
  

### docker提升

#### 容器数据卷

#####什么是容器数据卷

+ docker 理念   应用+环境打包成镜像      删除容器 => 数据丢失？？     

+ 需求：数据可以持久化    （比如mysql 数据）   => 容器之间实现数据共享

+ Docker 容器中产生的数据，同步到本地

+ 卷技术：目录的挂载  将容器内的目录，挂载到Linux上

  <img src="docker.assets\image-20201217133407155.png" alt="image-20201217133407155" style="zoom:50%;" />

  容器的持久化和同步操作以及容器间的数据共享

##### 使用

+ 命令挂载  -v

  ```shell
  docker run -it -v 主机目录：容器内目录 
  [root@localhost ~]# docker run -it -v /home/test:/home centos /bin/bash
  
  
  #查看详情
  [root@localhost home]# docker ps
  CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS         PORTS     NAMES
  7cf9b7cd012d   centos    "/bin/bash"   2 minutes ago   Up 2 minutes             sweet_rhodes
  [root@localhost home]# docker inspect 7cf9b7cd012d
  [
  	###双向绑定  
      ###容器关闭后    宿主机修改文件   开启容器   数据仍然同步
      ###本地修改数据即可！！
      {
          "Id": "7cf9b7cd012dd2f875e8833ea4976f4a5910ddf2631286351bda63a304f46203",
          "Created": "2020-12-17T05:38:17.866554099Z",
          "Path": "/bin/bash",
         。。。。。。。。
          "Mounts": [
              {    ###挂载
                  "Type": "bind",
                  "Source": "/home/test",         #主机地址
                  "Destination": "/home",		    #容器地址
                  "Mode": "",
                  "RW": true,
                  "Propagation": "rprivate"
              }
          ]
         。。。。。。。。。
  ]
  ```

##### 实战：mysql数据持久化

+ 下载启动(数据挂载)

  <u>指定路径挂载</u>

  ```shell
  docker run -d(后台) -p（端口） 3310:3306 -v（数据挂载） /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql  --name mysql01 mysql:5.7
  ```

+ 配置密码

  ```shell
  docker run -d -p 3310:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql:latest
  ```

+ 链接测试（本地创建数据库   Linux文件添加对应文件）

  <img src="docker.assets\image-20201217135721207.png" alt="image-20201217135721207" style="zoom:60%;" />

  ```shell
  [root@localhost home]# docker rm -f mysql01
  ##删除容器后    、本地/home/mysql数据仍然在
  ```

##### 具名挂载  匿名挂载

+ 匿名挂载

  ```shell
  docker run -d -P --name nginx02 -v /etc/nginx nginx
  
  #查看所有卷
  docker volume ls
  
  [root@localhost docker-test-volume]# docker volume ls
  DRIVER    VOLUME NAME
  local     9879bae518017fd75834eee6bf022f57a3a559080a29e038ca1a515c5f9564af   ##匿名
  local     e9f0eccf74e7196efa4d60c0de2b2d7e7785306808b8fd2805a046a94d38faa5
  
  
  ```

  

+ 具名挂载

  ```shell
  docker run -d -P --name nginx03 -v jumign-nginx:/etc/nginx nginx
  
  #-v  卷名：容器内路径
  [root@localhost docker-test-volume]# docker volume ls
  DRIVER    VOLUME NAME
  local     9879bae518017fd75834eee6bf022f57a3a559080a29e038ca1a515c5f9564af
  local     e9f0eccf74e7196efa4d60c0de2b2d7e7785306808b8fd2805a046a94d38faa5
  local     jumign-nginx                    ##具名挂载
  
  
  ##所有卷  未指定目录  默认在  /var/lib/docker/volumes
  [root@localhost docker-test-volume]# docker volume inspect jumign-nginx
  [
      {
          "CreatedAt": "2020-12-17T14:20:48+08:00",
          "Driver": "local",
          "Labels": null,
          "Mountpoint": "/var/lib/docker/volumes/jumign-nginx/_data",
          "Name": "jumign-nginx",
          "Options": null,
          "Scope": "local"
      }
  ]
  
  
  [root@localhost docker-test-volume]# cd /var/lib/docker
  [root@localhost docker]# ls
  buildkit    image    overlay2  runtimes  tmp    volumes
  containers  network  plugins   swarm     trust
  [root@localhost docker]# cd volumes/
  [root@localhost volumes]# ls
  9879bae518017fd75834eee6bf022f57a3a559080a29e038ca1a515c5f9564af
  backingFsBlockDev
  e9f0eccf74e7196efa4d60c0de2b2d7e7785306808b8fd2805a046a94d38faa5
  jumign-nginx
  metadata.db
  [root@localhost volumes]# 
  
  
  [root@localhost volumes]# cd jumign-nginx/
  [root@localhost jumign-nginx]# ls
  _data
  [root@localhost jumign-nginx]# cd _data/
  [root@localhost _data]# ls         ###nginx 配置文件
  conf.d          koi-utf  mime.types  nginx.conf   uwsgi_params
  fastcgi_params  koi-win  modules     scgi_params  win-utf
  
  ```

##### 扩展

```shell
docker run -d -P --name nginx03 -v jumign-nginx:/etc/nginx:ro nginx    ##只读
docker run -d -P --name nginx03 -v jumign-nginx:/etc/nginx:rw nginx	   ##可读可写
```
##### 初识dockerfile

​	用来构建docker镜像的构建文件       命令脚本

+ 测试

  ```shell
  [root@localhost home]# mkdir docker-test-volume
  [root@localhost home]# ls
  docker-test-volume  mysql  test  test.java  wxk.java
  
  [root@localhost home]# cd docker-test-volume/
  [root@localhost docker-test-volume]# pwd
  /home/docker-test-volume
  ##创建dockerfile文件
  [root@localhost docker-test-volume]# vi dockerfile1
  。。。写入内容
  [root@localhost docker-test-volume]# cat dockerfile1 
  FROM centos     ##一步 一层
  VOLUME ["volum01" ,"volum02"]
  
  CMD echo "-------end-------"
  CMD /bin/bash
  
  [root@localhost docker-test-volume]# docker build -f dockerfile1 -t wxk/centos .
  Sending build context to Docker daemon  2.048kB
  Step 1/4 : FROM centos
   ---> 300e315adb2f
  Step 2/4 : VOLUME ["volum01" ,"volum02"]
   ---> Running in 97708cf76efc
  Removing intermediate container 97708cf76efc
   ---> d9157a8db2d5
  Step 3/4 : CMD echo "-------end-------"
   ---> Running in 2e02ebb28cc9
  Removing intermediate container 2e02ebb28cc9
   ---> a7e756cb4cd2
  Step 4/4 : CMD /bin/bash
   ---> Running in fab4dfc97071
  Removing intermediate container fab4dfc97071
   ---> 949e9de857fd
  Successfully built 949e9de857fd
  Successfully tagged wxk/centos:latest
  
  
  [root@localhost docker-test-volume]# docker images
  REPOSITORY            TAG       IMAGE ID       CREATED          SIZE
  wxk/centos            latest    949e9de857fd   44 seconds ago   209MB
  mytomcat              1.0       22ae1e7bcd43   56 minutes ago   654MB
  。。。
  hello-world           latest    bf756fb1ae65   11 months ago    13.3kB
  [root@localhost _data]# docker run -it 949e9de857fd
  [root@b7e42f50d792 /]# ls -l
  total 0
  lrwxrwxrwx   1 root root   7 Nov  3 15:22 bin -> usr/bin
  drwxr-xr-x   5 root root 360 Dec 17 06:31 dev
  。。。
  drwxr-xr-x   2 root root   6 Dec 17 06:31 volum01		#生成镜像的时候 自己挂载的数据卷目录
  drwxr-xr-x   2 root root   6 Dec 17 06:31 volum02		#与外部同步
  
  ##容器内创建文件
  [root@b7e42f50d792 /]# cd volum01
  [root@b7e42f50d792 volum01]# touch hello.txt
  
  ##宿主机查看
  [root@localhost data]# docker ps
  CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS     NAMES
  b7e42f50d792   949e9de857fd   "/bin/sh -c /bin/bash"   2 minutes ago   Up 2 minutes             hungry_poincare
  [root@localhost data]# docker inspect b7e42f50d792
          "Mounts": [
              {
                  "Type": "volume",      
                  "Name": "62a5184a367cf85fc7910d19febd29a1f643e167748153322d75cda569798d20",
                  "Source": "/var/lib/docker/volumes/62a5184a367cf85fc7910d19febd29a1f643e167748153322d75cda569798d20/_data",
                  "Destination": "volum02",
                  "Driver": "local",
        
              },
              {
                  "Type": "volume",
                  "Name": "d5c8b4c0e19a85b27ae1813d310ec92d22e63838fbdbba32a80bf9e12553bfe1",
                  "Source": "/var/lib/docker/volumes/d5c8b4c0e19a85b27ae1813d310ec92d22e63838fbdbba32a80bf9e12553bfe1/_data",
                  "Destination": "volum01",
            
              }
          ],
  
  
  [root@localhost data]# cd /var/lib/docker/volumes/d5c8b4c0e19a85b27ae1813d310ec92d22e63838fbdbba32a80bf9e12553bfe1/_data
  [root@localhost _data]# ls
  hello.txt          ##同步成功！!
  [root@localhost _data]# 
  ```

##### 数据卷容器

实现容器之间的数据持久化：  多个mysql同步数据    --volumes-from

![image-20201217144016310](docker.assets\image-20201217144016310.png)

```shell
[root@localhost _data]# docker run -it --name docker01 wxk/centos
[root@290492261f70 /]# ls
bin  home   lost+found  opt   run   sys  var
dev  lib    media       proc  sbin  tmp  volum01
etc  lib64  mnt         root  srv   usr  volum02  #数据卷

#Ctrl+P+Q
[root@localhost _data]# docker run -it --name docker01_1 --volumes-from docker01 wxk/centos
[root@55f38197f41f /]#  ls
bin  home   lost+found  opt   run   sys  var
dev  lib    media       proc  sbin  tmp  volum01
etc  lib64  mnt         root  srv   usr  volum02

##测试
[root@localhost _data]# docker ps
CONTAINER ID   IMAGE        COMMAND                  CREATED          STATUS          PORTS     NAMES
55f38197f41f   wxk/centos   "/bin/sh -c /bin/bash"   51 seconds ago   Up 50 seconds             docker01_1
290492261f70   wxk/centos   "/bin/sh -c /bin/bash"   3 minutes ago    Up 3 minutes              docker01
[root@localhost _data]# docker attach 290492261f70
[root@290492261f70 /]# ls
bin  home   lost+found  opt   run   sys  var
dev  lib    media       proc  sbin  tmp  volum01
etc  lib64  mnt         root  srv   usr  volum02
[root@290492261f70 /]# cd volum02
[root@290492261f70 volum02]# ls
[root@290492261f70 volum02]# touch docker01
[root@290492261f70 volum02]# ls
docker01
[root@290492261f70 volum02]# read escape sequence
[root@localhost _data]# docker attach 55f38197f41f
[root@55f38197f41f /]# ls
bin  home   lost+found  opt   run   sys  var
dev  lib    media       proc  sbin  tmp  volum01
etc  lib64  mnt         root  srv   usr  volum02
[root@55f38197f41f /]# cd volum02
[root@55f38197f41f volum02]# ls
docker01   ##同步


##双向拷贝
##后续 docker run -it --name docker01_2 --volumes-from docker01 wxk/centos   在docker01_2创建  也会同步到docker01  docker01_1
```

![image-20201217145157391](docker.assets\image-20201217145157391.png)

+ 多个mysql实现数据共享

  ```shell
  docker run -d -p 3310:3306 -v /etc/mysql/conf.d -v /var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql:latest
  
  docker run -d -p 3310:3306 -e MYSQL_ROOT_PASSWORD=123456 --name mysql02 --volumes-from mysql01 mysql:latest
  ```

  ![image-20201217145612622](docker.assets\image-20201217145612622.png)

#### DockerFile

用来构建docker镜像的文件     命令参数脚本

构建步骤：

+ 编写dockerfile文件
+ docker build构建镜像
+ docker run 
+ docker push  发布镜像

部分镜像都是基础包    缺少功能    可以尝试搭建自己的镜像（centos + mysql + es + nginx）

##### DockerFile构建过程  

+ 基础知识

  + 大写 

  + 由上自下    每个指令都会创建提交一个新的镜像层

    <img src="docker.assets\image-20201217150735973.png" alt="image-20201217150735973" style="zoom: 80%;" />

  + dockerfile 面向开发    发布项目=> 编写dockerfile 文件

  + 企业交付的标准

  + ![image-20201217151108853](docker.assets\image-20201217151108853.png)

+ 命令

  <img src="docker.assets\image-20201217150553838.png" alt="image-20201217150553838" style="zoom: 80%;" />

  ![image-20201217151740104](docker.assets\image-20201217151740104.png)

  

##### 构建自己的centos

+ scratch  基础镜像

  <img src="docker.assets\image-20201217152049429.png" alt="image-20201217152049429" style="zoom: 80%;" />

+ 构建

  + 编写配置文件

    ```shell
    [root@localhost dockerfile]# cat mydockerfile-centos 
    FROM centos
    MAINTAINER wangxaingkun<2274474105@qq.com>
    
    ENV MYPATH /usr/local          ###工作目录    cd
    WORKDIR $MYPATH
    
    RUN yum -y install vim
    RUN yum -y install net-tools
    
    EXPOSE 80
    
    CMD echo $MYPATH
    CMD echo "----end-------"
    CMD /bin/bash
    [root@localhost dockerfile]# 
    ```

  + 构建镜像docker build -f

    ```shell
    [root@localhost dockerfile]# docker build -f mydockerfile-centos -t mycentos:0.1 .
    Sending build context to Docker daemon  2.048kB
    Step 1/10 : FROM centos
     ---> 300e315adb2f
    Step 2/10 : MAINTAINER wangxaingkun<2274474105@qq.com>
     ---> Using cache
     ---> 359d62b98b31
    Step 3/10 : ENV MYPATH /usr/local
     ---> Using cache
     ---> 516ec33e97f7
    Step 4/10 : WORKDIR $MYPATH
     ---> Using cache
     ---> e69fb82adb9a
    Step 5/10 : RUN yum -y install vim
     ---> Running in 2affbbfd20be
    CentOS Linux 8 - AppStream                      268 kB/s | 6.3 MB     00:23    
    CentOS Linux 8 - BaseOS                         661 kB/s | 2.3 MB     00:03    
    CentOS Linux 8 - Extras                         1.6 kB/s | 8.6 kB     00:05    
    Dependencies resolved.
    ================================================================================
     Package             Arch        Version                   Repository      Size
    ================================================================================
    Installing:
     vim-enhanced        x86_64      2:8.0.1763-15.el8         appstream      1.4 M
    Installing dependencies:
     gpm-libs            x86_64      1.20.7-15.el8             appstream       39 k
     vim-common          x86_64      2:8.0.1763-15.el8         appstream      6.3 M
     vim-filesystem      noarch      2:8.0.1763-15.el8         appstream       48 k
     which               x86_64      2.21-12.el8               baseos          49 k
    
    Transaction Summary
    ================================================================================
    Install  5 Packages
    
    Total download size: 7.8 M
    Installed size: 30 M
    Downloading Packages:
    (1/5): gpm-libs-1.20.7-15.el8.x86_64.rpm        6.3 kB/s |  39 kB     00:06    
    (2/5): vim-filesystem-8.0.1763-15.el8.noarch.rp 2.3 kB/s |  48 kB     00:21    
    (3/5): which-2.21-12.el8.x86_64.rpm             385 kB/s |  49 kB     00:00    
    (4/5): vim-enhanced-8.0.1763-15.el8.x86_64.rpm  6.3 kB/s | 1.4 MB     03:41    
    ^C
    [root@localhost dockerfile]# docker build -f mydockerfile-centos -t mycentos:0.1 .
    Sending build context to Docker daemon  2.048kB
    Step 1/10 : FROM centos
     ---> 300e315adb2f
    Step 2/10 : MAINTAINER wangxaingkun<2274474105@qq.com>
     ---> Using cache
     ---> 359d62b98b31
    Step 3/10 : ENV MYPATH /usr/local
     ---> Using cache
     ---> 516ec33e97f7
    Step 4/10 : WORKDIR $MYPATH
     ---> Using cache
     ---> e69fb82adb9a
    Step 5/10 : RUN yum -y install vim
     ---> Running in c0e745d5460a
    CentOS Linux 8 - AppStream                      1.4 MB/s | 6.3 MB     00:04    
    CentOS Linux 8 - BaseOS                          33 kB/s | 2.3 MB     01:11    
    CentOS Linux 8 - Extras                          10 kB/s | 8.6 kB     00:00    
    Dependencies resolved.
    ================================================================================
     Package             Arch        Version                   Repository      Size
    ================================================================================
    Installing:
     vim-enhanced        x86_64      2:8.0.1763-15.el8         appstream      1.4 M
    Installing dependencies:
     gpm-libs            x86_64      1.20.7-15.el8             appstream       39 k
     vim-common          x86_64      2:8.0.1763-15.el8         appstream      6.3 M
     vim-filesystem      noarch      2:8.0.1763-15.el8         appstream       48 k
     which               x86_64      2.21-12.el8               baseos          49 k
    
    Transaction Summary
    ================================================================================
    Install  5 Packages
    
    Total download size: 7.8 M
    Installed size: 30 M
    Downloading Packages:
    (1/5): gpm-libs-1.20.7-15.el8.x86_64.rpm        207 kB/s |  39 kB     00:00    
    (2/5): vim-filesystem-8.0.1763-15.el8.noarch.rp  85 kB/s |  48 kB     00:00    
    (3/5): vim-enhanced-8.0.1763-15.el8.x86_64.rpm  1.8 MB/s | 1.4 MB     00:00    
    (4/5): which-2.21-12.el8.x86_64.rpm             371 kB/s |  49 kB     00:00    
    (5/5): vim-common-8.0.1763-15.el8.x86_64.rpm    1.3 MB/s | 6.3 MB     00:05    
    warning: /var/cache/dnf/appstream-02e86d1c976ab532/packages/gpm-libs-1.20.7-15.el8.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID 8483c65d: NOKEY
    --------------------------------------------------------------------------------
    Total                                           1.3 MB/s | 7.8 MB     00:06     
    CentOS Linux 8 - AppStream                       66 kB/s | 1.6 kB     00:00    
    Importing GPG key 0x8483C65D:
     Userid     : "CentOS (CentOS Official Signing Key) <security@centos.org>"
     Fingerprint: 99DB 70FA E1D7 CE22 7FB6 4882 05B5 55B3 8483 C65D
     From       : /etc/pki/rpm-gpg/RPM-GPG-KEY-centosofficial
    Key imported successfully
    Running transaction check
    Transaction check succeeded.
    Running transaction test
    Transaction test succeeded.
    Running transaction
      Preparing        :                                                        1/1 
      Installing       : which-2.21-12.el8.x86_64                               1/5 
      Installing       : vim-filesystem-2:8.0.1763-15.el8.noarch                2/5 
      Installing       : vim-common-2:8.0.1763-15.el8.x86_64                    3/5 
      Installing       : gpm-libs-1.20.7-15.el8.x86_64                          4/5 
      Running scriptlet: gpm-libs-1.20.7-15.el8.x86_64                          4/5 
      Installing       : vim-enhanced-2:8.0.1763-15.el8.x86_64                  5/5 
      Running scriptlet: vim-enhanced-2:8.0.1763-15.el8.x86_64                  5/5 
      Running scriptlet: vim-common-2:8.0.1763-15.el8.x86_64                    5/5 
      Verifying        : gpm-libs-1.20.7-15.el8.x86_64                          1/5 
      Verifying        : vim-common-2:8.0.1763-15.el8.x86_64                    2/5 
      Verifying        : vim-enhanced-2:8.0.1763-15.el8.x86_64                  3/5 
      Verifying        : vim-filesystem-2:8.0.1763-15.el8.noarch                4/5 
      Verifying        : which-2.21-12.el8.x86_64                               5/5 
    
    Installed:
      gpm-libs-1.20.7-15.el8.x86_64         vim-common-2:8.0.1763-15.el8.x86_64    
      vim-enhanced-2:8.0.1763-15.el8.x86_64 vim-filesystem-2:8.0.1763-15.el8.noarch
      which-2.21-12.el8.x86_64             
    
    Complete!
    Removing intermediate container c0e745d5460a
     ---> 63374c1c75eb
    Step 6/10 : RUN yum -y install net-tools
     ---> Running in 64835692a49f
    Last metadata expiration check: 0:00:17 ago on Thu Dec 17 07:48:18 2020.
    Dependencies resolved.
    ================================================================================
     Package         Architecture Version                        Repository    Size
    ================================================================================
    Installing:
     net-tools       x86_64       2.0-0.52.20160912git.el8       baseos       322 k
    
    Transaction Summary
    ================================================================================
    Install  1 Package
    
    Total download size: 322 k
    Installed size: 942 k
    Downloading Packages:
    net-tools-2.0-0.52.20160912git.el8.x86_64.rpm    37 kB/s | 322 kB     00:08    
    --------------------------------------------------------------------------------
    Total                                            35 kB/s | 322 kB     00:09     
    Running transaction check
    Transaction check succeeded.
    Running transaction test
    Transaction test succeeded.
    Running transaction
      Preparing        :                                                        1/1 
      Installing       : net-tools-2.0-0.52.20160912git.el8.x86_64              1/1 
      Running scriptlet: net-tools-2.0-0.52.20160912git.el8.x86_64              1/1 
      Verifying        : net-tools-2.0-0.52.20160912git.el8.x86_64              1/1 
    
    Installed:
      net-tools-2.0-0.52.20160912git.el8.x86_64                                     
    Complete!
    Removing intermediate container 64835692a49f
     ---> 295fde789994
    Step 7/10 : EXPOSE 80
     ---> Running in f811dba58f81
    Removing intermediate container f811dba58f81
     ---> 11b4bbc45e7b
    Step 8/10 : CMD echo $MYPATH
     ---> Running in 30575c4d346d
    Removing intermediate container 30575c4d346d
     ---> f0d0258b5a7e
    Step 9/10 : CMD echo "----end-------"
     ---> Running in 7752167a9e1c
    Removing intermediate container 7752167a9e1c
     ---> 0a8ac1ab62f2
    Step 10/10 : CMD /bin/bash
     ---> Running in f1c18483cd26
    Removing intermediate container f1c18483cd26
     ---> eefd37045130
    Successfully built eefd37045130
    Successfully tagged mycentos:0.1
    ```

    

  + 测试运行

    ```shell
    [root@localhost _data]# docker images
    REPOSITORY            TAG       IMAGE ID       CREATED              SIZE
    mycentos              0.1       eefd37045130   About a minute ago   291MB
    wxk/centos            latest    949e9de857fd   2 hours ago          209MB
    mytomcat              1.0       22ae1e7bcd43   3 hours ago          654MB
    nginx                 latest    ae2feff98a0c   35 hours ago         133MB
    tomcat                latest    6d15a1d68603   4 days ago           649MB
    mysql                 latest    ab2f358b8612   5 days ago           545MB
    redis                 latest    ef47f3b6dc11   5 days ago           104MB
    centos                latest    300e315adb2f   9 days ago           209MB
    portainer/portainer   latest    62771b0b9b09   4 months ago         79.1MB
    elasticsearch         7.6.2     f29a1ee41030   8 months ago         791MB
    hello-world           latest    bf756fb1ae65   11 months ago        13.3kB
    [root@localhost _data]# docker run -it 300e315adb2f
    [root@f632faf206b7 /]# ifconfig
    bash: ifconfig: command not found
    
    
    ##自己的   增加部分功能
    [root@localhost _data]# docker run -it eefd37045130
    [root@8d048d5ac3fe local]# pwd  
    /usr/local
    [root@8d048d5ac3fe local]# ifconfig
    eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
            inet 172.17.0.2  netmask 255.255.0.0  broadcast 172.17.255.255
            ether 02:42:ac:11:00:02  txqueuelen 0  (Ethernet)
            RX packets 8  bytes 656 (656.0 B)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 0  bytes 0 (0.0 B)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
    
    lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
            inet 127.0.0.1  netmask 255.0.0.0
            loop  txqueuelen 1000  (Local Loopback)
            RX packets 0  bytes 0 (0.0 B)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 0  bytes 0 (0.0 B)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
    
    [root@8d048d5ac3fe local]# vim wxk.txt
    
    ```

  + CMD与ENTRYPOINT的区别

    ![image-20201217154109813](docker.assets\image-20201217154109813.png)

    ![image-20201217154240675](docker.assets\image-20201217154240675.png)

    ![image-20201217154316955](docker.assets\image-20201217154316955.png)

##### dockerfile构建发布tomcat镜像

+ tomcat   jdk  安装包   .tar.gz

+ dockerfile

  ```shell
  [root@localhost tomcat]# cat Dockerfile 
  FROM centos
  MAINTAINER wangxiangkun<2274474105@qq.com>
  
  COPY readme.txt /usr/local/readme.txt
  ADD jdk-8u131-linux-x64.tar.gz /usr/local/
  ADD apache-tomcat-7.0.70.tar.gz /usr/local/
  
  RUN yum -y install vim
  ENV MYPATH /usr/local
  WORKDIR $MYPATH
  
  ENV JAVA_HOME /usr/local/jdk_1.8.0_131
  ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
  ENV CATALINA_HOME /usr/local/apache-tomcat-7.0.70
  ENV CATALINA_BASH /usr/local/apache-tomcat-7.0.70
  ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin
  
  EXPOSE 8080
  
  CMD /usr/local/apache-tomcat-7.0.70/bin/startup.sh && tail -F /url/local/apache-tomcat-7.0.70/bin/logs/catalina.out 
  
  ```

+ 构建镜像

  ```shell
  #文件名  Dockerfile  不用指定
  docker build -t diytomcat .
  
  [root@localhost tomcat]# docker images
  REPOSITORY            TAG       IMAGE ID       CREATED          SIZE
  diytomcat             latest    43428a375fbc   35 seconds ago   657MB
  <none>                <none>    e7bf83ee43f3   2 minutes ago    516MB
  mycentos              0.1       eefd37045130   6 hours ago      291MB
  wxk/centos            latest    949e9de857fd   8 hours ago      209MB
  mytomcat              1.0       22ae1e7bcd43   9 hours ago      654MB
  nginx                 latest    ae2feff98a0c   42 hours ago     133MB
  tomcat                latest    6d15a1d68603   4 days ago       649MB
  mysql                 latest    ab2f358b8612   5 days ago       545MB
  redis                 latest    ef47f3b6dc11   5 days ago       104MB
  centos                latest    300e315adb2f   9 days ago       209MB
  portainer/portainer   latest    62771b0b9b09   4 months ago     79.1MB
  elasticsearch         7.6.2     f29a1ee41030   8 months ago     791MB
  hello-world           latest    bf756fb1ae65   11 months ago    13.3kB
  [root@localhost tomcat]# 
  
  ```

+ 启动镜像

  ```shell
  [root@localhost tomcat]# docker run -d -p 9091:8080 --name ddddmjtomcat -v /home/wxk/build/tomcat/test:/url/local/apache-tomcat-7.0.70/webapps/test -v /home/wxk/build/tomcat/tomcatlogs:/url/local/apache-tomcat-7.0.70/logs dddtomcat
  
  
  docker run -d -p 9090:8080 --name wxktomcat4 -v /home/wxk/build/tomcat/test:/usr/local/apache-tomcat-7.0.70/webapps/test -v /home/wxk/build/tomcat/tomcatlogs:/usr/local/apache-tomcat-7.0.70/logs diytomcat
  db43e0a087ed17e9e0e542a3e49c82655e983c23a5cead64e4515a076fe23e1f
  
  
  
  d561e127b7329cd68d2159438e73940ed8891bdba21ec58728b6307f0c444fc3
  [root@localhost tomcat]# ls
  apache-tomcat-7.0.70.tar.gz  jdk-7u79-linux-x64.gz       readme.txt  tomcatlogs
  Dockerfile                   jdk-8u131-linux-x64.tar.gz  test
  [root@localhost tomcat]# 
  
  
  [root@localhost tomcat]# docker ps
  CONTAINER ID   IMAGE          COMMAND                  CREATED              STATUS              PORTS                    NAMES
  d561e127b732   diytomcat      "/bin/sh -c '/usr/lo鈥   About a minute ago   Up About a minute   0.0.0.0:9090->8080/tcp   wxktomcat
  8d048d5ac3fe   eefd37045130   "/bin/sh -c /bin/bash"   6 hours ago          Up 6 hours          80/tcp                   busy_wilbur
  [root@localhost tomcat]# docker exec -it d561e127b732 /bin/bash
  [root@d561e127b732 local]# pwd
  /usr/local
  [root@d561e127b732 local]# ll
  bash: ll: command not found
  [root@d561e127b732 local]# ls
  apache-tomcat-7.0.70  etc    include       lib    libexec     sbin   src
  bin                   games  jdk1.8.0_131  lib64  readme.txt  share
  [root@d561e127b732 local]# ls -l
  total 0
  drwxr-xr-x 1 root root  21 Dec 17 13:59 apache-tomcat-7.0.70
  drwxr-xr-x 2 root root   6 Nov  3 15:22 bin
  drwxr-xr-x 2 root root   6 Nov  3 15:22 etc
  drwxr-xr-x 2 root root   6 Nov  3 15:22 games
  drwxr-xr-x 2 root root   6 Nov  3 15:22 include
  drwxr-xr-x 8   10  143 255 Mar 15  2017 jdk1.8.0_131
  drwxr-xr-x 2 root root   6 Nov  3 15:22 lib
  drwxr-xr-x 3 root root  17 Dec  4 17:37 lib64
  drwxr-xr-x 2 root root   6 Nov  3 15:22 libexec
  -rw-r--r-- 1 root root   0 Dec 17 13:44 readme.txt
  drwxr-xr-x 2 root root   6 Nov  3 15:22 sbin
  drwxr-xr-x 5 root root  49 Dec  4 17:37 share
  drwxr-xr-x 2 root root   6 Nov  3 15:22 src
  [root@d561e127b732 local]# cd apache-tomcat-7.0.70/
  [root@d561e127b732 apache-tomcat-7.0.70]# ls
  LICENSE  RELEASE-NOTES  bin   lib   temp     work
  NOTICE   RUNNING.txt    conf  logs  webapps
  [root@d561e127b732 apache-tomcat-7.0.70]# 
  ```

+ 访问测试

  ![image-20201217222229196](docker.assets\image-20201217222229196.png)

+ 发布项目 

  ```shell
  web.xml
  [root@localhost tomcat]# ls -l
  total 339828
  -rw-r--r-- 1 root root   8924465 Dec 17 21:42 apache-tomcat-7.0.70.tar.gz
  -rw-r--r-- 1 root root       643 Dec 17 22:13 Dockerfile
  -rw-r--r-- 1 root root 153512879 Dec 17 21:42 jdk-7u79-linux-x64.gz
  -rw-r--r-- 1 root root 185540433 Dec 17 21:49 jdk-8u131-linux-x64.tar.gz
  -rw-r--r-- 1 root root         0 Dec 17 21:44 readme.txt
  drwxr-xr-x 2 root root         6 Dec 17 22:05 test    ##发布在这里就可以
  drwxr-xr-x 2 root root        26 Dec 17 22:05 tomcatlogs  ##日志在这里
  [root@localhost tomcat]# 
  
  
  
  ```

  ```
  index.jsp
  
  <%@ page language="java" contentType="text/html; charset=UTF-8"
      pageEncoding="UTF-8"%>
  <!DOCTYPE html>
  <html>
  <head>
  <meta charset="utf-8">
  <title>菜鸟教程(runoob.com)</title>
  </head>
  <body>
  Hello World!<br/>
  <%
  out.println("你的 IP 地址 " + request.getRemoteAddr());
  %>
  </body>
  </html>
  ```

  ```shell
  #挂载错误？？？？？？？？？？？？？？？？？？？？？？？？？？？？？？？？？？？？？？？？？？？？？？？？？
  ```

+ 发布镜像



#### 小结

<img src="docker.assets\image-20201218112025366.png" alt="image-20201218112025366" style="zoom:80%;" />



<img src="docker.assets\image-20201218112330809.png" alt="image-20201218112330809" style="zoom:80%;" />

#### Docker网络（容器编排  集群部署 准备工作）

#####Docker0

+ 移除所有容器

  ```shell
  [root@localhost tomcat7]# docker rm -f $(docker ps -aq)
  ```

+ 移除所有镜像

  ```shell
  [root@localhost tomcat7]# docker rmi -f $(docker images -aq)
  ```

+ 测试

  ```shell
  [root@localhost tomcat7]# docker images
  REPOSITORY   TAG       IMAGE ID   CREATED   SIZE
  [root@localhost tomcat7]# ip addr
  1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
      link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
      inet 127.0.0.1/8 scope host lo      #本机回环地址
         valid_lft forever preferred_lft forever
      inet6 ::1/128 scope host 
         valid_lft forever preferred_lft forever
  2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
      link/ether 00:0c:29:37:c0:ff brd ff:ff:ff:ff:ff:ff
      inet 192.168.254.133/24 brd 192.168.254.255 scope global noprefixroute ens33
         valid_lft forever preferred_lft forever
      inet6 fe80::a7ae:5ed6:1894:72ee/64 scope link noprefixroute 
         valid_lft forever preferred_lft forever
  3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default 
      link/ether 02:42:94:26:a9:9a brd ff:ff:ff:ff:ff:ff
      inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
         valid_lft forever preferred_lft forever    #docker0地址
      inet6 fe80::42:94ff:fe26:a99a/64 scope link 
         valid_lft forever preferred_lft forever
  ```

  <img src="docker.assets\image-20201218113057711.png" alt="image-20201218113057711" style="zoom: 75%;" />

  <img src="docker.assets\image-20201218113124365.png" alt="image-20201218113124365" style="zoom:80%;" />

  + 启动tomcat

    ```shell
    [root@localhost tomcat7]# docker run -d -P --name tomcat01 tomcat
    
    #查看容器内部网络地址        每个容器有一个eth@if***   <=  外部能ping通吗
    [root@localhost tomcat7]# docker exec -it tomcat01 ip addr
    
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
           valid_lft forever preferred_lft forever
    90: eth0@if91: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
        link/ether 02:42:ac:11:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
        inet 172.17.0.2/16 brd 172.17.255.255 scope global eth0
           valid_lft forever preferred_lft forever
    [root@localhost tomcat7]# 
    
    #Linux可以ping通容器内部
     [root@localhost tomcat7]# ping 172.17.0.2
    PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
    64 bytes from 172.17.0.2: icmp_seq=1 ttl=64 time=0.122 ms
    64 bytes from 172.17.0.2: icmp_seq=2 ttl=64 time=0.036 ms
    64 bytes from 172.17.0.2: icmp_seq=3 ttl=64 time=0.097 ms
    
     
    #再次查看宿主机网络地址 
    [root@localhost tomcat7]# ip addr
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
           valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host 
           valid_lft forever preferred_lft forever
    2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
        link/ether 00:0c:29:37:c0:ff brd ff:ff:ff:ff:ff:ff
        inet 192.168.254.133/24 brd 192.168.254.255 scope global noprefixroute ens33
           valid_lft forever preferred_lft forever
        inet6 fe80::a7ae:5ed6:1894:72ee/64 scope link noprefixroute 
           valid_lft forever preferred_lft forever
    3: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
        link/ether 02:42:94:26:a9:9a brd ff:ff:ff:ff:ff:ff
        inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
           valid_lft forever preferred_lft forever
        inet6 fe80::42:94ff:fe26:a99a/64 scope link 
           valid_lft forever preferred_lft forever
    91: vethfe61e0a@if90: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default 
        link/ether 4a:d8:66:73:fb:ee brd ff:ff:ff:ff:ff:ff link-netnsid 0
        inet6 fe80::48d8:66ff:fe73:fbee/64 scope link 
           valid_lft forever preferred_lft forever
    [root@localhost tomcat7]# 
    
    #再启动一个tomcat  又多一对网卡
    [root@localhost tomcat7]# docker exec -it tomcat02 ip addr
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
           valid_lft forever preferred_lft forever
    92: eth0@if93: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
        link/ether 02:42:ac:11:00:03 brd ff:ff:ff:ff:ff:ff link-netnsid 0
        inet 172.17.0.3/16 brd 172.17.255.255 scope global eth0
           valid_lft forever preferred_lft forever
    [root@localhost tomcat7]# 
    
    #再次查看宿主机网络地址 
    [root@localhost tomcat7]# ip addr
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
           valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host 
           valid_lft forever preferred_lft forever
    2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
        link/ether 00:0c:29:37:c0:ff brd ff:ff:ff:ff:ff:ff
        inet 192.168.254.133/24 brd 192.168.254.255 scope global noprefixroute ens33
           valid_lft forever preferred_lft forever
        inet6 fe80::a7ae:5ed6:1894:72ee/64 scope link noprefixroute 
           valid_lft forever preferred_lft forever
    3: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
        link/ether 02:42:94:26:a9:9a brd ff:ff:ff:ff:ff:ff
        inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
           valid_lft forever preferred_lft forever
        inet6 fe80::42:94ff:fe26:a99a/64 scope link 
           valid_lft forever preferred_lft forever
    91: vethfe61e0a@if90: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default 
        link/ether 4a:d8:66:73:fb:ee brd ff:ff:ff:ff:ff:ff link-netnsid 0
        inet6 fe80::48d8:66ff:fe73:fbee/64 scope link 
           valid_lft forever preferred_lft forever
    93: veth8f3848f@if92: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default 
        link/ether f6:99:07:7a:b6:1e brd ff:ff:ff:ff:ff:ff link-netnsid 1
        inet6 fe80::f499:7ff:fe7a:b61e/64 scope link 
           valid_lft forever preferred_lft forever
    [root@localhost tomcat7]# 
    ```

  + 原理

    ```shell
    #原理  192.168.0.1/16    路由器IP          存放255*255-回环            
    	  192.168.0.*     手机等其它设备  同一网段
    	  
    #veth-pair 技术   一对的虚拟技术接口     一段连着协议   一段彼此相连
    #veth-pair 充当一个桥梁    链接各种虚拟网络设备   
    ```

  + tomcat01  tomcat02 之间是否可以ping通

    ```shell
    docker exec -it tomcat02 ping 172.17.0.2     #可以
    
    [root@localhost tomcat7]# docker exec -it tomcat02 ping 172.17.0.2 
    PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
    64 bytes from 172.17.0.2: icmp_seq=1 ttl=64 time=0.104 ms
    64 bytes from 172.17.0.2: icmp_seq=2 ttl=64 time=0.045 ms
    
    ```

    <img src="docker.assets\image-20201218115104736.png" alt="image-20201218115104736" style="zoom:67%;" />

    **结论**tomcat01、tomcat02  公用一个 路由器 docker0        Linux 桥接   Docker中的网络接口   都是虚拟的  虚拟转发效率高     容器移除  网卡对消失

    ​		所有容器在不指定网络的情况下     都是由docker0路由    

    <img src="docker.assets\image-20201218115759939.png" alt="image-20201218115759939" style="zoom:80%;" />

    

##### 容器互联--link

![image-20201218120802876](docker.assets\image-20201218120802876.png)

+ tomcat  01 02 直接ping   BYname

  ```shell
  [root@localhost tomcat7]# clear
  [root@localhost tomcat7]# docker ps
  CONTAINER ID   IMAGE     COMMAND             CREATED         STATUS         PORTS                     NAMES
  0effa64ed83a   tomcat    "catalina.sh run"   4 minutes ago   Up 4 minutes   0.0.0.0:49156->8080/tcp   tomcat02
  f171fb3fabd8   tomcat    "catalina.sh run"   8 minutes ago   Up 8 minutes   0.0.0.0:49155->8080/tcp   tomcat01
  [root@localhost tomcat7]# docker exec -it tomcat02 ping tomcat01
  ping: tomcat01: Name or service not known   ##ping不通
  [root@localhost tomcat7]# 
  ```

+ 如何解决

  ```shell
  [root@localhost tomcat7]# docker run -d -P --name tomcat03 --link tomcat02 tomcat
  b74d4cdb82ad701d57ab6946407c7672f9685e71bfe5da5742cbb062ac133eda
  [root@localhost tomcat7]#docker exec -it tomcat03 ping tomcat02
  PING tomcat02 (172.17.0.3) 56(84) bytes of data.
  64 bytes from tomcat02 (172.17.0.3): icmp_seq=1 ttl=64 time=0.098 ms
  64 bytes from tomcat02 (172.17.0.3): icmp_seq=2 ttl=64 time=0.108 ms
  ...
  
  #02  不能ping 03
  ```

+ docker network

  ```shell
  [root@localhost tomcat7]# docker network ls
  NETWORK ID     NAME      DRIVER    SCOPE
  1dbb45624e8b   bridge    bridge    local       
  a0cfe38ed259   host      host      local
  91b269b1c8ea   none      null      local
  [root@localhost tomcat7]# docker network inspect 1dbb45624e8b
  
  ##--link 本质          ==    不建议了
  ##查看host配置
  [root@localhost tomcat7]# docker exec -it tomcat03 cat /etc/hosts
  127.0.0.1       localhost
  ::1     localhost ip6-localhost ip6-loopback
  fe00::0 ip6-localnet
  ff00::0 ip6-mcastprefix
  ff02::1 ip6-allnodes
  ff02::2 ip6-allrouters
  172.17.0.3      tomcat02 0effa64ed83a       ##--link  
  172.17.0.4      b74d4cdb82ad
  
  
  [root@localhost tomcat7]# docker exec -it tomcat02 cat /etc/hosts
  127.0.0.1       localhost
  ::1     localhost ip6-localhost ip6-loopback
  fe00::0 ip6-localnet
  ff00::0 ip6-mcastprefix
  ff02::1 ip6-allnodes
  ff02::2 ip6-allrouters
  172.17.0.3      0effa64ed83a
  [root@localhost tomcat7]#    
  
  ```

##### 自定义网络

+ docker0  不支持容器名  访问（ping）

+ 容器互联  查看所有的docker网络

  ```shell
  [root@localhost ~]# docker network ls
  NETWORK ID     NAME      DRIVER    SCOPE
  1dbb45624e8b   bridge    bridge    local
  a0cfe38ed259   host      host      local
  91b269b1c8ea   none      null      local
  [root@localhost ~]# 
  
  ```

+ 网络模式

  + 桥接模式     （推荐）
  + none 不配置网络
  + host  和宿主机共享网络
  + container 容器网络连通     不建议

+ 初始化环境

  ```shell
  [root@localhost ~]# docker rm -f $(docker ps -aq)
  b74d4cdb82ad
  0effa64ed83a
  f171fb3fabd8
  [root@localhost ~]# docker ps
  CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
  [root@localhost ~]# ip addr
  1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
      link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
      inet 127.0.0.1/8 scope host lo
         valid_lft forever preferred_lft forever
      inet6 ::1/128 scope host 
         valid_lft forever preferred_lft forever
  2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
      link/ether 00:0c:29:37:c0:ff brd ff:ff:ff:ff:ff:ff
      inet 192.168.254.133/24 brd 192.168.254.255 scope global noprefixroute ens33
         valid_lft forever preferred_lft forever
      inet6 fe80::a7ae:5ed6:1894:72ee/64 scope link noprefixroute 
         valid_lft forever preferred_lft forever
  3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default 
      link/ether 02:42:94:26:a9:9a brd ff:ff:ff:ff:ff:ff
      inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
         valid_lft forever preferred_lft forever
      inet6 fe80::42:94ff:fe26:a99a/64 scope link 
         valid_lft forever preferred_lft forever
  [root@localhost ~]# 
  
  ```

+ 测试自定义网络 

  ```shell
  #默认
  docker run -d -P --name tomcat01  tomcat
  docker run -d -P --name tomcat01 --net bridge tomcat   
  
  #自定义网络  /  --link
  [root@localhost ~]# docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet                              ##范围  192.168。0.2-> 192.168.255.255
  88888c6650682dba347429fcbed6281ae3f68d977c4395cf4de8c081f7f8758e
  [root@localhost ~]# docker network ls
  NETWORK ID     NAME      DRIVER    SCOPE
  1dbb45624e8b   bridge    bridge    local
  a0cfe38ed259   host      host      local
  88888c665068   mynet     bridge    local
  91b269b1c8ea   none      null      local
  
  [root@localhost ~]# docker network inspect mynet
  [
      {
          "Name": "mynet",
          "Id": "88888c6650682dba347429fcbed6281ae3f68d977c4395cf4de8c081f7f8758e",
          "Created": "2020-12-18T13:42:21.401091237+08:00",
          "Scope": "local",
          "Driver": "bridge",
          "EnableIPv6": false,
          "IPAM": {
              "Driver": "default",
              "Options": {},
              "Config": [
                  {
                      "Subnet": "192.168.0.0/16",
                      "Gateway": "192.168.0.1"
                  }
              ]
          },
          "Internal": false,
          "Attachable": false,
          "Ingress": false,
          "ConfigFrom": {
              "Network": ""
          },
          "ConfigOnly": false,
          "Containers": {},
          "Options": {},
          "Labels": {}
      }
  ]
  
  [root@localhost ~]#docker run -d -P --name tomcat-net-01 --net mynet tomcat
  [root@localhost ~]#docker run -d -P --name tomcat-net-02 --net mynet tomcat
  [root@localhost ~]# docker network inspect mynet
  [
      {
          "Name": "mynet",
          "Id": "88888c6650682dba347429fcbed6281ae3f68d977c4395cf4de8c081f7f8758e",
          "Created": "2020-12-18T13:42:21.401091237+08:00",
          "Scope": "local",
       	 。。。。
          "ConfigOnly": false,
          "Containers": {
              "0a0d38aed218223c4c45b90296fa6017e7f6be71e5eb57deaa4b2c5dd8737239": {
                  "Name": "tomcat-net-01",   ##***********
                  "EndpointID": "58c8f1205865923cf44526618b5a9eb6c6e148ed2016a294676fb71b9302f233",
                  "MacAddress": "02:42:c0:a8:00:02",
                  "IPv4Address": "192.168.0.2/16",
                  "IPv6Address": ""
              },
              "9b898f36ea4f8545b752b44daff4e15b52e01cb8a4051dca9f1d75b3db3adc7c": {
                  "Name": "tomcat-net-02",  ##************
                  "EndpointID": "34f476489b7ec5936cc9add17200d0ef0f7bab7fdda5b88c6a3ee70f9e345691",
                  "MacAddress": "02:42:c0:a8:00:03",
                  "IPv4Address": "192.168.0.3/16",
                  "IPv6Address": ""
              }
          },
          "Options": {},
          "Labels": {}
      }
  ]
  
  ##测试ping
  [root@localhost ~]# docker exec -it tomcat-net-01 ping 192.168.0.3
  PING 192.168.0.3 (192.168.0.3) 56(84) bytes of data.
  64 bytes from 192.168.0.3: icmp_seq=1 ttl=64 time=0.494 ms
  64 bytes from 192.168.0.3: icmp_seq=2 ttl=64 time=0.042 ms
  64 bytes from 192.168.0.3: icmp_seq=3 ttl=64 time=0.043 ms
  64 bytes from 192.168.0.3: icmp_seq=4 ttl=64 time=0.043 ms
  64 bytes from 192.168.0.3: icmp_seq=5 ttl=64 time=0.111 ms
  --- 192.168.0.3 ping statistics ---
  11 packets transmitted, 11 received, 0% packet loss, time 24ms
  rtt min/avg/max/mdev = 0.039/0.103/0.494/0.126 ms
  
  $######不用--link   双向ping 通      
  [root@localhost ~]# docker exec -it tomcat-net-01 ping tomcat-net-02        ##OKKKKK
  PING tomcat-net-02 (192.168.0.3) 56(84) bytes of data.
  64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=1 ttl=64 time=0.030 ms
  64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=2 ttl=64 time=0.045 ms
  64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=3 ttl=64 time=0.102 ms
  64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=4 ttl=64 time=0.112 ms
  ```

  ![image-20201218135226565](docker.assets\image-20201218135226565.png)

+ 思考：  网络连通   如何打通上图？？？？？



#####网络连通

+ 目的

  ```shell
  [root@localhost ~]# docker run -d -P --name tomcat01 tomcat
  fd0ecc0b77dee96b6f4f61e0825fe2cca99281b0cde8934f5418e404ad58015e
  [root@localhost ~]# docker run -d -P --name tomcat02 tomcat
  c0816677a484dac7d3f0bcb7db0cca24ef0bf1d1a7a9101ded603c609b69617b
  [root@localhost ~]# docker ps
  CONTAINER ID   IMAGE     COMMAND             CREATED          STATUS          PORTS                     NAMES
  c0816677a484   tomcat    "catalina.sh run"   3 seconds ago    Up 2 seconds    0.0.0.0:49161->8080/tcp   tomcat02
  fd0ecc0b77de   tomcat    "catalina.sh run"   11 seconds ago   Up 10 seconds   0.0.0.0:49160->8080/tcp   tomcat01
  9b898f36ea4f   tomcat    "catalina.sh run"   7 minutes ago    Up 7 minutes    0.0.0.0:49159->8080/tcp   tomcat-net-02
  0a0d38aed218   tomcat    "catalina.sh run"   7 minutes ago    Up 7 minutes    0.0.0.0:49158->8080/tcp   tomcat-net-01
  [root@localhost ~]# docker exec -it tomcat01 ping tomcat02
  ping: tomcat02: Name or service not known
  [root@localhost ~]# docker exec -it tomcat01 ping tomcat-net-01
  ping: tomcat-net-01: Name or service not known
  ###如何ping通
  ```

  ![image-20201218135631516](docker.assets\image-20201218135631516.png)

+ 解决      connect

  ```shell
  [root@localhost ~]# docker network --help
  
  Usage:  docker network COMMAND
  
  Manage networks
  
  Commands:
    connect     Connect a container to a network       ###here   核心
    create      Create a network
    disconnect  Disconnect a container from a network
    inspect     Display detailed information on one or more networks
    ls          List networks
    prune       Remove all unused networks
    rm          Remove one or more networks
  Run 'docker network COMMAND --help' for more information on a command.
  [root@localhost ~]# 
  ```

+ 测试

  ```SHELL
  [root@localhost ~]# docker network connect mynet tomcat01
  [root@localhost ~]# docker network inspect mynet
  [
      {
          "Name": "mynet",
          "Id": "88888c6650682dba347429fcbed6281ae3f68d977c4395cf4de8c081f7f8758e",
          "Created": "2020-12-18T13:42:21.401091237+08:00",
          。。。。。。。。
          "ConfigOnly": false,
          "Containers": {
              "0a0d38aed218223c4c45b90296fa6017e7f6be71e5eb57deaa4b2c5dd8737239": {
                  "Name": "tomcat-net-01",
                  "EndpointID": "58c8f1205865923cf44526618b5a9eb6c6e148ed2016a294676fb71b9302f233",
                  "MacAddress": "02:42:c0:a8:00:02",
                  "IPv4Address": "192.168.0.2/16",
                  "IPv6Address": ""
              },
              "9b898f36ea4f8545b752b44daff4e15b52e01cb8a4051dca9f1d75b3db3adc7c": {
                  "Name": "tomcat-net-02",
                  "EndpointID": "34f476489b7ec5936cc9add17200d0ef0f7bab7fdda5b88c6a3ee70f9e345691",
                  "MacAddress": "02:42:c0:a8:00:03",
                  "IPv4Address": "192.168.0.3/16",
                  "IPv6Address": ""
              },
              "fd0ecc0b77dee96b6f4f61e0825fe2cca99281b0cde8934f5418e404ad58015e": {
                  "Name": "tomcat01",    ### 添加tomcat01  打通
                  "EndpointID": "78695ad2aa8375812c3146bfe996a4bd176fe5bb98dd8c8c44e4b8ecdf295c0c",
                  "MacAddress": "02:42:c0:a8:00:04",
                  "IPv4Address": "192.168.0.4/16",       ###一个容器两个
                  "IPv6Address": ""
              }
          },
          "Options": {},
          "Labels": {}
      }
  ]
  ##测试通过
  [root@localhost ~]# docker exec -it tomcat01 ping tomcat-net-01
  PING tomcat-net-01 (192.168.0.2) 56(84) bytes of data.
  64 bytes from tomcat-net-01.mynet (192.168.0.2): icmp_seq=1 ttl=64 time=0.063 ms
  64 bytes from tomcat-net-01.mynet (192.168.0.2): icmp_seq=2 ttl=64 time=0.057 ms
  64 bytes from tomcat-net-01.mynet (192.168.0.2): icmp_seq=3 ttl=64 time=0.064 ms
  ```

##### Redis集群部署

+ 分片+高可用+负载均衡

  <img src="docker.assets\image-20201218140532020.png" alt="image-20201218140532020" style="zoom: 80%;" />

+ 搭建

  ```shell
  [root@localhost ~]# docker rm -f $(docker ps -aq)
  c0816677a484
  fd0ecc0b77de
  9b898f36ea4f
  0a0d38aed218
  [root@localhost ~]# docker ps
  CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
  
  [root@localhost ~]# docker network create redis --subnet 172.38.0.0/16
  7b3f89a070c52cbbee6ca660c1b3cb70a820ce0cc70c5ded70c61096881fe19f
  [root@localhost ~]# docker network ls
  NETWORK ID     NAME      DRIVER    SCOPE
  1dbb45624e8b   bridge    bridge    local
  a0cfe38ed259   host      host      local
  88888c665068   mynet     bridge    local
  91b269b1c8ea   none      null      local
  7b3f89a070c5   redis     bridge    local
  [root@localhost ~]# docker network inspect redis
  [
      {
          "Name": "redis",
          "Id": "7b3f89a070c52cbbee6ca660c1b3cb70a820ce0cc70c5ded70c61096881fe19f",
          "Created": "2020-12-18T14:08:40.546617047+08:00",
          "Scope": "local",
          "Driver": "bridge",
          "EnableIPv6": false,
          "IPAM": {
              "Driver": "default",
              "Options": {},
              "Config": [
                  {
                      "Subnet": "172.38.0.0/16"
                  }
              ]
          },
          "Internal": false,
          "Attachable": false,
          "Ingress": false,
          "ConfigFrom": {
              "Network": ""
          },
          "ConfigOnly": false,
          "Containers": {},
          "Options": {},
          "Labels": {}
      }
  ]
  
  ```

+ 通过脚本创建6个Redis 配置

  ```shell
  for port in $(seq 1 6); \
  do \
  mkdir -p /mydata/redis/node-${port}/conf
  touch /mydata/redis/node-${port}/conf/redis.conf
  cat << EOF >/mydata/redis/node-${port}/conf/redis.conf
  port 6379 
  bind 0.0.0.0
  cluster-enabled yes 
  cluster-config-file nodes.conf
  cluster-node-timeout 5000
  cluster-announce-ip 172.38.0.1${port}
  cluster-announce-port 6379
  cluster-announce-bus-port 16379
  appendonly yes
  EOF
  done
  
  
  [root@localhost ~]# for port in $(seq 1 6); \
  > do \
  > mkdir -p /mydata/redis/node-${port}/conf
  > touch /mydata/redis/node-${port}/conf/redis.conf
  > cat << EOF >/mydata/redis/node-${port}/conf/redis.conf
  > port 6379 
  > bind 0.0.0.0
  > cluster-enabled yes 
  > cluster-config-file nodes.conf
  > cluster-node-timeout 5000
  > cluster-announce-ip 172.38.0.1${port}
  > cluster-announce-port 6379
  > cluster-announce-bus-port 16379
  > appendonly yes
  > EOF
  > done
  [root@localhost ~]# cd /mydata/redis/
  [root@localhost redis]# ls
  node-1  node-2  node-3  node-4  node-5  node-6
  [root@localhost redis]# cd node-1
  [root@localhost node-1]# ls
  conf
  [root@localhost node-1]# cd conf/
  [root@localhost conf]# ls
  redis.conf
  [root@localhost conf]# cat redis.conf 
  port 6379 
  bind 0.0.0.0
  cluster-enabled yes 
  cluster-config-file nodes.conf
  cluster-node-timeout 5000
  cluster-announce-ip 172.38.0.11
  cluster-announce-port 6379
  cluster-announce-bus-port 16379
  appendonly yes
  [root@localhost conf]# 
  ```

+ 启动服务6个

  ```shell
  ##1
  docker run -p 6371:6379 -p 16371:16379 --name redis-1 \
  -v /mydata/redis/node-1/data:/data \
  -v /mydata/redis/node-1/conf/redis.conf:/etc/redis/redis.conf \
  -d --net redis --ip 172.38.0.11 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf
  
  [root@localhost conf]# docker ps
  CONTAINER ID   IMAGE                    COMMAND                  CREATED          STATUS          PORTS                                              NAMES
  1a1447dd356f   redis:5.0.9-alpine3.11   "docker-entrypoint.s鈥   27 seconds ago   Up 26 seconds   0.0.0.0:6371->6379/tcp, 0.0.0.0:16371->16379/tcp   redis-1
  [root@localhost conf]# 
  
  ##2
  [root@localhost conf]# docker run -p 6372:6379 -p 16372:16379 --name redis-2 \
  > -v /mydata/redis/node-2/data:/data \
  > -v /mydata/redis/node-2/conf/redis.conf:/etc/redis/redis.conf \
  > -d --net redis --ip 172.38.0.12 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf
  29c64aeb03b0a7cba3acfe8b5ea71b9625f3cf16c30f24059f556b1a1089effb
  
  。。。
  docker run -p 6373:6379 -p 16373:16379 --name redis-3 \
  -v /mydata/redis/node-3/data:/data \
  -v /mydata/redis/node-3/conf/redis.conf:/etc/redis/redis.conf \
  -d --net redis --ip 172.38.0.13 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf
  
  docker run -p 6374:6379 -p 16374:16379 --name redis-4 \
  -v /mydata/redis/node-4/data:/data \
  -v /mydata/redis/node-4/conf/redis.conf:/etc/redis/redis.conf \
  -d --net redis --ip 172.38.0.14 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf
  
  docker run -p 6375:6379 -p 16375:16379 --name redis-5 \
  -v /mydata/redis/node-5/data:/data \
  -v /mydata/redis/node-5/conf/redis.conf:/etc/redis/redis.conf \
  -d --net redis --ip 172.38.0.15 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf
  
  
  ##6
  [root@localhost conf]# docker run -p 6376:6379 -p 16376:16379 --name redis-6 \
  > -v /mydata/redis/node-6/data:/data \
  > -v /mydata/redis/node-6/conf/redis.conf:/etc/redis/redis.conf \
  > -d --net redis --ip 172.38.0.16 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf
  a3f3146ec07e0360545f95ad4f187f1c72181da8b6e1454991bda9ad72cdafcd
  [root@localhost conf]# 
  
  
  
  [root@localhost conf]# docker ps
  CONTAINER ID   IMAGE                    COMMAND                  CREATED              STATUS              PORTS                                              NAMES
  a3f3146ec07e   redis:5.0.9-alpine3.11   "docker-entrypoint.s鈥   About a minute ago   Up About a minute   0.0.0.0:6376->6379/tcp, 0.0.0.0:16376->16379/tcp   redis-6
  60f4fe25bfe4   redis:5.0.9-alpine3.11   "docker-entrypoint.s鈥   About a minute ago   Up About a minute   0.0.0.0:6375->6379/tcp, 0.0.0.0:16375->16379/tcp   redis-5
  ae9079de30b6   redis:5.0.9-alpine3.11   "docker-entrypoint.s鈥   2 minutes ago        Up About a minute   0.0.0.0:6374->6379/tcp, 0.0.0.0:16374->16379/tcp   redis-4
  8c6d7a2bedf9   redis:5.0.9-alpine3.11   "docker-entrypoint.s鈥   2 minutes ago        Up 2 minutes        0.0.0.0:6373->6379/tcp, 0.0.0.0:16373->16379/tcp   redis-3
  29c64aeb03b0   redis:5.0.9-alpine3.11   "docker-entrypoint.s鈥   3 minutes ago        Up 3 minutes        0.0.0.0:6372->6379/tcp, 0.0.0.0:16372->16379/tcp   redis-2
  1a1447dd356f   redis:5.0.9-alpine3.11   "docker-entrypoint.s鈥   5 minutes ago        Up 5 minutes        0.0.0.0:6371->6379/tcp, 0.0.0.0:16371->16379/tcp   redis-1
  [root@localhost conf]# 
  ```

  

+ 创建集群

  ```shell
  [root@localhost conf]# docker exec -it redis-1 /bin/sh
  
  /data # ls
  appendonly.aof  nodes.conf
  
  /data # redis-cli --cluster create 172.38.0.11:6379 172.38.0.12:637
  9 172.38.0.13:6379 172.38.0.14:6379 172.38.0.15:6379 172.38.0.16:63
  79 --cluster-replicas 1
  
  >>> Performing hash slots allocation on 6 nodes...
  Master[0] -> Slots 0 - 5460
  Master[1] -> Slots 5461 - 10922
  Master[2] -> Slots 10923 - 16383
  Adding replica 172.38.0.15:6379 to 172.38.0.11:6379
  Adding replica 172.38.0.16:6379 to 172.38.0.12:6379
  Adding replica 172.38.0.14:6379 to 172.38.0.13:6379
  M: afec0ff7c0e7ca28c46739bd88e52ce7495e60ca 172.38.0.11:6379
     slots:[0-5460] (5461 slots) master
  M: ff3a7db3a90d7eb711dca8411130e4bddf89c522 172.38.0.12:6379
     slots:[5461-10922] (5462 slots) master
  M: effe962378f7e705d6308a042c8ff507277ac6d7 172.38.0.13:6379
     slots:[10923-16383] (5461 slots) master
  S: 962349fa657e7f77300c9b1cdd88ce124214c3c0 172.38.0.14:6379
     replicates effe962378f7e705d6308a042c8ff507277ac6d7
  S: 94763f5272bb7bf6424ea150202106b16ae102d1 172.38.0.15:6379
     replicates afec0ff7c0e7ca28c46739bd88e52ce7495e60ca
  S: 55441edf7315c1b4e8f920c1e1c453688e86cc68 172.38.0.16:6379
     replicates ff3a7db3a90d7eb711dca8411130e4bddf89c522
  Can I set the above configuration? (type 'yes' to accept): yes
  
  >>> Nodes configuration updated
  >>> Assign a different config epoch to each node
  >>> Sending CLUSTER MEET messages to join the cluster
  Waiting for the cluster to join
  ..
  >>> Performing Cluster Check (using node 172.38.0.11:6379)
  M: afec0ff7c0e7ca28c46739bd88e52ce7495e60ca 172.38.0.11:6379
     slots:[0-5460] (5461 slots) master
     1 additional replica(s)
  S: 962349fa657e7f77300c9b1cdd88ce124214c3c0 172.38.0.14:6379
     slots: (0 slots) slave
     replicates effe962378f7e705d6308a042c8ff507277ac6d7
  S: 94763f5272bb7bf6424ea150202106b16ae102d1 172.38.0.15:6379
     slots: (0 slots) slave
     replicates afec0ff7c0e7ca28c46739bd88e52ce7495e60ca
  M: ff3a7db3a90d7eb711dca8411130e4bddf89c522 172.38.0.12:6379
     slots:[5461-10922] (5462 slots) master
     1 additional replica(s)
  M: effe962378f7e705d6308a042c8ff507277ac6d7 172.38.0.13:6379
     slots:[10923-16383] (5461 slots) master
     1 additional replica(s)
  S: 55441edf7315c1b4e8f920c1e1c453688e86cc68 172.38.0.16:6379
     slots: (0 slots) slave
     replicates ff3a7db3a90d7eb711dca8411130e4bddf89c522
  [OK] All nodes agree about slots configuration.
  >>> Check for open slots...
  >>> Check slots coverage...
  [OK] All 16384 slots covered.
  
  /data # 
  ```

+ 测试

  ```shell
  /data # redis-cli -c
  
  127.0.0.1:6379> cluster info
  cluster_state:ok
  cluster_slots_assigned:16384
  cluster_slots_ok:16384
  cluster_slots_pfail:0
  cluster_slots_fail:0
  cluster_known_nodes:6
  cluster_size:3
  cluster_current_epoch:6
  cluster_my_epoch:1
  cluster_stats_messages_ping_sent:253
  cluster_stats_messages_pong_sent:245
  cluster_stats_messages_sent:498
  cluster_stats_messages_ping_received:240
  cluster_stats_messages_pong_received:253
  cluster_stats_messages_meet_received:5
  cluster_stats_messages_received:498
  127.0.0.1:6379> cluster nodes
  962349fa657e7f77300c9b1cdd88ce124214c3c0 172.38.0.14:6379@16379 slave effe962378f7e705d6308a042c8ff507277ac6d7 0 1608272834000 4 connected
  94763f5272bb7bf6424ea150202106b16ae102d1 172.38.0.15:6379@16379 slave afec0ff7c0e7ca28c46739bd88e52ce7495e60ca 0 1608272835390 5 connected
  ff3a7db3a90d7eb711dca8411130e4bddf89c522 172.38.0.12:6379@16379 master - 0 1608272835000 2 connected 5461-10922
  afec0ff7c0e7ca28c46739bd88e52ce7495e60ca 172.38.0.11:6379@16379 myself,master - 0 1608272835000 1 connected 0-5460
  effe962378f7e705d6308a042c8ff507277ac6d7 172.38.0.13:6379@16379 master - 0 1608272834525 3 connected 10923-16383            ##3主3从
  55441edf7315c1b4e8f920c1e1c453688e86cc68 172.38.0.16:6379@16379 slave ff3a7db3a90d7eb711dca8411130e4bddf89c522 0 1608272835607 6 connected
  127.0.0.1:6379> 
  
  
  127.0.0.1:6379> set a b
  -> Redirected to slot [15495] located at 172.38.0.13:6379    ##3
  OK
  172.38.0.13:6379> 
  
  #stop  3
  [root@localhost ~]# docker ps
  CONTAINER ID   IMAGE                    COMMAND                  CREATED          STATUS          PORTS                                              NAMES
  a3f3146ec07e   redis:5.0.9-alpine3.11   "docker-entrypoint.s鈥   10 minutes ago   Up 10 minutes   0.0.0.0:6376->6379/tcp, 0.0.0.0:16376->16379/tcp   redis-6
  60f4fe25bfe4   redis:5.0.9-alpine3.11   "docker-entrypoint.s鈥   10 minutes ago   Up 10 minutes   0.0.0.0:6375->6379/tcp, 0.0.0.0:16375->16379/tcp   redis-5
  ae9079de30b6   redis:5.0.9-alpine3.11   "docker-entrypoint.s鈥   10 minutes ago   Up 10 minutes   0.0.0.0:6374->6379/tcp, 0.0.0.0:16374->16379/tcp   redis-4
  8c6d7a2bedf9   redis:5.0.9-alpine3.11   "docker-entrypoint.s鈥   11 minutes ago   Up 11 minutes   0.0.0.0:6373->6379/tcp, 0.0.0.0:16373->16379/tcp   redis-3
  29c64aeb03b0   redis:5.0.9-alpine3.11   "docker-entrypoint.s鈥   12 minutes ago   Up 12 minutes   0.0.0.0:6372->6379/tcp, 0.0.0.0:16372->16379/tcp   redis-2
  1a1447dd356f   redis:5.0.9-alpine3.11   "docker-entrypoint.s鈥   14 minutes ago   Up 14 minutes   0.0.0.0:6371->6379/tcp, 0.0.0.0:16371->16379/tcp   redis-1
  [root@localhost ~]# docker stop redis-3
  redis-3
  [root@localhost ~]# 
  
  ##测试高可用
  172.38.0.13:6379> 
  /data # redis-cli -c
  127.0.0.1:6379> 
  127.0.0.1:6379> 
  127.0.0.1:6379> 
  127.0.0.1:6379> 
  127.0.0.1:6379> get a
  -> Redirected to slot [15495] located at 172.38.0.14:6379          ##3的从机
  "b"
  
  127.0.0.1:6379> cluster nodes
  962349fa657e7f77300c9b1cdd88ce124214c3c0 172.38.0.14:6379@16379 master - 0 1608273195503 7 connected 10923-16383            ##4  master
  94763f5272bb7bf6424ea150202106b16ae102d1 172.38.0.15:6379@16379 slave afec0ff7c0e7ca28c46739bd88e52ce7495e60ca 0 1608273196841 5 connected
  ff3a7db3a90d7eb711dca8411130e4bddf89c522 172.38.0.12:6379@16379 master - 0 1608273195811 2 connected 5461-10922
  afec0ff7c0e7ca28c46739bd88e52ce7495e60ca 172.38.0.11:6379@16379 myself,master - 0 1608273194000 1 connected 0-5460
  effe962378f7e705d6308a042c8ff507277ac6d7 172.38.0.13:6379@16379 master,fail - 1608273013534 1608273013022 3 connected      ##3master  fail  故障转移
  55441edf7315c1b4e8f920c1e1c453688e86cc68 172.38.0.16:6379@16379 slave ff3a7db3a90d7eb711dca8411130e4bddf89c522 0 1608273196000 6 connected
  127.0.0.1:6379> 
  ```

  

##### Spring Boot 微服务打包镜像

+ 构建springBoot微服务项目

+ <img src="docker.assets\image-20201218144638449.png" alt="image-20201218144638449" style="zoom:80%;" />

  <img src="docker.assets\image-20201218144654152.png" alt="image-20201218144654152" style="zoom:80%;" />

+ 打包应用

  ![image-20201218144835661](docker.assets\image-20201218144835661.png)

  ![image-20201218144952660](docker.assets\image-20201218144952660.png)

  ![image-20201218145129734](docker.assets\image-20201218145129734.png)

+ 编写dockerfile

  ![image-20201218145248074](docker.assets\image-20201218145248074.png)

  ![image-20201218145851492](docker.assets\image-20201218145851492.png)

+ 构建镜像

  ![image-20201218150144500](docker.assets\image-20201218150144500.png)

  ```shell
  [root@localhost home]# mkdir idea
  [root@localhost home]# cd idea/
  [root@localhost idea]# ls
  demo-0.0.1-SNAPSHOT.jar  Dockerfile
  [root@localhost idea]# docker build -t wxkidea .
  
  
  
  ```

  

+ 发布运行   (以后交付一个镜像即可)

  ```shell
  
  [root@localhost idea]#docker images
  
  [root@localhost idea]#docker run -d -P --name wxk-springboot-web wxkidea
  
  [root@localhost idea]#docker ps
  [root@localhost idea]#curl localhost:32779
  ```

+ 思考：很多镜像如何维护？？？？？？   =>  进阶

## Part2:进阶：企业实战

### docker进阶

#### DockerCompose

#### Swarm集群搭建

####CI/CD



