# level 3：容器通信+挂载

### 一、dockerfile文件的编写

1. 进入合适的工作目录并查看现有镜像信息

   ![image-20221209121458532](https://obssh.obs.cn-east-3.myhuaweicloud.com/img_xunguang/202212091214570.png)

2. 根据实际要求编写两个不同容器的dockerfile文件

   ```shell
   # container_a
   mkdir container_A
   vim dockerfile
   
   # container_b
   mkdir container_B
   vim dockerfile
   
   chmod 777 container_A
   chmod 777 container_B
   
   ```
   
   - 容器`container_a`的dockerfile文件
   
     ![image-20221216041448260](https://obssh.obs.cn-east-3.myhuaweicloud.com/img_xunguang/202212160414355.png)
   
     > FROM：以本地现有的nginx:latest镜像打底
     >
     > WORKDIR：把容器内的工作目录设定为work_space
     >
     > RUN：里面会进行软件包的换源、更新检查，
     >
     > ENTRYPOINT：将ping的结果重定向到res.txt文件内并查看结果
     >
     > RUN：同上，检测ping的结果
   
   - 容器`container_b`的dockerfile文件
   
     ![image-20221216042153410](https://obssh.obs.cn-east-3.myhuaweicloud.com/img_xunguang/202212160421455.png)
   
     > 除了ping的目标，其他的均同上container_a
   
   - 换源命令
   
     > ###### 这里未采用替换`/etc/apt/sources.list`里的文本内容的方式进行换源，而是用命令进行自动检测换源，
   
   ```
   sudo sed -i "s@http://.*archive.ubuntu.com@https://mirrors.tuna.tsinghua.edu.cn@g" /etc/apt/sources.list
   sudo sed -i "s@http://.*security.ubuntu.com@https://mirrors.tuna.tsinghua.edu.cn@g" /etc/apt/sources.list
   ```
   
   

### 二、docker-compose.yaml文件编写与运行

#### Write

```text
vim docker-compose.yaml
```

```yaml
version: '3'
services: 
  container_a:
    container_name: container_a
    build: ./confile/container_A
    ports: 
      - "8080"
    links: 
      - container_b 
    volumes: 
      - ./ping_res:/work_space/res.txt
  container_b: 
    container_name: container_b
    build: ./confile/container_B
    ports: 
      - "8080" 
    volumes: 
      - ./ping_res:/work_space/res.txt
```



#### Run

```shell
docker-compose up -d
```

输出结果：

```shell
Creating network "p2p_ping_default" with the default driver


Building container_b
Step 1/6 : FROM nginx:latest
 ---> 88736fe82739
Step 2/6 : WORKDIR /work_space
 ---> Using cache
 ---> 219d4d35b55f
Step 3/6 : COPY . .
 ---> d46375546cb7
Step 4/6 : RUN sed -i "s@http://.*archive.ubuntu.com@https://mirrors.tuna.tsinghua.edu.cn@g" /etc/apt/sources.list &&    sed -i "s@http://.*security.ubuntu.com@https://mirrors.tuna.tsinghua.edu.cn@g" /etc/apt/sources.list &&    apt-get -y update &&    apt-get -y upgrade &&    apt-get -y install inetutils-ping &&    apt-get -y clean
 ---> Running in 2af2760bd066
...
...
...
Setting up inetutils-ping (2:2.0-1+deb11u1) ...
Removing intermediate container 2af2760bd066
 ---> 12493d4150d0
Step 5/6 : ENTRYPOINT ping container_a:8080 -c 10 >> ./res.txt && cat ./res.txt
 ---> Running in 39c1421944d2
Removing intermediate container 39c1421944d2
 ---> a2924440068b
Step 6/6 : CMD ping container_a:8080 -c 10
 ---> Running in b071e8276987
Removing intermediate container b071e8276987
 ---> afc3dfa34672
Successfully built afc3dfa34672
Successfully tagged p2p_ping_container_b:latest
WARNING: Image for service container_b was built because it did not already exist. To rebuild this image you must use `docker-compose build` or `docker-compose up --build`.


Building container_a
Step 1/6 : FROM nginx:latest
 ---> 88736fe82739
Step 2/6 : WORKDIR /work_space
 ---> Using cache
 ---> 219d4d35b55f
Step 3/6 : COPY . .
 ---> 01947f76188d
Step 4/6 : RUN sed -i "s@http://.*archive.ubuntu.com@https://mirrors.tuna.tsinghua.edu.cn@g" /etc/apt/sources.list &&    sed -i "s@http://.*security.ubuntu.com@https://mirrors.tuna.tsinghua.edu.cn@g" /etc/apt/sources.list &&    apt-get -y update &&    apt-get -y upgrade &&    apt-get -y install inetutils-ping &&    apt-get -y clean
 ---> Running in 4ef6e058e58b
...
...
...
Setting up inetutils-ping (2:2.0-1+deb11u1) ...
Removing intermediate container 4ef6e058e58b
 ---> d0c9d7762967
Step 5/6 : ENTRYPOINT ping container_b:8080 -c 10 >> ./res.txt && cat ./res.txt
 ---> Running in a486c5239d3b
Removing intermediate container a486c5239d3b
 ---> 9c496075a55c
Step 6/6 : CMD ping container_b:8080 -c 10
 ---> Running in 4042383d82a2
Removing intermediate container 4042383d82a2
 ---> 7ce570f9072b
Successfully built 7ce570f9072b
Successfully tagged p2p_ping_container_a:latest
WARNING: Image for service container_a was built because it did not already exist. To rebuild this image you must use `docker-compose build` or `docker-compose up --build`.

Creating container_b ... done
Creating container_a ... done
```

上面显示创建成功，于是再up一次来启动

```shell
docker-compose up -d
```

```shell
Starting container_b ... done
Starting container_a ... done
```

### 但是。。。没有结果ping_res文件查看为空

- 尝试查看挂载是否成功

  ```shell
  docker inspect 容器名
  ```

  但……挂载是成功了的，和docker-compose.yaml文件里的是一样的

  ![image-20221216043932728](https://obssh.obs.cn-east-3.myhuaweicloud.com/img_xunguang/202212160439769.png)

### 枯了，弄了好久，找不到问题在哪里，求解答

# ~|^|~

### 三、pull地址

#### `docker pull nginx`  (容器的源镜像)



#### 容器a的镜像

#### `docker pull xunguang/sre-docker-homework:class_7-level_3_a` 



#### 容器b的镜像

#### `docker pull xunguang/sre-docker-homework:class_7-level_2_b`
