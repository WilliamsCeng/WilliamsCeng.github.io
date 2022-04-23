[toc]

# Metaprogramming



## build system



## make 工具

### makefile

编写makeile有特定的语法规则。。。

用：冒号描述dependencies，命令描述如何根据dependency的文件，构建该文件

如：

```makefile
xxx.o:xxx.c xxx.h		# 表示xxx.o文件依赖xxx.c 和 xxx.h文件生成，生成的方法时执行命令 gcc
	gcc xxx.c -o xxx.o	# indent must be tab（not 2/4 spaces）
	
# left-hand side:right-hand side
# target:dependencies
```

如果最近一次make之后，dependencies的文件没有改变，那么再次make就不会再次执行（因为没必要执行）



## Semantic versioning

semantic adj. 语义的；语义学的

使用版本号，可以让其它软件更明确地知道它依赖的软件的版本，它的dependency明确的版本



### Semantic versioning

https://semver.org/

在release发布软件、库版本时，遵循的版本命名规则

如：

`8.1.7`：(major version).(minor version).(patch version)，主版本.次版本.补丁版本

1. 假如外部看到的接口，没有任何变化（没有rename,remove，但是修改了内部对用户不可见的bug，内容等），它是向后兼容的backwards compatible（那么patch version 增加）

2. 假如增加了接口，外部可以看到更多接口，它是向后兼容的（那么minor version增加，同时将patch version 设为0）

   但是，因为增加了接口，所以依赖更高minor version版本而写的软件，在更低的minor version版本中，可能无法运行（因为用了新的，低minor version版本没有的接口）

3. 假如修改了软件（如rename,remove了某些function），使得它不再向后兼容backwards incompatible（那么major version增加，同时将minor, patch version设为0）



> With semantic versioning, every version number is of the form: major.minor.patch. The rules are:
>
> - If a new release does not change the API, increase the patch version.
> - If you *add* to your API in a backwards-compatible way, increase the minor version.
> - If you change the API in a non-backwards-compatible way, increase the major version.



## Lock files

A lock file is simply a file that lists the exact version you are *currently* depending on of each dependency



## Continuous integration

Continuous integration, or CI, is an umbrella term for “stuff that runs whenever your code changes”, and there are many companies out there that provide various types of CI, often for free for open-source projects. 

当某事发生时，应该执行什么程序

如：当有人pull request时，应该执行什么test suite去测试这个提交

#### CI工具有

Travis CI 

Azure pipelines

github actions



### Continuous integration testing

### github pages 

github pages：set up a CI action that build your repository as a blog essentially it's it runs a static site generator called Jekyll.

Jekyll takes a bunch of markdown files and then produces a website

github pages可以根据repo自动生成一个网页。它使用Jekyll工具，将md文件用于生成网页



## A brief aside on testing

Most large software projects come with a “test suite”. You may already be familiar with the general concept of testing, but we thought we’d quickly mention some approaches to testing and testing terminology that you may encounter in the wild:

- Test suite: a collective term for all the tests
- Unit test: a “micro-test” that tests a specific feature in isolation
- Integration test: a “macro-test” that runs a larger part of the system to check that different feature or components work *together*.
- Regression test: a test that implements a particular pattern that *previously* caused a bug to ensure that the bug does not resurface.
- Mocking: to replace a function, module, or type with a fake implementation to avoid testing unrelated functionality. For example, you might “mock the network” or “mock the disk”.



### mocking (abstraction)

类似于芯片验证中的模型，模型给出一个模拟了（抽象了）某个功能的组件，但其内部并没有真正地实现某个组件，只是看起来有类似的输入输出效果罢了





## Miscellaneous

### make与cmake？

cmake，可看作是为c project定制的make，可以为c project提供更好的特性

Maven 与 Ant，可看作是为Java定制的make

所以，可以在top使用make，然后subsystem用特定的make 工具，如cmake for C，Maven for Java 





## 课后习题
1. make工具的使用（不想apt-get pdflatex，所以用gcc -o练习了）
>clean：makefile中叫做clean的target，并不用于产生一个叫做clean的文件，而只用于清除一些make命令生成的文件

>`.PHONY（必须大写）`：如果类似clean这种并不实际生成文件的target，在目录中有实际的同名文件时，就会导致make clean命令无法执行，而总是产生“make: "clean" is  up to date”的信息。`.PHONY`用于指定phony target，说明它指定的target只是准备执行的命令，而不是实际要生成的文件。
about phony:https://www.gnu.org/software/make/manual/html_node/Phony-Targets.html

>可以测试一下，有clean文件时，没有`.PHONY`的情况。
```makefile
hello.o:hello.h hello.c
	gcc hello.c -o hello.o
.PHONY:clean
clean:
	rm hello.o
```

2. Rust's build system, 介绍了cargo指定 dependencies版本的方法
https://doc.rust-lang.org/cargo/reference/specifying-dependencies.html
```
caret requirements
明确指定dependency依赖库的版本，如指定 1.2.3版本
tilde requirements
可以指定最低的版本要求，并且不在意某些版本
如：~1.2.3:major和minor版本必须相同为1.2，patch版本最低为3，~1.2:major版本必须相同为1，minor版本最低为2，patch版本无限制，~1：major版本必须相同，为1，minor和patch版本无限制
wildcard requirements
通配符指定特定版本，cargo不支持只用*指定版本
1.*：表示major版本必须为1，minor和patch其余任意
1.2.*：表示major.minor必须为1.2，patch任意
comparison requirements
指定满足关系表达式的版本要求
如>=,<,>1.2.0:大于小于，大于。。。某个版本。。。
multiple requirements
指定版本范围
>=1.2，<1.5,major.minor版本必须大于1.2，小于1.5
```

3. 
```bash
cd git .git/hooks
vim pre-commit
#make hello.o
#if [ $? -ne 0 ];then
#	exit 1
#fi
git commit 
# 设置了 pre-commit 脚本以后，git commit 会自动 invoke 这个脚本，并执行，当脚本以非0值退出时，会终止这次commit，在 .git/hooks/提供的 各种 hook.sample文件中有说明
```

4. 
