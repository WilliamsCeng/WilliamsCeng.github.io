[toc]



# Command-line Environment



## Job control 

man signal：可以查看哪些signal可以被传递给进程
info signal

`ctrl - c`：停止命令的运行，本质是通过发送一个SIGINT的信号，signal interrupt，告诉程序停止运行

`ctrl-\`：发送 SIGQUIT信号

`ctrl-z`：发送 SIGSTOP信号


### jobs：查看后台没有退出的进程

jobs：查看后台中没有退出的（未完成的，unfinished）进程

没有退出（未完成的，unfinished）：指suspended 或者 running 中

fg，bg指定一个job在foregroud 前台或 background 后台运行

bg %1

%1，percent symbol followed by ist job number（displayed by jobs），可以用于指定jobs显示的第一个进程，数字n同理，

kill -STOP/... %n ：kill 命令可以传递特定的signal给指定的第n个进程，此处 发送了SIGSTOP信号给 第n个进程

### pid
进程的id，process id，可以使用pgrep查找

### 让一个job或command在background运行
`& suffix in a command`：在command 后面加&，指示它在bg运行
或
对一个运行的program按快捷键，`ctrl-z`，再使用bg命令


## Terminal Multiplexers

终端复用器，可以在一个终端terminal中创建不同的工作空间

tmux 和 screen都是 terminal multiplexer工具



### tmux

tmux中的一些层级概念：

有层级的包含关系，

- Sessions 
  - Windows：类似其他编辑器/浏览器中的tabs概念
    - Panes


当你在命令行中执行 tmux 命令时，shell 启动了 tmux 进程，tmux将 启动另一个 shell，然后tmux可以对其启动的shell进行管理


#### tmux 的keybindings
tmux的快捷键，都有如下形式：`<ctrl-b> x`，所以执行快捷键时，有3步：1. 按下 crtl-b，2. 松开 ctrl-b，3. 按下按键x，执行相关快捷键功能

tmux 的控制快捷键，通常都是 一个前置快捷键唤起 控制功能（tmux默认是 ctrl-B），再按下控制键执行快捷键功能

如：想切换一个窗口，可以先 按 ctrl-B，再按D



#### 一个tmux的应用场景

运行两个不同的project，tmux建立两个session，根据project命名，就可以通过tmux切换不同的session做不同的project，

而每个session下，都可以通过建立多个windows，同时运行多个shell

同时，在一个window中，又想同时看代码和文档，就可以将window分成panes，不同pane展示不同的内容


#### tmux 的快捷键
https://www.hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/
顺便简单配置tmux：http://linuxcommand.org/lc3_adv_termmux.php
https://missing.csail.mit.edu/2020/command-line/

`tmux` expects you to know its keybindings, and they all have the form `<C-b> x` where that means (1) press `Ctrl+b`, (2) release `Ctrl+b`, and then (3) press `x`. `tmux` has the following hierarchy of objects:

`<C-b> ?`Show the list of key bindings (i.e., help)

-   **Sessions** - a session is an independent workspace with one or more windows
    -   `tmux` starts a new session.
    -   `tmux new -s NAME` starts it with that name.
    -   `tmux ls` lists the current sessions
    -   Within `tmux` typing `<C-b> d` detaches the current session
    -   `tmux a` attaches the last session. You can use `-t` flag to specify which
    - `<C-b> $` Rename the current session
    - `tmux kill-session`Kill the current session
-   **Windows** - Equivalent to tabs in editors or browsers, they are visually separate parts of the same session
    -   `<C-b> c` Creates a new window. To close it you can just terminate the shells doing `<C-d>`
    -   `<C-b> N` Go to the _N_ th window. Note they are numbered
    -   `<C-b> p` Goes to the previous window
    -   `<C-b> n` Goes to the next window
    -   `<C-b> ,` Rename the current window
    -   `<C-b> w` List current windows
    - `<C-b> &` Kill the current window
-   **Panes** - Like vim splits, panes let you have multiple shells in the same visual display.
    -   `<C-b> "` Split the current pane horizontally
    -   `<C-b> %` Split the current pane vertically
    -   `<C-b> <direction>` Move to the pane in the specified _direction_. Direction here means arrow keys.
    -   `<C-b> z` Toggle zoom for the current pane
    -   `<C-b> [` Start scrollback. You can then press `<space>` to start a selection and `<enter>` to copy that selection.
    -   `<C-b> <space>` Cycle through pane arrangements.y
    - `<C-b> x` 关闭当前pane，Kill the current pane 
    - `<C-b> <C-arrow keys>` 调整当前pane所占屏幕的大小Resize pane


## Dotfiles
关于各种工具的配置：https://dotfiles.github.io/

配置shell



### alias

alias：（中文是别名。。。）用于将一个字符串映射称为更长的字符串

如： 执行alias ll=“ls -lah”以后，执行ll，就等于执行 ls -lah



### dotfiles

但是执行 alias 之后，退出shell，这些alias就会失效，所以一个方法，就是通过基于文本的配置文件配置shell，让shell每次启动时，自动地加载这些alias

这些配置文件，通常称为dotfiles

如：bash 的配置文件 .bashrc，（此外bash的 .bashrc还有很多配置可以玩。。。）

vim 的 .vimrc



#### 同时要注意配置文件放置的位置，必须位于home folder中，~中（特定工具的配置文件应该有自己特定的放置目录）


#### 同步dotfiles
为了同步工具的配置，所以可以用git对各个配置文件进行版本控制，但是一般不会对~home目录进行git版本控制...

所以通过symlinks配合一个dotfiles文件夹进行

创建symlink，ln -s path/file symlink，symlink像是指针，当程序读取symlink时，symlink告诉程序去哪里读其指向的文件

dotfiles文件夹保存实质的配置文件

在home目录用symlink指向dotfiles中相关的文件

所以只要对dotfiles文件夹进行版本控制即可



## Remote Machines
remote machines or remote servers
用于远程机器的主要命令是 ssh（secure shell）

如：ssh jjgo@192.168.246.142
可以用IP或URL指定remote machine/server，所以，也可以 ssh foo@bar.mit.edu


表示用jjgo的用户名登录IP地址为192...的远程机器

然后ssh可以将远程的shell整个转发到本地，并显示，然后进行交互

### 远程文件夹
把一个remote machine/server的文件夹mout到本地，可以当成本地文件夹一样处理
Sometimes it is convenient to mount a remote folder. [sshfs](https://github.com/libfuse/sshfs) can mount a folder on a remote server locally, and then you can use a local editor.

## 其他的shell
除了bash这种shell外，
还有zsh，fish等shell，有更多便利的功能


## Miscellaneous
`<C-b> t`：tmux 在当前pane上显示一个digital clock


## 课后习题
### Job control
1. 
```bash
ps
sleep 1000
<C-z>
ps aux | grep sleep
jobs 
bg %1
pkill sleep # kill 用pid 指定进程，pkill 用进程名字指定进程
jobs
ps aux | grep sleep
```
2. 
```bash
# pidwait.sh 
pidwait(){
        echo "PID $1"
        wait $1
        echo "Hello world. Process $1 state changes"
}
source pidwait.sh
sleep 10 & # &指示该命令行在background运行
>[1] 10714
pidwait 10714
>PID 10714
>[1]+  Done                    sleep 10
>Hello world. Process 10714 state changes
```

### Terminal multiplexer

### Aliases

### Dotfiles
https://www.anishathalye.com/2014/08/03/managing-your-dotfiles/#fnref-1
https://drewdevault.com/2019/12/30/dotfiles.html
https://dotfiles.github.io/utilities/

dotfiles文件夹+ symlinks + git 版本控制 + bash 脚本自动 生成 symlinks 

### Remote Machines

