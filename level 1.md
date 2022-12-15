# level 1：打包一个定制nginx镜像

### 一、拉取niginx原生镜像

- 拉取命令

  ```
  docker pull nginx
  ```

  

- 检查是否拉取成功

  ```text
  docker images | grep nginx
  ```

  成功后如图：![image-20221203191859353](https://obssh.obs.cn-east-3.myhuaweicloud.com/img_xunguang/202212031918396.png)

### 二、进入容器中

- 设置容器名称为`test_nginx`，把容器内部`80`端口映射到本机的`8080`端口并run运行一下

  ```text
  docker run -d --name test_nginx -p 8080:80 nginx
  ```

- 然后访问一下，查看运行成功没有

  ```text
  curl localhost:8080
  ```

  出现下图，表示访问成功：

  ![image-20221203194214758](https://obssh.obs.cn-east-3.myhuaweicloud.com/img_xunguang/202212031942804.png)

- 进入容器，使用bash进行交互

  ```
  docker exec -it test_nginx bash
  ```

  效果如图：（可以看到，容器ID是一致的）

  ![image-20221203194528273](https://obssh.obs.cn-east-3.myhuaweicloud.com/img_xunguang/202212031945305.png)

### 三、修改并导出新镜像

- 修改`/usr/share/nginx/html/index.html`文件内容

  ```shell
  # 把“SREyyds”信息重定向到目标文件覆盖原有信息
  echo "SREyyds" > /usr/share/nginx/html/index.html
  ```

- 退出后产生尝试是否修改成功

  ```
  exit
  curl localhost:8080
  ```

效果图：（修改成功）

![image-20221203195708870](https://obssh.obs.cn-east-3.myhuaweicloud.com/img_xunguang/202212031957901.png)

- 把修改后的容器打包成新镜像（新镜像名`new_nginx`）并指定版本号v1

  ```text
  docker commit test_nginx new_nginx:v1             
  ```

  查看打包后的新镜像

  ```
  docker images | grep new_
  ```

  ![image-20221203201826307](https://obssh.obs.cn-east-3.myhuaweicloud.com/img_xunguang/202212032018338.png)

### 四、重新run一下新镜像

- 设置容器名称为`test_nginx_v1`，把容器内部`80`端口映射到本机的`11451`端口并run运行一下

  ```text
  docker run -d --name test_nginx_v1 -p 11451:80 new_nginx:v1
  ```

  

- 访问一下，查看运行结果

  ```text
  curl localhost:11451
  ```

  

令人满意的运行截图：

![image-20221203203559182](https://obssh.obs.cn-east-3.myhuaweicloud.com/img_xunguang/202212032035215.png)