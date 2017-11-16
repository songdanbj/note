Screen是一款由GNU计划开发的用于命令行终端切换的自由软件。用户可以通过该软件同时连接多个本地或远程的命令行会话，并在其间自由切换。GNU Screen可以看作是窗口管理器的命令行界面版本。它提供了统一的管理多个会话的界面和相应的功能。

#### 会话恢复
只要Screen本身没有终止，在其内部运行的会话都可以恢复。这一点对于远程登录的用户特别有用——即使网络连接中断，用户也不会失去对已经打开的命令行会话的控制。只要再次登录到主机上执行screen -r就可以恢复会话的运行。同样在暂时离开的时候，也可以执行分离命令detach，在保证里面的程序正常运行的情况下让Screen挂起（切换到后台）。这一点和图形界面下的VNC很相似。

#### 多窗口
在Screen环境下，所有的会话都独立的运行，并拥有各自的编号、输入、输出和窗口缓存。用户可以通过快捷键在不同的窗口下切换，并可以自由的重定向各个窗口的输入和输出。Screen实现了基本的文本操作，如复制粘贴等；还提供了类似滚动条的功能，可以查看窗口状况的历史记录。窗口还可以被分区和命名，还可以监视后台窗口的活动。 会话共享 Screen可以让一个或多个用户从不同终端多次登录一个会话，并共享会话的所有特性（比如可以看到完全相同的输出）。它同时提供了窗口访问权限的机制，可以对窗口进行密码保护。

#### 安装screen
```shell
yum -y install screen
```

#### 语法
```shell
screen [-AmRvx -ls -wipe][-d <作业名称>][-h <行数>][-r <作业名称>][-s ][-S <作业名称>]
```

#### 选项
```
-A             将所有的视窗都调整为目前终端机的大小。
-d             <作业名称> 　将指定的screen作业离线。
-h             <行数> 　指定视窗的缓冲区行数。
-m             即使目前已在作业中的screen作业，仍强制建立新的screen作业。
-r             <作业名称> 　恢复离线的screen作业。
-R             先试图恢复离线的作业。若找不到离线的作业，即建立新的screen作业。
-s             指定建立新视窗时，所要执行的shell。
-S             <作业名称> 　指定screen作业的名称。
-v             显示版本信息。
-x             恢复之前离线的screen作业。
-ls或--list    显示目前所有的screen作业。
-wipe          检查目前所有的screen作业，并删除已经无法使用的screen作业。
```

#### 常用screen参数
```
screen -S yourname -> 新建一个叫yourname的session
screen -ls -> 列出当前所有的session
screen -r yourname -> 回到yourname这个session
screen -d yourname -> 远程detach某个session
screen -d -r yourname -> 结束当前session并回到yourname这个session
```

在每个screen session 下，所有命令都以 ctrl+a(C-a) 开始。
```
C-a ? -> 显示所有键绑定信息
C-a c -> 创建一个新的运行shell的窗口并切换到该窗口
C-a n -> Next，切换到下一个 window
C-a p -> Previous，切换到前一个 window
C-a 0..9 -> 切换到第 0..9 个 window
Ctrl+a [Space] -> 由视窗0循序切换到视窗9
C-a C-a -> 在两个最近使用的 window 间切换
C-a x -> 锁住当前的 window，需用用户密码解锁
C-a d -> detach，暂时离开当前session，将目前的 screen session (可能含有多个 windows) 丢到后台执行，并会回到还没进 screen 时的状态，此时在 screen session 里，每个 window 内运行的 process (无论是前台/后台)都在继续执行，即使 logout 也不影响。 
C-a z -> 把当前session放到后台执行，用 shell 的 fg 命令则可回去。
C-a w -> 显示所有窗口列表
C-a t -> time，显示当前时间，和系统的 load
C-a k -> kill window，强行关闭当前的 window
C-a [ -> 进入 copy mode，在 copy mode 下可以回滚、搜索、复制就像用使用 vi 一样 
    C-b Backward，PageUp
    C-f Forward，PageDown
    H(大写) High，将光标移至左上角
    L Low，将光标移至左下角
    0 移到行首
    $ 行末
    w forward one word，以字为单位往前移
    b backward one word，以字为单位往后移
    Space 第一次按为标记区起点，第二次按为终点
    Esc 结束 copy mode
C-a ] -> paste，把刚刚在 copy mode 选定的内容贴上
```

#### 创建一个新的窗口 
安装完成后，直接敲命令screen就可以启动它。但是这样启动的screen会话没有名字，实践上推荐为每个screen会话取一个名字，方便分辨： 
```shell
screen -S david 
```
screen启动后，会创建第一个窗口，也就是窗口No. 0，并在其中打开一个系统默认的shell，一般都会是bash。所以你敲入命令screen之后，会立刻又返回到命令提示符，仿佛什么也没有发生似的，其实你已经进入Screen的世界了。当然，也可以在screen命令之后加入你喜欢的参数，使之直接打开你指定的程序，例如：
```bash
screen vim david.txt
```
screen创建一个执行vi david.txt的单窗口会话，退出vi 将退出该窗口/会话。

#### 会话分离与恢复
你可以不中断screen窗口中程序的运行而暂时断开（detach）screen会话，并在随后时间重新连接（attach）该会话，重新控制各窗口中运行的程序。例如，我们打开一个screen窗口编辑/tmp/david.txt文件：
```bash
screen vi /tmp/david.txt
```
之后我们想暂时退出做点别的事情，比如出去散散步，那么在screen窗口键入C-a d，Screen会给出detached提示：  
暂时中断会话  
半个小时之后回来了，找到该screen会话：
```bash
screen -ls
```
重新连接会话
```bash
screen -r 14062
```

### 杀死一个会话
方法1: Ctrl + a -> k
方法2: Ctrl + a -> : -> 输入quit

### 会话共享
```bash
screen -x <会话ID>
```

### 会话锁定和解锁
锁定会话: Ctrl + a -> s  
解锁会话: Ctrl + a -> q

### 在绘画外向会话内发命令
```bash
screen -S text -X screen ping www.baidu.com
```