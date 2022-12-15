# level 2：dockerfile构建镜像

### 一、编写dockerfile文件

- 准备工作

  - 找一个文件夹放置前置项目文件：`cd docker_depot`

  - 查看等下dockerfile文件中FROM部分会用到的镜像：

    ![image-20221204003028176](https://obssh.obs.cn-east-3.myhuaweicloud.com/img_xunguang/202212040030250.png)

  - 编写前置项目文本`a.txt`，其内容如下：

    ```text
    Hello SRE的各位：
    我是练习时长一年半的代码练习生SRE修🐶；
    我的爱好是Ctrl+CV、RunCode、DeBug；
    我最喜欢的歌是《机尼态酶》；
    崇拜的偶像爱是：SRE·卷
    最喜欢的口头禅是：鉴定完毕，一眼丁真，你这背景太假了，瓜皮子是黄金切尔西做的？还是瓜粒子是黄金切尔西做的？但我知道，英雄可以受委曲，但你不能踩我的切尔西……
    ```

- 编写dockerfile文件内容

  - 首先`vim dockerfile`创建dockerfile文件

  - 然后写入内容，保存：

    ```shell
    # 基于上面查看的结果中tag为latest的本地nginx镜像
    FROM nginx:latest
    # 指定当前工作目录
    WORKDIR /work_space
    # 把本地当前的目录的所有复制到上一行指定的工作目录
    COPY . .
    # 把a.txt里自定义的内容重定向到指定的index.html文件里
    RUN cat a.txt > /usr/share/nginx/html/index.html
    ```

  - 以该目录为上下文，构建新镜像

    ```text
    docker build -t mynginx . 
    ```

    过程如下：（两个Successfully代表构建成功）

    ![image-20221204011730211](https://obssh.obs.cn-east-3.myhuaweicloud.com/img_xunguang/202212040117273.png)

  - 检查一下dockerfile的构建结果

    ```text
    docker images | grep my
    ```

    ![image-20221204012402565](https://obssh.obs.cn-east-3.myhuaweicloud.com/img_xunguang/202212040124595.png)

  - 浅浅run一下下

    ```text
    docker run -d --name show_mynginx -p 8080:80 mynginx:latest
    ```

    ![image-20221204012815694](https://obssh.obs.cn-east-3.myhuaweicloud.com/img_xunguang/202212040128730.png)

    成功捏！

### 二、上传到dockerhub仓库

1. 首先，创建dockerhub账号，这里不多赘述，然后是创建一个公共访问仓库

   > 账户名：xunguang
   >
   > 仓库名：sre-docker-homework
   >
   > 描述：it would be used to commit homework images for cqupt SRE people
   >
   > Visibility：Public

   ![image-20221204025146714](https://obssh.obs.cn-east-3.myhuaweicloud.com/img_xunguang/202212040251780.png)

2. 然后，在Linux终端中登录dockerhub账号

   ![image-20221204025414966](https://obssh.obs.cn-east-3.myhuaweicloud.com/img_xunguang/202212040254002.png)

3. 其次，将上面dockerfile构建出的镜像通过tag命令指定一下push的位置：

   > 格式为：
   >
   > ```text
   > docker tag <原镜像名或ID> <user-name>/<repo-name>:[tag标识，默认为latest]
   > ```

   ![image-20221204025729111](https://obssh.obs.cn-east-3.myhuaweicloud.com/img_xunguang/202212040257145.png)

4. 最后，将生成的镜像push到dockerhub仓库里：

   > 这里push后面要写  全名:标识tag  ，不能偷懒写IMAGE ID，因为其中的信息指定了上传地址

   ![image-20221204030308168](https://obssh.obs.cn-east-3.myhuaweicloud.com/img_xunguang/202212040303203.png)

   可以看到，最后显示了校验码`sha256`，这就表示上传成功，等待一会到dockerhub page网页的对应仓库位置查看，可以看到刚才push的镜像：

   ![image-20221204030828825](https://obssh.obs.cn-east-3.myhuaweicloud.com/img_xunguang/202212040308876.png)

   可以得到该镜像的pull地址：
   
   ##### `docker pull xunguang/sre-docker-homework:class_7-level_2`
