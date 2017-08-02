## 基础使用

* [Tmux：Linux 从业者必备利器](http://blog.jobbole.com/87562/)
* [推荐gpakosz的Tmux配置](https://github.com/gpakosz/.tmux)



快捷键：
* <prefix> means you have to either hit Ctrl + a or Ctrl + b
* <prefix> c means you have to hit Ctrl + a or Ctrl + b followed by c
* <prefix> C-c means you have to hit Ctrl + a or Ctrl + b followed by Ctrl + c
* <prefix> C-c creates a new session
* <prefix> e opens ~/.tmux.conf.local with the editor defined by the $EDITOR environment variable (defaults to vim when empty)
* <prefix> r reloads the configuration
* <prefix> C-f lets you switch to another session by name

更多：https://github.com/gpakosz/.tmux


按下 <prefix> 后的快捷键如下：

基础

* ? 获取帮助信息

Session——会话管理

* s 列出所有会话
* $ 重命名当前的会话
* d 断开当前的会话
* C-c 新建会话

Window——窗口管理

* c 创建一个新窗口
* , 重命名当前窗口
* w 列出所有窗口
* n 选择下一个窗口，替换为C-h
* p 选择上一个窗口，替换为C-l
* 0~9 选择0~9对应的窗口

Panel——窗格管理

* % 创建一个水平窗格
* " 创建一个竖直窗格
* h 将光标移入左侧的窗格*
* j 将光标移入下方的窗格*
* l 将光标移入右侧的窗格*
* k 将光标移入上方的窗格*
* q 显示窗格的编号
* o 在窗格间切换
* } 与下一个窗格交换位置
* { 与上一个窗格交换位置
* ! 在新窗口中显示当前窗格
* x 关闭当前窗格> 要使用带“*”的快捷键需要提前配置，配置方法可以参考上文的“在窗格间移动光标”一节。——译者注

其他

* t 在当前窗格显示时间
* r reloads the configuration
* e opens ~/.tmux.conf.local with the editor defined by the $EDITOR environment variable (defaults to vim when empty)
* b lists the paste-buffers
* p pastes from the top paste-buffer
* P lets you choose the paste-buffer to paste from
* + maximizes the current pane to a new window
* Enter enters copy-mode
* : 输入命令，如输入setw mode-mouse on可以使用鼠标滚轮

[如何鼠标滚动，或者查看窗口历史内容？](http://www.cnblogs.com/bamanzi/archive/2012/08/17/mouse-wheel-in-tmux-screen.html)
所以要用<prefix> [ 进入copy-mode，然后才能用PgUp/PgDn/光标/Ctrl-S等键在copy-mode中移动。


tmux kill-server 杀掉tmux，所有session和window

## 个人定制

sudo apt get install xsel

### 复制模式copy-mode

* 前缀 [ 进入复制模式
* 按 space 开始复制，移动光标选择复制区域
* 按 Enter 复制并退出copy-mode。
* 将光标移动到指定位置，按 PREIFX ] 粘贴

如果把tmux比作vim的话，那么我们大部分时间都是处于编辑模式，我们复制的时候可不可以像vim一样移动呢？只需要在配置文件(~/.tmux.conf)中加入如下行即可。

```
#copy-mode 将快捷键设置为vi 模式
setw -g mode-keys vi

```


## 扩展
### Tmuxinator （为项目自动创建会话）

* sudo apt-get install ruby rubygems
* gem install tmuxinator  #报错，无法连接ruby服务器

  
* 国内无法连接gem  
http://ruby.taobao.org/ => https://ruby.taobao.org/  #淘宝已弃用
https://gems.ruby-china.org/   #最新的源：https://github.com/ruby-china/rubygems-mirror
```
gem source -a https://gems.ruby-china.org/ 

```
结果报SSL 错误

* 安装rvm-ruby用于解决ruby版本问题
sudo apt-get install ruby-rvm 
rvm list known
rvm install 2.2.1

tmuxinator暂时无法安装，可以使用shell脚本替代，每次执行tmus-start即可：

```bash
jty@jty-vm:~/bin$ cat tmux-start 
#!/bin/sh

cmd=$(which tmux) # tmux path
session=coding    # session name

if [ -z $cmd ]; then
  echo "You need to install tmux."
  exit 1
fi

$cmd has -t $session

if [ $? != 0 ]; then
  # setup octoly session
  $cmd new-session -s $session -d -n neffos
  $cmd send-keys "cd ~/neffos/mt6757_7.1/alps/kernel-4.4" Enter
  $cmd send-keys 'vim drivers/input/touchscreen/mediatek/FT8716/focaltech_core.c ' Enter

  $cmd split-window -h -p 50
  $cmd send-keys "cd ~/neffos/mt6757_7.1/alps" Enter
  $cmd send-keys "bash" Enter
  $cmd send-keys "source build/envsetup.sh" Enter
  $cmd send-keys "lunch 25" Enter
  $cmd split-window -v -p 50
  $cmd send-keys 'ls ' Enter

  # Select vim pane
  $cmd select-pane -t 1

  # create second window
  $cmd new-window -n tmp
  $cmd send-keys 'htop' Enter

  # create third window
  $cmd new-window -n git
  $cmd send-keys 'cd ~/github' Enter

  # Optional step, reselect window 1 (the one with vim)
  $cmd select-window -t neffos

fi

$cmd att -t $session

exit 0

```
### Tmate 协作分享session




