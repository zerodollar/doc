While there is a shell, there is a way.
----
## 1.配置使用zsh

```shell
brew install zsh  zsh-completions

sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
chsh -s /bin/zsh
brew install git osx autojump 
autoload -U zmv
```
编辑自定义的zsh配置  .oh-my-zsh/custom/zjy.zsh
```shell
plugins=(git osx autojump)

#设置特定后缀名文件打开方式
alias -s html="open -b com.apple.safari "

alias h="history"
export HISTSIZE=200  #历史纪录条目数量
export SAVEHIST=200  #注销后保存的历史纪录条目数量

#设置go相关的环境变量
export GOPATH=$HOME/workspace/gosrc
export PATH=.:$PATH:/usr/local/go/bin:$HOME/tool:$GOPATH/bin
```

## 2.配置vim和go
主要用于golang开发，参考[vim-go](https://github.com/fatih/vim-go)安装，参考[vim-go教学](https://github.com/fatih/vim-go-tutorial)利用，使用vim-plug做包管理器
```
brew link lua
brew install vim --with-lua  #如果已安装brew unlink vim卸载
brew install go


#安装包管理器vim-plug／vim-go
curl -fLo ~/.vim/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
git clone https://github.com/fatih/vim-go.git ~/.vim/plugged/vim-go
go get github.com/fatih/vim-go-tutorial
cp $GOPATH/src/github.com/fatih/vim-go-tutorial/vimrc ~/.vimrc
#安装插件
vim -c PlugInstall  

#安装go组件
:GoInstallBinaries   
:GoUpdateBinaries
```
安装[neocomplete](https://github.com/Shougo/neocomplete.vim),自动完成
```
git clone https://github.com/Shougo/neocomplete.vim.git ~/.vim/plugged/neocomplete

#添加配置到 ~/.vimrc
Plug 'Shougo/neocomplete'

let g:neocomplete#enable_at_startup = 1
```

修改go模版增加
```
~/.vim/plugged/vim-go/templates／hello_world.go  
func must(err error) {
       	if err != nil {
       		pacnic(err)
       	}
}
```


安装[tagbar](https://github.com/majutsushi/tagbar.git)，显示函数窗口
默认的ctags，不是tagbar要求的Exuberant ctags 5.5，配置时要指定目录
```
brew install ctags --HEAD

git clone https://github.com/majutsushi/tagbar.git    ~/.vim/plugged/tagbar


#添加配置到 ~/.vimrc
Plug 'majutsushi/tagbar'

"nmap <Leader>tb :TagbarToggle<CR>
let g:tagbar_ctags_bin='/usr/local/bin/ctags' 
let g:tagbar_width=30	
map <F3> :Tagbar<CR>
autocmd BufReadPost *.go,*.cpp,*.c,*.h,*.hpp,*.cc,*.cxx call tagbar#autoopen() 	"如果是c/go语言的程序的话，tagbar自动开启
```
安装[NERDTree](https://github.com/scrooloose/nerdtree),浏览文件系统的树形资源管理
```
git clone https://github.com/scrooloose/nerdtree.git ~/.vim/plugged/nerdtree

#添加配置到 ~/.vimrc
Plug 'scrooloose/nerdtree'

let NERDTreeWinPos='left'
let NERDTreeWinSize=30
map <F2> :NERDTreeToggle<CR>
```
安装beego等模块
```
go get github.com/astaxie/beego
```
### 2.1 常用快捷键
参考 ~/.vimrc中配置

功能键 |快捷键 | 功能
------- | ------- |-------
:GoRun |\<leader\>r|运行|
:GoBuild| \<leader\>b|编译有错误跳转 \<C-n\> :cnext和\<C-m\> :cprevious<br>关闭错误 \<leader\>a  :cclose |
:GoTest |  \<leader\>t|测试|
:GoDoc |  \<leader\>d|生成文档|
:GoCoverage|\<leader\>c |覆盖率
:GoAlternate| |切换文件，:A  :AV :AS  :AT
:GoMetaLinter|\<leader\>l|同时运行go vet／golint／errcheck
:GoDef|\<C-t\> \<C-]\>|跳转到定义处， \<leader\>v 垂直窗口\<leader\>s  水平窗口
||\<C-o\>| 返回上次光标处
:GoDecls||本文件中调用该函数的地方
:GoDeclsDir|\<C-g\>|本目录中调用该函数的地方
:GoCallers||
:GoReferrers||调用该变量的地方
:GoChannelPeers||该channel相关的地方
:GoFiles||本包包含的文件
:GoDeps||依赖的包
:GoRename|| 修改变量名
:GoFreevars||重构函数
:GoImpl || 为一个结构实现接口
]]||跳到后一个函数
[[||跳到前一个函数
||\<C-W-c\>|关闭当前窗口， q／o
||\<C-W-hjkl\>|窗口间切换
:TagbarToggle|F3| 打开类窗口
:NERDTreeToggle|F2|打开文件浏览窗口


### 2.2 代码片段
默认使用[Ultisnips](https://github.com/fatih/vim-go/blob/master/gosnippets/UltiSnips/go.snippets),还支持neosnippet
errp+Tab:  判断有错误则panic
###2.3自动补全
<C-n>  本文中字符串补齐，不涉及语义
neocomplete 已经支持输入时补齐，不需要再按键

## 3.安装npm和nodejs
```
brew install npm      #3.x.x更高版本
brew install nodejs   #v4.x.x更高版本
```
默认npm版本不够，从https://nodejs.org/en/download/ 下载最新版本nodejs，目前内含node 6.9.2,npm 3.10.9

## 4.安装VS code
[官方下载](https://code.visualstudio.com)
安装插件
* Go语法 publisher:"lukehoban"
* jslint
* TypeScript Import
* TSLint

常用的快捷键：
⇧⌥F  格式化
⌘⇧P  命令面板


## 5.安装angular开发环境
参考[搭建本地开发环境](https://angular.cn/docs/ts/latest/guide/setup.html)
```
git clone https://github.com/angular/quickstart.git quickstart
cd quickstart
rm -rf .git   #不需要提交代码
npm install  #包括tsc编译器／lite-server
npm start   #ts编译器和server都是watch模式
```
如果没有异常，会自动打开浏览器访问3000端口

用VS code打开目录quickstart开始开发


## 附录：其它命令简单参考
* curl

```
-X POST url -d '{key}" 
-H "User-Agent: ..."  url
--user "username:password"  url
-L -c cookies url
-L -b cookies url
```
* grep 

````
-n -E "regular expressio"
-C n  匹配＋前后n行
-B n  匹配＋前n行
-A n  匹配＋后n行
````
* JSON查询利器jq

```
brew install jq
```









