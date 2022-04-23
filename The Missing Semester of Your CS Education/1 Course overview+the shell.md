[toc]
#Course overview + the shell

大部分命令都会有 -flag 和 -option 选项，用于控制命令的行为
-flag：-加字母的组合，一般就是 flags选项
-option：- 加 字母 + 值 的组合，一般就是命令的option选项

| : pipes 管道符，可用于连接programs，使得一个程序的输出是另一个程序的输入
如： ls -l / | tail -n1 或者 echo “Hello World” | tee Hello.md

sudo : do something as su（super user，root）

\>,<,|等符号，是由shell本身去执行的，所以，有时候sudo不能贯穿整条命令，让shell去写一些没有权限的文件
如：sudo echo 3>brightness，将导致 permission denied，因为shell 用> 去写系统的brightness文件时，并没有权限
所以，通常应该写为 echo 3 | sudo tee brightness，tee，将输入流同时定向到文件和terminal，所以让tee命令以su的身份，就可以写brightness文件了

## 课后习题
1. 
2. mkdir Templates/tmp/missing
3. man touch
4. touch missing/semester
5. vim semester
	#!/bin/sh
	curl --head --silent https://missing.csail.mit.edu
6. ./semester
	bash: ./semester: Permission denied
	ls -l 可以发现文件没有执行权限 `-rw-rw-r--`
7. sh semester
	可以执行，因为有权限执行sh这个程序，semester被当成了参数传给sh程序处理
8. man chmod
9. chmod u+x semester
	可以发现，semester绿了，说明有执行权限了，
	也可以 chmod 700 semester 
	此时，./semester 就可以直接执行了
通过shebang，#!，shell知道应该使用#!指示的路径的interpretor来解释后面的脚本
10. ./semester | grep "last-modified" > ~/last-modified.txt
	不喜欢放在home目录，所以 mv ~/last-modified.txt ./
11. cd /sys/class
	查看自己想看的硬件设备状态吧。。。
12. 


