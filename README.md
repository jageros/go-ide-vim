# go-ide-vim
#### gopro.tar.gz
GOPATH下的一些插件包
#### vim.tar.gz
.vim/文件夹和.vimrc配置文件

PS：打开vim即自动部署

vim是linux系统下常用的代码编辑器，默认情况下不支持go的代码高亮和语法检查，不过可以通过安装vim插件来支持go的开发，golang官方也向我们推荐了一些vim的go插件如下所列：（具体哪些插件请查看https://github.com/golang/go/wiki/IDEsAndTextEditorPlugins）







接下来介绍插件的安装，主要是vim-go (https://github.com/fatih/vim-go) 和gocode (https://github.com/nsf/gocode) 这两款插件



1、升级vim到较新的版本



直接键入vim命令，后面不带任何文件名，会显示当前vim的版本信息和一些帮助提示



卸载老版本的vim：


[root@localhost ~]# yum remove -y vim-enhanced

下载并安装新版本vim，这里安装vim 7这个大版本下（最新大版本为vim 8）最新的 7.4.2367



[root@localhost src]# wget https://github.com/vim/vim/archive/v7.4.2367.tar.gz

[root@localhost src]# tar zxvf v7.4.2367.tar.gz

[root@localhost src]# cd vim-7.4.2367/src/

[root@localhost src]# make
.....
[root@localhost src]# make install
.....

这样vim就安装好了，可以再次查看下vim的版本确认安装是否成功。


2、安装vim的插件管理工具

vim插件管理工具可以让插件安装变得简单快捷，vim的插件管理工具也有很多，这里选用 vim-pathogen （https://github.com/tpope/vim-pathogen），安装很简单

先创建两个目录：


[root@localhost ~]# mkdir -p ~/.vim/autoload ~/.vim/bundle

下载 vim-pathogen 到 ~/.vim/autoload 目录下：

curl -LSso ~/.vim/autoload/pathogen.vim https://tpo.pe/pathogen.vim

编辑  ~/.vimrc  ，加入如下几行内容：
execute pathogen#infect()
syntax on
filetype plugin indent on

重启vim 即可安装成功，之后如果想要安装其他插件，只需把插件放到 ~/.vim/bundle 目录下， 重启vim ，vim-pathogen就会帮你自动安装好插件。例如安装markdown：

[root@localhost ~]# cd ~/.vim/bundle/
[root@localhost bundle]# git clone https://github.com/plasticboy/vim-markdown.git
然后重启vim即可


3、安装vim-go

vim-go 是一款go代码高亮和语法检查的插件

如上所示安装方法，clone vim-go 到 ~/.vim/bundle 目录下即可


[root@localhost bundle]# cd ~/.vim/bundle/
[root@localhost bundle]# git clone https://github.com/fatih/vim-go.git
[root@localhost bundle]# ls
vim-go  vim-markdown  Vundle.vim

进入vim，执行vim-go提供的 :GoInstallBinaries 命令可以一键安装其他有助于开发的go工具/命令，但我在运行该命令时，vim-go意外出错了：



原因可能是国内的网络环境导致网络不通，其实这些工具和命令不安装并不会影响vim-go的基本使用，如果确实想要安装，请先解决科学上网，然后再次尝试。



4、测试golang的代码高亮和语法检查







当你保存代码时，如果检测到有语法错误，会在底部给出提示。


5、解决退格键bug和设置一个tab=4个空格

新版vim可能会出现退格键（backspace）无效的问题，解决办法在 ~/.vimrc 加入以下两行：


set nocompatible
set backspace=indent,eol,start


如果要设置按一个tab键等于四个空格，可以在 ~/.vimrc 加入以下两行：


set ts=4
set expandtab
6、安装gocode插件

gocode是一款go的代码提示插件

安装gocode：

首先要正确设置GOROOT、GOPATH、PATH等几个环境变量，在GOPATH目录下新建 src、pkg、bin 三个子目录，并且把 $GOPATH/bin 子目录加入到PATH环境变量下，可以在 /etc/profile 文件下加入类似以下几行：


export GOROOT=/root/software/go-go1.9.2
export GOPATH=/root/mygo
export PATH=$PATH:/root/software/go-go1.9.2/bin:$GOPATH/bin
export GOBIN=$GOPATH/bin

然后 source /etc/profile 使配置立即生效：

[root@localhost ~]# source /etc/profile
[root@localhost ~]# echo $PATH
/usr/local/luajit/bin:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/software/go-go1.9.2/bin:/bin:/root/bin:/root/software/go-go1.9.2/bin:/root/mygo/bin:/root/software/go-go1.9.2/bin:/root/mygo/bin

进入 $GOPAH/src 目录下，clone gocode代码

[root@localhost ~]# cd $GOPATH/src
[root@localhost src]# go get github.com/nsf/gocode
[root@localhost src]# ls
app  github.com  mymath  utils
[root@localhost src]# ls github.com/nsf/gocode/
autocompletecontext.go  debian        emacs-company  LICENSE         package.go       rpc.go     type_alias_build_hack_18.go
autocompletefile.go     declcache.go  formatters.go  nvim            package_text.go  scope.go   type_alias_build_hack_19.go
client.go               decl.go       _gccgo         os_posix.go     pre_go17.go      server.go  utils.go
config.go               docs          gocode.go      os_windows.go   README.md        subl3      vim
cursorcontext.go        emacs         _goremote      package_bin.go  ripper.go        _testing

进入 github.com/nsf/gocode 目录 编辑安装gocode：

[root@localhost src]# cd github.com/nsf/gocode/
[root@localhost gocode]# go build
[root@localhost gocode]# go install

这样 就会生成一个gocode的可执行文件，并被放到 $GOPATH/bin 目录下，把该目录加入PATH环境变量就是方便我们调用该可执行文件。

[root@localhost gocode]# ls $GOPATH/bin/gocode
/root/mygo/bin/gocode
[root@localhost gocode]# gocode -h
Usage: gocode [-s] [-f=<format>] [-in=<path>] [-sock=<type>] [-addr=<addr>]
       <command> [<args>]

Flags:
  -addr string
    	address for tcp socket (default "127.0.0.1:37373")
......

再安装 vim-gocode (https://github.com/Blackrush/vim-gocode)


cd ~/.vim/bundle
git clone https://github.com/Blackrush/vim-gocode.git

设置 gocode的快捷键，比如设置为 F6 ，可以在 ~/.vimrc 加入以下几行：


imap <F6> <C-x><C-o>

编写代码时按 F6 键就可以呼出代码提示了：









7、其他插件

这里再演示下安装 tagbar （https://github.com/majutsushi/tagbar），一款用于显示代码结构的插件

安装ctags：


[root@localhost ~]# git clone https://github.com/universal-ctags/ctags.git

[root@localhost ~]# cd ctags/
[root@localhost ctags]# ./autogen.sh

[root@localhost ctags]# ./configure
.....
[root@localhost ctags]# make
.....
[root@localhost ctags]# make install

安装tagbar：

[root@localhost ctags]# cd ~/.vim/bundle/
[root@localhost bundle]# git clone https://github.com/majutsushi/tagbar.git
[root@localhost bundle]# ls
tagbar  vim-go  vim-markdown  Vundle.vim

设置展开/关闭大纲视图快捷键：

在 ~/.vimrc 加入如下一行：

nmap <F8> :TagbarToggle<CR>

测试tagbar是否正常：


F8键可以打开/关闭 右侧边栏代码大纲

虽然vim的go插件目前还不够完美，但大体上满足我们的日常开发了。



That‘s all ^^
---------------------
原文：https://blog.csdn.net/zhang197093/article/details/78559903
