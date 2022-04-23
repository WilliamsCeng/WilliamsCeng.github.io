[toc]

# Regular Expressions
RegexOne：https://regexone.com/
有Lessons，还有Problems。可以尝试解决一下Problems

## 元字符 metacharacter

写出的用于匹配特定文本的正则表达式，叫做pattern

`abc...`：直接匹配字符
`123....`：直接匹配数字
`\d`：匹配任意数字
`\D`：匹配任意非数字

`.`：.(dot)匹配任意字符，通配符的概念（wildcard，‘外卡’）
`\.`：\转义符号（slash，斜杠\，backslash，反斜杠/），\.匹配句号（period），转义表示.dot（dot should be escaped）

`[abc]`：匹配a，b，或c，方括号`[]（square braces）`，只匹配方括号内部的任意一个字符
`[^abc]`：^(hat)匹配除了方括号内指定的字符之外的任意字符，补集
`[a-zA-Z0-9]`：范围匹配，-(dash)，匹配a-z范围内的字符

`\w`：匹配任意字母或数字字符（any alphanumeric character），等价于[A-Za-z0-9_]
`\W`：匹配任意非字母或数字字符（any Non-alphanumeric character）

`{}`：花括号curly braces，指定 匹配次数，如 a{3}，匹配3次a，甚至可以指定匹配次数范围 [ab]{1,3}，匹配a或b，1到3次
`*`：star，匹配0次或更多次zero or more repetitions，a*，匹配0个或更多个a
`+`：plus，匹配1次或更多次one or more repetitions，[abc]+，匹配1个或更多个a，b，c中的任一个字符

`?`：问号 question mark，匹配0或1次，可选性optionality，如：ab?c可以匹配ab，或abc，因为b?可选，匹配0或1次，单纯匹配?问号，则需要转义字符\?（question mark should be escaped to match the actual question mark）

quantifiers数量修饰语，`{n},{m,n},*,+,?`叫做quantifiers数量修饰语，用于指定相关匹配字符的重复次数repetitions



`\s`：匹配任意空白字符（whitespace），包括 space空格，tab缩进？`\t`，new line新行`\n`，以及回车carriage return`\r`。
`\S`：匹配任意非空白字符。包含标点符号punctuation。感觉所有大写元字符，都是对小写情况的补集。

`^`：匹配句首位置，^(hat)，注意与方括号内`[^abc]`的区别，
`$`：匹配句尾位置，`$(dollar sign)`，`^a.*c$`:表示匹配一行 以a开头，以c结束的字符

Match Groups捕获组的概念：Regular Expressions不仅可以用于匹配文本，还可以提取出信息用于进一步的处理extract information for further processing。
使用圆括号`()，parentheses`括起来的元字符，其匹配到的文本，就会被捕获，可以单独提取出来

Nested Groups嵌套组的概念：在捕获组中，嵌套捕获组。~~捕获捕获组的内容~~

group work的概念，重复多次匹配一个字符子串（感觉翻译地不准确啊），apply quantifiers on sequences of characters instead of the individual characters themselves. 对一个字符串使用数量修饰符。
用圆括号()括起来，就表示对整个字符子串进行匹配，后面就可以加quantifiers了，
(ab){2}，会匹配abab

`|`：logical OR，aka. the pipe逻辑或，亦称管道符号。表示匹配一组候选字符中的一个即可，different possible sets of characters. `（milk|bread|juice）`：匹配milk 或 bread 或 juice即可。


## 杂项 Miscellaneous

大写字符的元字符，都是求小写字符的补集。如`\s`：匹配所有空白符，`\S`：匹配所有非空白符

`\b`：匹配一个word与non-word character之间的boundary。如：\w+\b匹配一个完整的word

#### back referencing反向引用的概念？：
注意：有些regular expression engines并不支持
back referencing 使得可以引用捕获组捕获到的字符。如通过\0：表示整个匹配到的文本内容，\1：表示第一个捕获组捕获到的字符内容，\2，\n同理

#### More
greedy vs. non-greedy：贪婪与非贪婪模式
greedy在字符串中匹配尽可能多次，匹配到能匹配的最后一次，然后结束
non-greedy可认为只匹配第一次，然后结束匹配
在 *,+后使用?，表示非贪婪匹配non-greedy

贪婪的匹配，尽可以能多地匹配满足pattern的字符串
通过 在 *,+ 后加 ?，来指定非贪婪的匹配，尽可能少地匹配pattern

`**`：使用双*，通过通配符去列出文件系统的文件。**two asterisks to match nested directories**
`*.js`会match files such as helloworld.js，但不会match Test/helloworld.js
而使用`Test/**/*.js`则可以match到Test目录下，以及子目录下的所有js文件，如：Test/helloworld.js，Test/UI/helloworld.js，Test/UI/Feature/helloworld.js等
https://stackoverflow.com/questions/28176590/what-do-double-asterisk-wildcards-mean
