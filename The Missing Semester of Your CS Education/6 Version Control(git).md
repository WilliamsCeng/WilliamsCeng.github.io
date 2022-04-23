[toc]



# Version Control(git)



## Version Control systems

version control systems are tools that are used to keep track of changes to source code or other collections of files or folders.

version control systems track the history of changes to some set of documents and they facilitate collaboration





## Model of Files and Folders

git 看待 数据的 方式...，git的数据结构

就是文件目录树tree

（root）目录就是 git track的目录

文件夹：被称为 “tree”

文件：被称为 “blob”
```
<root> (tree)
|
+- foo (tree)
|  |
|  + bar.txt (blob, contents = "hello world")
|
+- baz.txt (blob, contents = "git is wonderful")
```
The top-level tree contains two elements, a tree "foo" （that itself contains one element, a blob "bar.txt"), and a blob "baz.txt".

![image-20220412102129573](6 6 Version Control(git).assets/image-20220412102129573.png)



## Model of History

git 如何 记录 数据 的变化

directed acyclic graph：有向无环图

使用有向无环图来记录 Files and Folders 的变化

下图中，每一个圈表示 git 对track的目录的一个snapshot 快照（Git 把这些snapshot称为commit）

#### （Git 把这些snapshot称为commit）

每个圈还有相应的description 用于区分彼此，如 author，message...

![image-20220412102017532](6 Version Control(git).assets/image-20220412102017532.png)

git 的branch分支功能，使得可以同时进行多个feature的同时开发，或者bug fix 工作，分别完成以后，可以再把各个修改merge 合并到一起

所以，通常就是有一个master分支，用于合并各个branch，

每次需要添加新特性、功能，就branch 一个新分支，在分支上进行开发，开发完成再merge进master里



### Data model数据模型，pseudocode 伪代码，用于理解 git 里面的数据结构

而commit中包含的东西，如parents，即本commit来源的父commit等内容，都是object的ID指针即hash值（sha1算法计算）

而object才是真实存储在硬盘的数据，并通过其ID进行区分与索引

啊，自己看着pseudocode理解吧，老师还是讲得很清楚的

![image-20220412104540030](6 Version Control(git).assets/image-20220412104540030.png)

```pseudocode
// a file is a bunch of bytes
type blob = array<byte>

// a directory contains named files and directories
type tree = map<string, tree | blob>

// a commit has parents, metadata, and the top-level tree
type commit = struct {
    parents: array<commit>
    author: string
    message: string
    snapshot: tree
}
```

#### Objects and Content-addressing 对象与内容寻址
```
type object = blob | tree | commit

objects = map<string, object>

def store(object):
    id = sha1(object)
    objects[id] = object

def load(id):
    return objects[id]
```

即对象object在Git中可以是blob，tree或commit，object对象通过SHA-1 hash进行寻址，查找。
hash值，通过sha-1算法计算出的hash值是40个16进制的字符串，也就是160bit 

![image-20220412105844382](6 Version Control(git).assets/image-20220412105844382.png)

##### 通过 git cat-file -p hash哈希值 
可以一步步查看到实际存储的内容哦


#### References 引用
因为hash太长、无意义的字符，因此，有references的概念，将可读的字符（human-readable，其实就是给某个hash对应的commit命名映射一下，容易记住），映射为长串的哈希值

**reference可以看成是指向某个commit的指针**
这样就可以通过 可读的字符 去 索引 特定hash值对应的前面graph中的 圈圈（git 中的某一个快照）
```
references = map<string, string>

def update_reference(name, id):
    references[name] = id

def read_reference(name):
    return references[name]

def load_reference(name_or_id):
    if name_or_id in references:
        return load(references[name_or_id])
    else:
        return load(name_or_id)
```


如：下图用一个 fixbug 字符 指向合并之后的 node，因此 fixbug字符对应了一个特定的内容的快照

![image-20220412110036917](6 Version Control(git).assets/image-20220412110036917.png)


#### Repositories
Git 的repo就是 前面所说 objects 和 references数据的集合，git把这些存在disk上

#### git 的 commands 就是操作 这些 pseudocode 展示的objects data 和 references data
所以，输入git 命令的时候，想想这个命令到底在怎样操作着这些objects 和 references

其实这些objects 和 references 都只是一个概念，要自己好好理解到底指代的是什么

同时，Git中，有一个特殊的reference，`HEAD`：用于指示当前位于哪个commit上



## git 的staging area概念（缓存区的概念？）

git add 把所有 新的内容 添加到 staging area中，然后git commit 会把 staging area 中的所有内容生成一个快照

可以更加精确地控制下一次commit 会 把哪些内容 添加到快照里，而不是简单地把所有东西都take a snapshot快照

比如，只添加 makefile，而不添加 compile 之后生成的 .o .elf文件什么的	


## git commands 

git init

初始化一个git repository 进行版本控制，通常在一个 folder 下面进行（即 前文的 (root) 目录），随后就可以用git 对该folder进行 track

会生成一个 .git 的隐藏folder，里面就有 objects 和 refs 文件夹


git help xxx，如 git help init 


git status


#### git status



#### git add：

git add，不只是对新添加的文件或者文件夹，对于修改后的新文件，也应该使用 git add 来指示把 新的内容添加到下一次的commit中

实际上是把 新的内容add到了staging area缓存区中，等待被commit



#### git commit 

生成 git repository 里面所有add了的内容的快照，也就是 前文 graph中的圈圈

实际只commit了被add到了 staging area缓存区中的内容



#### 通过 git cat-file -p hash哈希值 

可以一步步查看到实际存储的内容哦



#### git log 

git log --all --graph --decorate

可以让git log 的结果显示出 类似前文的 graph 图形化结果

git log --all --graph --decorate --oneline

更简洁



#### git diff 

可以查看当前目录current working directory的内容 与最近一次 commit 之间有什么不同

git diff hash值ID（指定的圈圈） [objects]

可以查看当前目录current working directory的内容 与hash值指定的快照之间有什么不同，还可以指定查看某一个objects的区别





## Branching and Merging 分支与合并

#### git branch

git branch 打印所有的branch

git branch -vv，打印额外的信息，very verbose

git branch 分支名，创建以分支名命名的新branch



git checkout 分支名

切换到分支名指定的分支

git checout -b 分支名，创建分支名对应的branch，并且checkout 过去

等价于执行了 git branch 分支名; git checkout 分支名



git checkout 文件名

会把文件相应的没有commit的修改丢弃掉，



#### git merge

git merge

fast forward merge 说明没有任何conflict 地合并了 分支

如果有 conflicts，就需要开发者自己去solve掉conflicts



git 提供了 merge tool工具辅助进行 solve the conflicts

git mergetool

可以设置mergetool启动特定的merge 工具，如vimdiff



或者可以manually 编辑 有conflicts的文件，然后通过 git add 添加修改的内容，
再使用 git merge --continue 继续merge的过程





## git remote

使用git与别人合作开发的方法

git 可以知道 当前本地的 git repo 是否有其他remote 的 同一个repo，即本地repo 的remote 的clone repo



git remote 会打印所有 remote 的 git repo[sitory] 



#### 添加remote

git remote add `<name> <URL>`，可以配置当前的git repo，告诉它有一个 remote的repo在同时进行版本控制，这个remote 就是URL对应的 repo，并把它叫做name 



#### git push：

`git push <remote name> <local branch>:<remote branch>`

把本地某一个 local branch，push到远程机器remote上，并内容push在它的remote branch上。



#### git clone：

`git clone <url> <folder name>`

git clone 可以克隆clone url指定的repo到 本地 的folder里，并用该repo初始化init 本地clone得到的git  repo



#### git fetch：

`git fetch <remote>`

从remote机器的repo中取回repo的内容，看看是否有更改

fetch以后，就可以git merge 取回的内容



#### git pull：

git pull

等价于执行 git fetch; git merge 这两条命令





## git config 

配置git ，可以通过 git config 或者通过编辑 .gitconfig文件





## 进阶 git add

git add 甚至可以交互性地选择哪些 修改将被 add到staging area 缓存区中

如：git add -p 文件就可以交互性地选择需要add的修改内容了

然后，git diff --cached，可以查看哪些内容已经被staged

git diff，查看哪些内容还没有被staged

然后git commit 就会把staged 的内容commit了



## 其他一些有用的 git commands

##### git blame 

git blame 文件名，会显示出文件的每一行修改来源于哪一次commit



#### git stash

git stash 还原repo为最近的一次commit，但已有的没有stage 或 commit的修改，会被存储起来（正如 stash英文意思所示）

git stash pop，可以将存储起来的修改再还原到repo中，即undo the stash



#### git bisect

查找commit 历史记录的有用工具，STFW有什么用，以及怎么用，



### git ignore file

指定git 不对某些文件进行版本控制，即每次 commit 或者 git diff等操作的时候，不理会它们的变化与修改

修改 .gitignore  文件即可



### Miscellaneous

#### 图形化git客户端

#### 把git 集成到 shell中


## 课后习题
1. Pro Git：https://git-scm.com/book/en/v2
Learn Git Branch：https://learngitbranching.js.org/
2. 
```bash
# 1. version history
git log --graph [--onelline]
# 2. version of README.md
git log README.md [--graph --oneline]
# 3. commit message associated with the last modification of specific line
git blame _config.yml
>   a88b4eac (Anish Athalye 2020-01-17 15:26:30 -0500 18) collections:
git show a88b4eac
>   Redo lectures as a collection
```

3.  git filter-repo 
4. git stash
```bash
echo "Hello World" >> README.md # README.md 文件的末尾 写入 Hello World
git stash # README.md 文件的末尾并没有 Hello World
git log --all --graph --oneline # 出现一条 refs/stash指向的记录，但不使用--all将无法看到

*   7a8f0e9 (refs/stash) WIP on master: bbbe7d8 remove sensitive data
|\  
| * 9c70a66 index on master: bbbe7d8 remove sensitive data
|/  
* bbbe7d8 (HEAD -> master, origin/master) remove sensitive data

git stash pop # README.md 文件的末尾重新出现 Hello World

# git stash 在短暂查看修改的内容时非常有效
```

5. git alias
```bash
git config --global alias.graph 'log --all --graph --decorate --oneline'
git graph
```

6.  
```bash
git config --global core.excludesfile ~/.gitignore_global
```

7. try to submit a pull request on GitHub
look at this tutorial：https://github.com/firstcontributions/first-contributions


 



# Git command-line interface

To avoid duplicating information, we’re not going to explain the commands below in detail. See the highly recommended [Pro Git](https://git-scm.com/book/en/v2) for more information, or watch the lecture video.

## Basics

-   `git help <command>`: get help for a git command
-   `git init`: creates a new git repo, with data stored in the `.git` directory
-   `git status`: tells you what’s going on
-   `git add <filename>`: adds files to staging area
-   `git commit`: creates a new commit
    -   Write [good commit messages](https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html)!
    -   Even more reasons to write [good commit messages](https://chris.beams.io/posts/git-commit/)!
-   `git log`: shows a flattened log of history
-   `git log --all --graph --decorate`: visualizes history as a DAG
-   `git diff <filename>`: show changes you made relative to the staging area
-   `git diff <revision> <filename>`: shows differences in a file between snapshots
-   `git checkout <revision>`: updates HEAD and current branch

## Branching and merging

-   `git branch`: shows branches
-   `git branch <name>`: creates a branch
-   `git checkout -b <name>`: creates a branch and switches to it
    -   same as `git branch <name>; git checkout <name>`
-   `git merge <revision>`: merges into current branch
-   `git mergetool`: use a fancy tool to help resolve merge conflicts
-   `git rebase`: rebase set of patches onto a new base

## Remotes

-   `git remote`: list remotes
-   `git remote add <name> <url>`: add a remote
-   `git push <remote> <local branch>:<remote branch>`: send objects to remote, and update remote reference
-   `git branch --set-upstream-to=<remote>/<remote branch>`: set up correspondence between local and remote branch
-   `git fetch`: retrieve objects/references from a remote
-   `git pull`: same as `git fetch; git merge`
-   `git clone`: download repository from remote

## Undo

-   `git commit --amend`: edit a commit’s contents/message
-   `git reset HEAD <file>`: unstage a file
-   `git checkout -- <file>`: discard changes

# Advanced Git

-   `git config`: Git is [highly customizable](https://git-scm.com/docs/git-config)
-   `git clone --depth=1`: shallow clone, without entire version history
-   `git add -p`: interactive staging
-   `git rebase -i`: interactive rebasing
-   `git blame`: show who last edited which line
-   `git stash`: temporarily remove modifications to working directory
	- `git stash pop`，可以将存储起来的修改再还原到repo中，即undo the stash
-   `git bisect`: binary search history (e.g. for regressions)
-   `.gitignore`: [specify](https://git-scm.com/docs/gitignore) intentionally untracked files to ignore


## Github 使用
### 基本使用
refs.[Pro Git]第2章Git Basics https://git-scm.com/book/en/v2/Git-Basics-Getting-a-Git-Repository
1. configure and initialize a repository
2. begin and stop tracking files 
3. stage and commit changes 
4. set up Git to ignore files and file patterns
5. undo mistakes quickly and easily
6. browse the history of project and view changes between commits
7. push and pull from remote repositories 

[Pro Git]的第6章：https://git-scm.com/book/en/v2/GitHub-Account-Setup-and-Configuration

### contributing to a project
在github上fork别人的repo到自己帐号的repo中
本地clone下来，然后branch一个新feature分支修改，再push，pull到自己帐号的repo中
再在github上发起pull request，等别人merge自己的pull request

#### 工作流程
workflow：fork -> clone -> edit -> pull request 

### Maintain a Project
refs.[Pro Git]第2章Git Basics https://git-scm.com/book/en/v2/Git-Basics-Getting-a-Git-Repository
在gihub新建一个repo，
把URL作为本地git repo的 remote
就可以pull，push了

### Scripting GitHub
就是一些 Continuous  Integration Services，在Metaprogramming一课有介绍


#### Services

#### Hooks

#### GitHub API
