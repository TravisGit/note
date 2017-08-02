

如何在多个vim中复制

## 1. vim与vim
借助文件
1. 先选中，然后:w! /tmp/blablabla 
2. 然后到另一个 vim :r /tmp/blablabla

使用剪贴板
1. 按 v 选中想要复制的文本 
2. "*y 复制到本地 X 剪贴板 
3. 切换到要复制的 vim 
4. "*p 把 X 剪贴版的内容复制到 vim 中 

## tmux

* 到对应panel，鼠标选中，ctrl+shift+c复制
* ctrl+shift+v粘贴


## vmware xshell ssh

输出内容到剪切板：
1. ubuntu xclip：
- echo "Hello, world" | xclip
2. linux xsel：
cat README.TXT | xsel -b

vmware支持虚拟机与主机共享剪切板，在虚拟机设置——选项——客户机隔离中，启动即可。
1. windows中复制，可粘贴到虚拟机
2. 虚拟机中复制，可粘贴到windows
3. 在ubuntu虚拟机中使用：echo hello | xclip，可以粘贴hello到主机或虚拟机中——从此不需要用鼠标选择，直接命令输出到剪切板
4. 但使用xshell ssh到虚拟机中时，在xshell中使用xclip命令，是无法输出到剪切板的，怎么办？

解决过程：
1.  直接在xshell的中执行——echo hello | xclip ，会报错——Error: Can't open display: (null)
     * xclip是与X server或Display系统对应的，没有对应的display设备，则无法执行命令
2.  指定一个DISPLAY，执行——echo hello | DISPLAY=:0 xclip，虚拟机中可以粘贴，但windows中仍然无法粘贴
http://stackoverflow.com/questions/18695934/error-cant-open-display-null-when-using-xclip-to-copy-ssh-public-key
3. xshell开启X11转发：属性—连接—ssh—隧道—转发X11到xmanager，然后安装xmanager软件。重新建立连接后，执行echo hello | xclip，可以成功粘贴到虚拟机以及主机。


