# Docker

## 1.理念

![image-20200912185810914](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200912185811.png)

- 一次构建,随处运行

- 解决运行环境和配置问题软件容器,方便做持续集成并有助于整体发布的容器虚拟化技术

- 和虚拟机的区别

  ![image-20200912195455148](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200912195455.png)

## 2.docker组成

- 架构图:
  
- ![image-20200912200443779](C:%5CUsers%5Chuanl%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20200912200443779.png)
  
- 基本组成:

  - 镜像:

    ```markdown
       类似于Java的类,可以创建多个对象(也就是docker的容器)
    
       镜像是分层的
    ```

    

  - 容器

    ```markdown
    	Docker利用容器(Container)独立运行的一个或一组应用.容器时用镜像创建的运行实例.
    	容器可以被启动,开始,停止,删除.每个容器都是相互隔离的,保证安全的平台.
    	可以把容器看作以简易版的Linux环境(包括root用户权限,进程空间,用户空间,和网络空间等)和运行在其中的应用程序,容器的定义     和镜像几乎一模一样,也是一堆层的统一视角.唯一区别在于容器的最上面那一层是可读可写的.
    ```

    

  - 仓库

  ![image-20200912205724008](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200912205724.png)

## 3.底层原理

- run执行过程

![image-20200912225141330](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200912225141.png)

- 为什么Docker比VM快?

  ![image-20200912225428414](C:%5CUsers%5Chuanl%5CAppData%5CRoaming%5CTypora%5Ctypora-user-images%5Cimage-20200912225428414.png)

## 4.Docker常用命令

- 文档

  ```markdown
  Usage:	docker [OPTIONS] COMMAND
  
  A self-sufficient runtime for containers
  
  Options:
        --config string      Location of client config files (default "/root/.docker")
    -c, --context string     Name of the context to use to connect to the daemon
                             (overrides DOCKER_HOST env var and default context set with
                             "docker context use")
    -D, --debug              Enable debug mode
    -H, --host list          Daemon socket(s) to connect to
    -l, --log-level string   Set the logging level
                             ("debug"|"info"|"warn"|"error"|"fatal") (default "info")
        --tls                Use TLS; implied by --tlsverify
        --tlscacert string   Trust certs signed only by this CA (default
                             "/root/.docker/ca.pem")
        --tlscert string     Path to TLS certificate file (default "/root/.docker/cert.pem")
        --tlskey string      Path to TLS key file (default "/root/.docker/key.pem")
        --tlsverify          Use TLS and verify the remote
    -v, --version            Print version information and quit
  
  Management Commands:
    builder     Manage builds
    config      Manage Docker configs
    container   Manage containers
    context     Manage contexts
    engine      Manage the docker engine
    image       Manage images
    network     Manage networks
    node        Manage Swarm nodes
    plugin      Manage plugins
    secret      Manage Docker secrets
    service     Manage services
    stack       Manage Docker stacks
    swarm       Manage Swarm
    system      Manage Docker
    trust       Manage trust on Docker images
    volume      Manage volumes
  
  Commands:
    attach      Attach local standard input, output, and error streams to a running container
    build       Build an image from a Dockerfile
    commit      Create a new image from a container's changes
    cp          Copy files/folders between a container and the local filesystem
    create      Create a new container
    diff        Inspect changes to files or directories on a container's filesystem
    events      Get real time events from the server
    exec        Run a command in a running container
    export      Export a container's filesystem as a tar archive
    history     Show the history of an image
    images      List images
    import      Import the contents from a tarball to create a filesystem image
    info        Display system-wide information
    inspect     Return low-level information on Docker objects
    kill        Kill one or more running containers
    load        Load an image from a tar archive or STDIN
    login       Log in to a Docker registry
    logout      Log out from a Docker registry
    logs        Fetch the logs of a container
    pause       Pause all processes within one or more containers
    port        List port mappings or a specific mapping for the container
    ps          List containers
    pull        Pull an image or a repository from a registry
    push        Push an image or a repository to a registry
    rename      Rename a container
    restart     Restart one or more containers
    rm          Remove one or more containers
    rmi         Remove one or more images
    run         Run a command in a new container
    save        Save one or more images to a tar archive (streamed to STDOUT by default)
    search      Search the Docker Hub for images
    start       Start one or more stopped containers
    stats       Display a live stream of container(s) resource usage statistics
    stop        Stop one or more running containers
    tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
    top         Display the running processes of a container
    unpause     Unpause all processes within one or more containers
    update      Update configuration of one or more containers
    version     Show the Docker version information
    wait        Block until one or more containers stop, then print their exit codes
  
  
  ```

  

- 帮助命令:

  ![image-20200912225816054](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200912225816.png)

- 镜像命令:

  ![image-20200912225842275](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200912225842.png)

  ```markdown
      docker images 列出本机镜像文件
      常用参数说明:
          -a:列出本地所有的镜像(含中间映像层)
          -q:只显示镜像ID
          --digests: 显示镜像的摘要信息
          --no-trunc:显示完整的镜像信息
  ```

  ![image-20200912230621978](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200912230622.png)

  - docker search`

  ```markdown
      docker search 镜像名字
  
      去hub.docker上面查有哪些镜像
  
      参数说明: 
          --no-trunc:显示完整的镜像描述
          -s:列出收藏数不小于指定值的镜像
          --automated:只列出automated build类型的镜像
  ```

  ![image-20200912231246913](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200912231246.png)

  - docker pull 镜像名

  ```markdown
      docker [options] pull 镜像名 [:tags]
  
      如果不加后面的tag,那么默认是latest最新版本. 
  ```

  - docker rmi 某个xxx镜像名字ID [tag]

  ```markdown
      删除镜像
          不加后面的tag默认删除latest
      删除单个 docker rmi 镜像名
      删除多个 docker rmi -f(强制) 镜像名1:tag 镜像名2:tag
      删除全部 docker rmi -f $(docker images -qa)
  ```

- 容器命令

  - docker run [Options] image [command]

    ```markdown
    创建并启动容器
    常用选项:
    	
    ```

    

    ![image-20200913131038245](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200913131045.png)

  - docker ps [OPTIONS] 列出当前所有正在运行的容器

    ![image-20200913132839771](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200913132839.png)

- 退出容器

  ```markdown
  exit  容器停止后退出,也就是下次使用得重启
  ctrl+P+Q 容器不停止退出,下次可以继续使用,只是暂时退出
  ```

- 开启容器

  ```markdown
  docker start 容器名/id
  ```

- 重启容器

  ```markdown
  docker restart 容器名/id
  ```

- 停止容器

  ```markdown
  docker stop 容器名/id  正常关机
  ```

- 强制停止

  ```markdown
  docker kill 容器名/id  类似于直接拔电源
  ```

- 删除已停止的容器

  ```markdown
  docker rm  容器ID
  	强制删除 docker rm -f 容器ID
  	一次性删除多个容器 docker rm -f $(docker ps -a -q)
  					docker ps -a -q | xargs docker rm重要
  ```

#### 重要细节:

- 启动守护式容器 docker run -d  镜像名

  ```markdown
  docker run -it centos  交互式启动
  docker run -d centos 	守护式启动
  ```

  ![image-20200913194303547](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200913194303.png)

- 查看容器日志

  ```markdown
  docker logs -f -t --t下`ail 容器ID
  	-t 列出日志时间
  	-f 实时打印日志
  	--tail num 显示最后num行
  ```

- 查看容器内运行的进程; docker top 容器ID

  ```markdown
  因为docker容器可以看成一个简易版的linux
  ```

- 查看容器内部细节 docker inspect 容器ID

  ```markdown
  
  ```

- 进入正在运行的容器并以命令行交互: 

  ```markdown
  docker exec  -it 容器ID bashShell 
  重新进入 docker attach 容器ID
  
  区别: attach 直接进入容器启动命令的终端
  	  exec 在容器中打开新的终端,并且可以启动新的进程.
  	  
  	exec 可以在桌面执行某容器的内部命令并且将结果返回至桌面.可以在外层直接执行内部的命令然后放回结果到外层.
  ```

  ![image-20200920162859832](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200920163104.png)

- 从容器类拷贝文件到主机上 docker cp 容器ID:/路径 目的路径

- 常用命令:

  ![image-20200913225541571](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200913225541.png)

![image-20200913225614050](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200913225614.png)

![image-20200913225637235](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200913225637.png)

![image-20200913225816758](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200913225816.png)

![image-20200913225835412](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200913225835.png)

![image-20200913225911982](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200913225912.png)

## 5.Docker镜像

![image-20200913230338412](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200913230338.png)

- 概念:

  ```markdown
  镜像是一种轻量级,可执行的独立软件包,用来打包软件运行环境和基于运行环境开发的软件,它包含运行某个软件所需的所有内容,包括代码,运行时,库,环境变量和配置文件.
  ```

- UnionFS联合文件系统

  ![image-20200913230622073](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200913230622.png)

- docker镜像加载原理

  ![image-20200913230751572](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200913230751.png)

![image-20200913231809604](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200913231809.png)

- 镜像分层的概念(以tomcat为什么这么大为路径)

  ```markdown
  因为联合文件系统UnionFS,tomcat需要包含它所有他所需的底层环境,一层一层这样叠加起来,就会变得很大
  ```

  

  ![image-20200913232017843](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200913232017.png)

- 为什么docker要采用多层镜像?

  ```markdown
  最大的好处 -共享资源
  
  ```

  ![image-20200913232312124](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200913232312.png)

- 特点:

  ![image-20200913232403285](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200913232403.png)

- 补充:

  ![image-20200913232633377](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200913232633.png)

![image-20200913232725769](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200913232725.png)

![image-20200913233042478](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200913233042.png)

![image-20200913233138604](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200913233138.png)

![image-20200913233419650](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200913233419.png)

## Docker容器数据卷

### 概念:

![image-20200920133957409](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200920134004.png)

```markdown
其实也就是相当于在容器和系统之间建立一个可以同步共享的文件夹,
```



### 作用:

![image-20200920134044123](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200920134044.png)

### 创建容器数据卷:

#### 命令行添加: 

```markdown
docker run -it -v /宿主机绝对路径目录:/容器内目录 镜像名

限制容器内对这个数据卷也就是文件夹只读不可写:
	docker run -it -v /宿主机绝对路径目录:/容器内目录:ro 镜像名
```

![image-20200920144546046](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200920144546.png)

#### DokcerFile添加:

##### 步骤:

![image-20200920165657519](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200920165657.png)

```markdown
1. 新建一个文件夹,并且在文件夹下创建一个名为Dockerfile的文件
2. 然后将要添加的dockerfile文件的内容写入保存
3. 然后将这个dockerfile文件和原始centos镜像通过docker build命令重新组装一个新镜像.
4. 然后用这个新镜像创建一个容器,容器中自动创建了我们定义好的那两个数据卷.
5. 可以通过用docker inspect 容器id 来查看容器的详细描述,里面有数据卷对应的主机目录.
```



![image-20200920165639189](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200920165639.png)

![image-20200920170735194](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200920170735.png)

![image-20200920165247757](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200920165247.png)

### 数据卷容器:

```markdown
如下图,也就是将dc01的数据卷与dc02共享,也就是在dc01中改变,dc02中也可以查看

!!!注意:
	1. 假如dc01有两个共享,也就是dc02和dc03都继承与dc01,那么将dc01删除后,dc02和dc03还有共享吗?   
		仍然共享,不受影响,只要共享一建立,就和其他任何其他容器没有任何关系
		比如dc3继承dc2,dc2继承dc1,将dc2删除,那么dc1和dc3之间仍然能够共享,不会受到任何影响.
```



![image-20200920191751352](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200920191751.png)

## Dockerfile

### 概念:

```markdown
dockerfile是用来构建docker镜像的构架文件,是由一系列的命令和参数构成的脚本.

构建的三个步骤:
 dockerfile -> docker build  -> run
 
```

`````````

`````````

### 构建过程:

![image-20200921091939285](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200921092342.png)

![image-20200921092119062](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200921092339.png)

![image-20200921092201404](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200921092335.png)

### Docker体系结构(指令)

![image-20200921093150094](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200921093150.png)

![image-20200921093226601](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200921093226.png)

```markdown
copy 后面的参数有两种写法 copy src dest
					copy["src","dest"]
```



![image-20200921093046252](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200921093046.png)

![image-20200921092919705](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200921092919.png)

### 案例

#### 案例1:  vim和ifconfig支持

![image-20200921094520287](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200921094520.png)

![image-20200921094026200](https://forpinco.oss-cn-chengdu.aliyuncs.com/20200921094026.png)
