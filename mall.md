mall 开发
---------

# 1. 动静分离
[网站开发动静分离实践](http://blog.csdn.net/dadadie/article/details/50768947)
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

# 2. 安装mysql
使用docker 安装
```
docker pull mysql
docker pull registry.mirrors.aliyuncs.com/library/mysql
docker run --name mall-mysql -e MYSQL_ROOT_PASSWORD=mypasswd1 -d mysql:tag
```
# 3.使用beego生成api
```
cd $GOPATH/src/github.com/zerodollar
bee api jeedev-api -conn="root:123456@tcp(127.0.0.1:3306)/jeedev"

```
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








