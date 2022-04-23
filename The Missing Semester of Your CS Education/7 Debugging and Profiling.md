[toc]

# Debugging and Profiling



## printf debugging

打印调试。。。



## logging 日志

可以定义 严重性级别 define severity levels，并基于它们进行过滤

可以通过 给不同级别的log使用不同颜色的字体



## python的debugger调试器
Debuggers are programs that let you interact with the execution of a program.

ipython：python -m ipdb xxx.py

C、C++、C-like language： GDB

 

## static analysis tools静态分析工具

**静态分析工具（感觉上类似语法分析工具，自动识别语法错误）**

不同的语言有不同的debuger和static analysis tool工具，需要的时候就可以STFW看看


调试程序，并不总是需要运行程序，如找到一个mistype的变量

python的静态分析工具

：pyflakes xxx.py

：mypy xxx.py



甚至有英语的static analyzer，writegood

writegood notes.md，哈哈哈哈



#### 但是这些静态分析工具，一般都可以集成到编辑器中
集成到编辑器中，叫做linter，用于code linting，在代码编辑时，实时检查code的语法与风格
linting (syntax checking and semantic errors)



## Profiling

debugging is more about finding issues with the code

profiling is more about optimize the code



### 程序的运行时间：

real time：某一语句/或程序执行从开始到结束实际经历的时间 

user time：the amount of time your program spent on the CPU doing user level cycles.（- Amount of time spent in the CPU running user code 程序执行用户级代码所用的时间？） 因为 like UNIX系统，有 user level code 和 kernel level code

system time：the amount of time your grogram spent on the CPU executing kernel mode instructions.（- Amount of time spent in the CPU running kernel code 程序执行内核级代码所用的时间？）



### Profiler分析器

profiler 通常指 CPU profiler，用于 identifying what time is being spent on the CPU.



### tracing profiler 和 sampling profiler
Tracing profilers keep a record of every function call your program makes 

whereas sampling profilers probe your program periodically (commonly every millisecond) and record the program’s stack.


#### tracing profiler

python：python -m cProfile 



#### line profiler

展示各语句执行占用的时间比例



#### memory profiler

与CPU profiler不同，memory profiler 分析memory resource的使用情况

同样的，还有其他资源的profiler

python：memory_profiler，python -m memory_profiler xxx.py

C：Valgrind





#### 使用pref 工具来分析命令行执行的情况

sudo perf command...



### visualization可视化展示 profiling结果

#### Flame Graph 



#### Call Graph

调用关系图

### Miscellaneous

#### HTOP
交互式地查看硬件资源的使用情况

#### ncdu
du 命令的交互式进阶版

#### LSOF
list of open files

#### benchmark 工具
hyperfine 用于比较两个命令执行的速度
如：`hyperfine --warmup 3 ‘fd -e jpg' 'find . -iname "*.jpg" '`比较fd和find两个命令查找所有jpg图片的速度


## 课后习题
### Debugging
1. 
```bash
journalctl -r -S "yesterday" | grep -E ".*root.*"  
``` 
2. pdb：python debugger。跟着玩一遍：https://github.com/spiside/pdb-tutorial
pdb的几个常用命令，跟gdb挺像的
	1.  `l(ist)` - Displays 11 lines around the current line or continue the previous listing.
	2.  `s(tep)` - Execute the current line, stop at the first possible occasion.
	3.  `n(ext)` - Continue execution until the next line in the current function is reached or it returns.
	4.  `b(reak)` - Set a breakpoint (depending on the argument provided).
	5.  `r(eturn)` - Continue execution until the current function returns.
	6. `c(continue)` - 执行到 breakpoint 或者 报错
	7. `!(bang)` - 执行 一行python语句
3. 
```bash
sudo apt-get shellcheck
shellcheck ...
# 装个 ale

```
4. Read about [reversible debugging](https://undo.io/resources/reverse-debugging-whitepaper/) (a.k.a. time travel debugging).
不仅能够一步步往前执行代码，还能够在断点处，往回查看代码的执行。
step backward from the point of failure line by line until the bug itself is found
moving freely backwards and forwards through the history

UDB，~~GDB的reversible debugging版~~
RevPDB，PDB的reversible debugging版？

### Profiling

5. 
```bash
# cProfile
python3 -m cProfile -s tottime sorts.py   


# line_profiler
sudo apt-get install python3-pip
pip3 install line_profiler[ipython]
# line_profiler need explicitly told what functions to profile. 
# you decorate the functions you want to profile with @profile.
# 在待分析的 函数上 使用decorator @profile 
vim sort.py
# @profile
# def insertionsort(arry):
#...
# @profile
# def quicksort(arry):
kernprof -l -v sorts.py
> 报错，并有建议 sudo apt-get install python3-line-profiler

> sudo apt-get install python3-line-profiler 之后 kernprof -l -v sorts.py 即可


# memory_profiler
python3 -m memory_profiler sorts.py
# 运行以后似乎没有效果，二者的Mem usage相同。。。

# challenge: use perf... skip逃课。。。
```

6. 
```bash
# 执行 pycallgraph，提示 sudo apt install..
# 那就 install pycallgraph，然后自动安装了 graphviz
sudo apt-get install python3-pycallgraph                    
pycallgraph graphviz -- ./fib.py # 生成图片pycallgraph.png
# fib0 called 21次
# 取消代码注释后，fibN 均只执行一次，牛啊，这个memoizing     

```
7. 
```bash
python3 -m http.server 4444
lsof | grep LISTEN
kill <PID>
```

8. 
```bash
sudo apt-get install htop
```

9. 
```bash
curl ipinfo.io
# 仅仅通过ip，居然可以拿到这么多信息。。。
# wireshark 去sniff request and reply packets that curl sent and received
```  


