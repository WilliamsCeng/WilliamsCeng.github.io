[toc]

# Editors (Vim)
写文章与写代码的编辑器
写代码：Vim,Emacs,VS Code,Sublime
写文章：word,or something

Vim 入门tutor，命令行直接 vimtutor...
还有vim 游戏，如 vim golf，vim adventure...

### vim 标记按键的方法
单字母：i j q ...表示单纯的按键
组合键：
`^V,Ctrl-V,<C-V>`表示 ctrl + v 组合键

### vim 的模式
vim 设计哲学：modal editing，不同的编辑模式，所以可以复用有限键盘，实现无限的编辑需求

normal mode 正常模式：按i进入insert mode
insert mode 插入模式：按<ESC>键返回normal mode
replace mode 替换模式: R
selection mode 选择模式: 
- visual :V
- visual -line :S-V
- visual-block:C-V
Command-line mode 命令行模式: :冒号进入

### vim 的 Marcos 
vim 的宏也太帅了吧，
做课后习题，录制marcos，然后用录制好的marcos直接完成了 xml 到 json的格式转换，666，帅！！！

录制宏，按q，再任意按键x，左下角出现 recording @x 的提示，然后所有操作都会被记录下来，完成操作后，按q结束。
此时，所有操作被映射到 @x 中，只要输入 @x，就会自动复现录制的操作，
而且还可以在前面 加 [count]，表示要重复多少次宏操作。。。


## 课后习题
8. 用 macros 完成 XML 到 JSON格式的转换
帅的不行
