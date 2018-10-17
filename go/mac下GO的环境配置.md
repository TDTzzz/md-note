# mac下GO的环境配置

### 1.在go的官网安装macos的软件包，傻瓜式安装。

### 2.安装完后，看了下网上教程，先运行go version。。看有没得东西出来，，但是我没有，zsh:command go没用。查了下，是因为，用了zsh，导致shell读取会优先从~/.zshrc里面读取。要在~/.zshrc里加上

### export PATH=$PATH:/usr/local/go/bin 就好了。

### 3.创建一个GO的工作空间，一般都创在/Users/name/下，比如我创的是gowork。然后在~/.bash_profile里export GOPATH=$HOME/go。并source ~/.bash_profile。可以在shell里echo \$GOPATH看是否正确。

### 4.设置bin可执行文件目录

> 额，这个好像可以不用设置，我设置了**GOPATH**后，没主动设置，但执行文件目录会自动在GOPATH目录下生成一个bin目录



[不错的教程](https://www.jianshu.com/p/40e08c85f9e3)