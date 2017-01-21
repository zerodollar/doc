mall 开发
---------

# 1. 动静分离
动静分离是将网站静态资源（HTML，JavaScript，CSS，img等文件）与后台应用分开部署，提高用户访问静态代码的速度，降低对后台应用访问。
* 静态资源部署至CDN上
项目中的JavaScript,CSS以及img文件都是存放在CDN服务器上，将HTML文件一起存放到CDN上之后
* 后端API提供数据
后端应用提供API，根据前端的请求进行处理，并将处理结果通过JSON格式返回至前端。
* 前后端域名
	* 相同域名
	用户请求api时可以避免跨域所带来的问题，相对开发更为快速，工作量也相对小一些。
	* 不同域名
	兼容跨域请求的情况,JSONP 或 CORS（需要浏览器支持）
* 优点
api接口服务化;
前后端开发并行；
减轻后端服务器压力，提高静态资源访问速度
* 缺点
开发量变大
前后端交流成本升高
不利于网站SEO
 
* 结论
小项目，有扩展可能，采用： 相同域名， nginx处理静态，go提供API。
初始状态：user --> nginx --> mall-api --> mysql
升级版本：user --> CDN／ haproxy  --> nginx(多个)  --> mall-api(多个) --> redis集群 --> mysql-proxy ---> mysql master/slave(多个)

# 2.使用beego生成api
## 2.1 生成api
考虑用docker容器安装， 参考 [docker笔记](docker.md) 启动mysql
将建表语句文件放在($HOME/docker/mall.sql](mal.sql)中，执行建表SYS_MENU_INF, 创建mall-api
```
docker run -it --link malldb:mysql -v $HOME/docker:/home/ --rm mysql:8.0 sh -c 'exec mysql -h"172.17.0.2" -P3306 -uroot -prootpw mall </home/mall.sql'

cd $GOPATH/src/github.com/zerodollar
bee api mall-api -tables="SYS_MENU_INF" -driver=mysql -conn="root:rootpw@tcp(127.0.0.1:3306)/mall"
```
生成的文件结构是
```
mall-api
├── conf
│   └── app.conf
├── controllers
│   └── SYS_MENU_INF.go    对应表Post／GetOne／GetAll／Put／Delete
├── main.go
├── models
│   └── SYS_MENU_INF.go     表对应的结构
├── routers
│   └── router.go           路由 /v1/SYS_MENU_INF
└── tests
```
## 2.2 测试访问
插入预置数据到SYS_MENU_INF表中，以bee方式启动，有个好处: `文件有变动会自动更新`
注意检查conf/app.conf中EnableDocs = true
```
cd $GOPATH/src/github.com/zerodollar/mall-api
bee run watchall -gendoc=true -downdoc=true
```
打开浏览器访问http://localhost:8080/v1/SYS_MENU_INF/ , 会以json返回所有记录

## 2.3测试API文档
访问API文档 http://localhost:8080/swagger/,
如果要支持中文，修改mall-api/swagger/index.html,打开对应的lang注释, 重新访问就是中文了
```
<script src='lang/translator.js' type='text/javascript'></script>
<script src='lang/zh-cn.js' type='text/javascript'></script>
```

## 2.4 生成docker镜像
构建linux运行包生成镜像，但docker hub只支持一个仓库,无法再push
```
export GOOS=linux
go build 
cp mall-api ~/docker
docker build -f Dockerfile.mall-api -t z29759/mall-api .
cat ~/docker/Dockerfile.mall-api
FROM scratch
ADD ./mall-api  /home/mall-api
ADD ./conf/app.conf /home/conf/app.conf
# -db MYSQL_URL
ENTRYPOINT ["/home/mall-api" ]
```


## 2.5 启动镜像
启动镜像，访问
```
export MYSQLIP=`docker inspect malldb|jq ".[0].NetworkSettings.IPAddress"|sed 's/"//g'`
docker run -p 8080:8080   --name mallapi --link malldb:mysql -d z29759/mall-api  -db "root:rootpw@tcp($MYSQLIP:3306)/mall"
```
## 3.静态部分
### 3.1 后台
### 3.2 前台
前台用 Angular2.0＋TypeScript 做js框架，使用AdminLTE做css框架

### 3.3 部署


## 监控
Supervisord 是用 Python 实现的一款非常实用的进程管理工具，supervisord 还要求管理的程序是非 daemon程序，supervisord 会帮你把它转成 daemon 程序
## MySQL 读写分离
[MySQL读写分离](http://www.jianshu.com/p/000dfd9bc3cf)
* 应用层实现：Sprint JDBC／mhBatis／Hibernate访问数据库时配置多数据源，组件会将请求分流到不同的数据源
一个MasterDataSource，更新数据时用
一个SlaveDataSource，查询数据时用，如果有多个从，集成AbstractRoutingDataSource自己实现算法
不需要底层复杂的配置／性能较好，对应用侵入比较强／不利于扩展
* 代理实现：应用层和数据库集群间添加一个代理服务。

[MySql读写分离，及数据库高可用负载均衡的实现](http://blog.csdn.net/xiaoyao8903/article/details/53244724)
proxy代理client和mysql服务，根据sql操作路由到master／或slave
实现读写分离的工具有很多，我介绍几个，阿里巴巴的cobar，360的atlas，以及用golang写的kingshard
kingshard  只支持一个master多个slave，不能同时存在两个master
1.读写分离。
2.跨节点分表。
3.客户端IP访问控制。
4.平滑上线DB或下线DB，前端应用无感知。

两台服务器，互为主从,应该使用mysql-router来实现
* client指定连接？


# 持续集成
### 本地构建
* 本地测试通过
* 提交 github
* 本地构建 docker image
* 上传docker image到仓库
  `docker hub太慢, 考虑： 用国内仓库;  用aws构建
* AWS上docker pull该镜像
* AWS上运行
### AWS构建
* 本地测试通过
* 提交 github
* AWS安装的脚本监控github，构建 docker image
* AWS上传docker image到仓库
* AWS上运行








