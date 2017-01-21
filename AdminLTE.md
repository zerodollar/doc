AdminLTE
----
### 下载
参考[说明](https://almsaeedstudio.com/themes/AdminLTE/documentation/index.html)，安装版本不可以修改LESS文件，源码版本可以修改，但需要LESS编译器。

有关[LESS](http://lesscss.cn)，是一门 CSS 预处理语言，它扩展了 CSS 语言，增加了变量、Mixin、函数等特性，使 CSS 更易维护和扩展。
`lessc styles.less styles.css` 编译less为css


基于bootstrap3和jQuery1.11以及其它一些插件，建议：
* 要熟悉bootstrap3，所有组件修改适配AdminLTE
* 浏览主题绑定的页面
* 文档
* 使用LESS构建
* github上issue／MR／贡献

### 布局
* Wrapper `.wrapper`. A div that wraps the whole site.
* Main Header `.main-header`. Contains the logo and navbar.
* Sidebar `.sidebar-wrapper`. Contains the user panel and sidebar menu.
* Content `.content-wrapper`. Contains the page header and content.

###### 布局选项  

* Fixed: `.fixed` 固定header and sidebar.
* Collapsed Sidebar: `.sidebar-collapse` 加载时折叠sidebar 
* Boxed Layout: `.layout-boxed` 固定布局到1250px.
* Top Navigation `.layout-top-nav` 去除sidebar，top navbar有链接.
######i 皮肤
dist/css/skins    比如skin-blue-light

### 修改AdminLTE
* 修改app.js的$.AdminLTE.options
* 加载app.js前定义变量AdminLTEOptions
* Main Header 例子增加导航／logo
* Sidebar   左侧的工具栏
* Control Sidebar   右侧的工具栏，可以覆盖／隐藏
`<button class="btn btn-default" data-toggle="control-sidebar">Toggle Right Sidebar</button>`
* Info Box 信息提示／支持进度
* Box
* Direct Chat

## 目录说明
```
dist      
bootstrap
plugins＝》fastclick／sparkline／jvectormap／slimScroll／chartjs／

.
├── Gruntfile.js  前端工作流Grunt配置
├── LICENSE
├── README.md
├── bootstrap    保存bootstrap的css／img/js
├── bower.json   
├── build        编译Less脚本
├── changelog.md
├── composer.json
├── dist         保存AdminLTE的css／img/js
├── documentation
├── index.html   例子
├── index2.html  例子2
├── package.json 包依赖
├── pages       预制页面：login.html/404.html
├── plugins     依赖的插件jQuery／chartjs ......
└── starter.html
```


