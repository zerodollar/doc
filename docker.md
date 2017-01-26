Docker 学习过程
----------
#1.实践

[Docker入门](http://www.docker.org.cn/book/docker/what-is-docker-16.html)，参考[官方入门](https://docs.docker.com/engine/getstarted/step_one/)，[Docker 入门实战](http://yuedu.baidu.com/ebook/d817967416fc700abb68fca1?pn=1&rf=http%3A%2F%2Fdockone.io%2Farticle%2F233)

Mac[下载](https://download.docker.com/mac/stable/Docker.dmg),双击打开，将Docker拖到Applications中完成安装，再启动Docker,右上角看到鲸鱼

```
#安装
docker version    #同时显示client和server的版本号
docker info       #Containers／Images／Storage／Registry等信息
docker run hello-world  ＃运行hello-world，没有image会自动下载，name是随机起的，退出后会留有一个Stopped状态的Containers，此时image是不能删除的
docker run -d -p 80:80 --name webserver nginx  #此时web可以访问
docker images  #已经安装的image
docker ps       #正在运行的进程，可以看到nginx
docker ps -a  #可以看到所有进程，包括已经退出的
docker stop webserver  #停止，webserver前面的name，ps中可以看到
docker start webserver #启动，webserver是前面指定的name
docker rm   #删除container，全部删除后对应的image才可以删除
docker rmi  #删除不用的image

docker search learn/tutorial  #查找image
docker pull learn/tutorial    #获取对应的image，镜像基于ubuntu系统
docker run learn/tutorial echo "hello word"  #在镜像中执行echo命令
docker run learn/tutorial apt-get install -y ping  #安装ping命令
docker commit 698 learn/ping #提交容器修改，698是容器ID，后面是name
docker run lean/ping ping www.google.com #新镜像可以ping
docker inspect  1cc  #检查镜像／容器，1cc是镜像ID或容器ID
docker push learn/ping  #发布镜像


```
# 2. 入门资料 
[学习笔记](http://www.open-open.com/lib/view/open1423703640748.html) 
[《Docker入门实战》](http://yuedu.baidu.com/ebook/d817967416fc700abb68fca1?pn=1&rf=http%3A%2F%2Fdockone.io%2Farticle%2F233)

# 3.加速器
下载mysql，指定8.0，不是latest。latest镜像会不断更新。
```
docker  pull mysql:8.0
```
国内比较慢，点击桌面顶栏的 docker 图标，选择 Preferences ，在 Advanced 标签下的 Registry mirrors 列表中加入加速器地址，比如
```
http://4cc89515.m.daocloud.io
```
点击 Apply & Restart 按钮使设置生效。 再试试速度。
# 4. 制作镜像

```
mkdir ~/docker;cd ~/docker
export GOOS=linux
go build zerodollar.go        # fmt.Println("vim-go at ", os.Getenv("HOME"))
cat Dockerfile
    FROM scratch
    ADD  ./zerodollar  /home/zerodollar
    CMD ["/home/zerodollar"]
docker build -t z29759/zerodollar .
docker images
    REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
    z29759/zerodollar   latest              31f39c2b0a14        8 minutes ago        1.646 MB
docker tag z29759/zerodollar:latest z29759/zerodollar:1
docker images
    REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
    z29759/zerodollar   1                   31f39c2b0a14        8 minutes ago        1.646 MB
    z29759/zerodollar   latest              31f39c2b0a14        8 minutes ago        1.646 MB
docker login -u z29759
docker push z29759/zerodollar:1
docker rmi --force 31
docker run z29759/zerodollar:1
    Status: Downloaded newer image for z29759/zerodollar:1
    vim-go at  /
```
如果报 `unauthorized: authentication required`, 将~/.docker/config.json中的url修改成"https://index.docker.io/v1/"

#  5.启动mysql并访问
还有一个优化版本mysql/mysql-server，开发方便还是用mysql了，参考[官方文档](https://hub.docker.com/_/mysql/)如何启动／连接
```
docker run --name malldb -p 3306:3306 -e MYSQL_ROOT_PASSWORD=rootpw -v $HOME/workspace/mall/malldb_volume:/var/lib/mysql -d mysql:8.0 
docker exec -it malldb bash     #本容器交互式访问
/# mysql -u root -prootpw               
mysql> create database mall;
```
查看最后10行日志
```
docker logs --tail 10 malldb            
```
另外容器访问malldb ,首先查询mysql服务容器的ip地址，赋值给MYSQLIP。 同时-p将3306映射到宿主机的3306，也可用访问。
```
export MYSQLIP=`docker inspect malldb|jq ".[0].NetworkSettings.IPAddress"`
docker run -it --link malldb:mysql --rm mysql:8.0 sh -c 'exec mysql -h"$MYSQLIP" -P3306 -uroot -prootpw'    
```
查看mount点
```
docker inspect malldb|jq ".[0].Mounts"
```
如果后面有mallapi的程序需要访问mysql，可用这样启动
```
docker run --name mallapi --link malldb:mysql -d z29759/mallapi:1   
```
直接进入容器内部
```
nsenter --target `docker inspect --format {{.State.Pid}}  3002e553d996` --mount --uts --ipc --net --pid
```







