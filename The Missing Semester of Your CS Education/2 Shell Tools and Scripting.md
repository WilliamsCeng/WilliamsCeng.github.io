[toc]



# Shell Tools and Scripting



## bash 脚本bash scripts：
Shell scripts，许多shell都会有自己的脚本，这里主要关注bash脚本(most common)

### 变量赋值：

foo=bar，中间不能有空格，因为空格在shell中，用于区分关键字，

假如是 foo = bar，那么shell 会认为先执行 foo 这个程序，再执行符号 = ，再执行bar这个程序



#### 取变量值：$

$foo，可以取出变量值bar，当用于字符串时，应用双引号： echo “foo is \$foo”，才会输出 foo is bar

假如是单引号，则原样输出 如 echo ‘foo is $foo’，输出 foo is \$foo



### 函数：

可以写函数

example(){

...

}

其中 $1,\$2,\$...可以用于取出传给函数的第n个参数

如 example foo，那么函数内，$1就对应foo的值



### $

$有很多用处，

\$?表示取出上一条命令执行的标准错误流（stderr），类似于stdin，stdout的第三种标准输入输出流

$0表示当前执行的命令的名字

$#表示传给函数的参数个数

$@表示展开所有参数为一个参数列表



#### /dev/null

UNIX系统的一种特殊设备，写到这里的内容都会被丢弃，

cat file > /dev/null，内容不会输出，都被丢掉了



### 逻辑操作符 || &&，逻辑短路的情况
通常可以配合 $? 取出的exit code，巧妙地利用逻辑短路的情况来设置某些语句会不会执行


### shell 单行命令分隔符，冒号；，可以一行写多个命令



### \>> 操作符，append到文件末尾



### 通配符 * ?，以及{}展开符？

foo{,1,2,3} 会被展开成 foo1，foo2，foo3



### #! 叫做 shebang，这个词源于# sharp，! bang，

shell 通过 script 脚本第一行 #! shebang开头的一行来了解怎么运行这个程序，要调用哪个解释器来执行脚本

如python script，#!/usr/local/bin/python，或 #!/usr/bin/env python，告诉shell要用#!指定目录里面的 python去执行这个脚本 

如bash，#!/usr/local/bin

所以shell执行的脚本文件，不一定非得是bash


### shellcheck 工具，可以用于debug bash脚本



### man --help的好朋友 tldr工具

tldr，too long，don't read，类似man，但是可以直接给出简单的用法示例。。。感觉可以对这个非常熟悉了再用这个tldr工具

如：tldr tar



### find工具：在文件系统中查找文件或代码
find

fd

grep

ripgrep

ack

ag：The Silver Search

fzf：模糊搜索工具，交互式的。。。高级货



### 查找已经执行过的命令

上下箭头。。。

history命令



### ls类似的命令

tree

broot

### 目录导航工具
fasd

autojump



## 其他Shell

fish：输入命令时，会动态地搜索展示历史命令中执行过的类似命令

zsh


## 课后习题：
1. man ls 
ls -a -hs -clt --color
ls -ahsaclt --color
- ls -a
- ls -hs
- ls -clt
- ls --color
2. 先在命令行中source marco.sh，source polo.sh，随后就可以直接使用marco和polo进行目录切换了
直接进行source marco.sh polo.sh，并不可行，第二个polo没有source成功
而且，就算marco.sh 和polo.sh 没有x执行权限，也可以通过source后，直接调用成功，牛，看来需要man source一下了
```bash
#marco.sh
marco(){
  echo "$(pwd)" > ~/Templates/tmp/missing/marco-pwd.txt
}
```
```bash
#polo.sh
polo(){
 cd "$(cat ~/Templates/tmp/missing/marco-pwd.txt)"
}
```
3. 这里之前自作主张地用sh rarely-fails-script.sh来运行脚本。。。有些问题。。sh跟bash是不一样的，（STFW，sh是被bash替代的老shell），这就导致sh频繁报错。。。
首先脚本已经通过#!来指示用bash解释运行脚本，再用sh就是多余，其次sh的语法跟bash的语法有些不同，所以导致sh ...之后频繁报错，然后就乱改。。。
了解一下bash的语法，很容易完成这个题目
```bash
# rarely-fails-script.sh 
#!/usr/bin/env bash

n=$(( RANDOM %100 ))

#n=42 # for debug

if [[ n -eq 42 ]]; then
	echo "Something went wrong"
	>&2 echo "The error was using magic numbers"
	exit 1
fi

echo "Everything went according to plan"
```
```bash
# debug-script.sh
#!/usr/bin/env bash

# repeat script

n=1

 ./rarely-fails-script.sh > ./debug-log.txt 2>> ./debug-log.txt

while [[ $? -ne 1 ]]; do
 ((n=$n+1))
 ./rarely-fails-script.sh > ./debug-log.txt 2>> ./debug-log.txt
done

echo "After $n runs, it fails" 
cat ./debug-log.txt

```

4. 如何recursively的find居然卡住了不久。。。
```bash
find . -name "*.html"
find . -name "*.html" -print0 
find . -name "*.html" -print0 | xargs -0 echo
find . -name "*.html" -print0 | xargs -0 tar -cvf html-tar.tar
```
5. 
list all files by recency
```bash
ls -a -R #list all files recursively 
ls -aR -l #using long list format
ls -alR -h #human readable
ls -alhR -t #sor by time,newest first
```
recursively find the most recently modified file in a directory
STFW，害
```bash
find . -type f -printf '%TY-%Tm-%Td %TT %p\n' |sort -r
```

