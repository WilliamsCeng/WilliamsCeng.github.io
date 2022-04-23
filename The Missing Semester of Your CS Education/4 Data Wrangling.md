[toc]

# Data Wrangling

data wrangling，把一种格式的数据转换成另一种格式

这里说的是从一堆数据中，如何选出自己想要的数据，找出自己感兴趣的信息。可以通过一系列的工具，如正则，xargs，sed，awk等工具实现



### 正则表达式 regular expression

sed: stream editor，流编辑器，可以对其输入流进行操作（如正则等）

 

capture group 捕获组：用于表示想要记住某个值，以便后面使用。正则表达式中用圆括号括起来的就是一个捕获组

随后使用 \n（或$n）来引用正则表达式中第n个捕获组匹配到的值


#### 正则表达式 常用的pattern
. * + ? 
[abc] 匹配其中的任意一个字符
(RX1|RX2) 匹配 其中一个
^ 匹配行首 start of the line
$ 匹配行尾 end of the line

贪婪的匹配，尽可以能多地匹配满足pattern的字符串
通过 在 *,+ 后加 ?，来指定非贪婪的匹配，尽可能少地匹配pattern


正则表达式调试器：regular expression debugger

regex101.com


为了匹配到某个字符串，正则表达式有时候会非常非常长、而且奇怪。。。



awk：column based editor 基于列的编辑器 

因为，linux的许多命令行工具，都是以行输入输出的，所以基于列的编辑工具，有时候会很实用

https://backreference.org/2010/02/10/idiomatic-awk/


### xargs：

xargs接收若干行输出，把它们转为参数


## 课后习题
1. 正则表达式学习网站： https://regexone.com/
2. 包含至少三个a，没说重复。。。不以's结尾
```bash
# wc -l命令（wc，word counts？），可以实现 grep ".*" -c 一样的功能
cd /usr/share/dict
cat words | grep ".*a.*a.*a.*[^('s)]$" -c
# 393
cat words | grep ".*a.*a.*a.*[^('s)]$" | sed -E 's/.*(\w\w)$/\1/' | grep ".*" -c
# 393
cat words | grep ".*a.*a.*a.*[^('s)]$" | sed -E 's/.*(\w\w)$/\1/' | uniq -c | grep ".*" -c
# 339

# How many of those two-letter combinations are there?
cat words | grep ".*a.*a.*a.*[^('s)]$" | sed -E 's/.*(\w\w)$/\1/' | sort | uniq -c | grep ".*" -c
# 76
cat words | grep ".*a.*a.*a.*[^('s)]$" | sed -E 's/.*(\w\w)$/\1/' | sort | uniq -c | sort -h | grep ".*" -c
# 76

# the three most common last two letters 
cat words | grep ".*a.*a.*a.*[^('s)]$" | sed -E 's/.*(\w\w)$/\1/' | sort | uniq -c | sort -h | tail -n3
     36 ly
     48 al
     85 an
cat words | grep ".*[a|A].*[a|A].*[a|A].*[^('s)]$" | sed -E 's/.*(\w\w)$/\1/' | sort | uniq -c | sort -h | tail -n3
     36 ly
     49 al
    101 an
    
# And for a challenge: which combinations do not occur?
# 通过比较两个文件，已有的两个字母组合：所有的字母组合
cat /usr/share/dict/words | grep ".*a.*a.*a.*[^('s)]$" | sed -E 's/.*(\w\w)$/\1/' | sort |  uniq -c | awk '{print $2}' | tee last-two-letters

# two-letter-combinations-script.sh
#!/usr/bin/env bash
for i in {a..z};do
	for j in {a..z};do
		echo $i$j
	done
done
chmod u+x two-letter-combinations-script.sh
two-letter-combinations-script.sh >> two-letter-combinations

diff --unchanged-group-format='' last-two-letters two-letter-combinations | tee not-occur-combinations | wc -l
```

3.  
	
4. 
```bash
# 取得 boot time 的logs
journalctl | grep "Startup finished in" | tee boot-logs 
# 匹配 boot 总时间，并用sed将文本替换为 boot time
cat boot-logs | grep -E ".*systemd\[1\].*" | sed -E 's/.*= (.*)\.$/\1/' 
1min 720ms
1min 4.008s
1min 2.105s

# 将时间统一为秒
# shell 可以使用 \进行命令换行。。。
cat boot-logs | grep -E ".*systemd\[1\].*" | sed -E 's/.*= (.*)\.$/\1/' | sed -E 's/(min)/*60+/' 
| sed -E 's/(ms)/\/1000/' 
| sed -E 's/s$//' 
| sed -E 's/\s//' 
| tee to-be-calculated

1*60+720/1000
1*60+4.008s
1*60+2.105s

# 使用 bc 即可计算一个表达式
cat to-be-calculated | bc -l
# min
| sort -n | head -n1
# min
| sort -n | tail -n1
# mean
cat to-be-calculated | wc -l
43
cat to-be-calculated | bc -l | paste -sd+ | bc -l 
| xargs echo 1/43*  | bc -l
59.39813953488372092251
# median 中位数，43个数，中位数就是排序后的第22个，所以两种方法都可以解
cat to-be-calculated | bc -l | sort -n | paste -sd' ' | awk '{print $22}'
cat to-be-calculated | bc -l | sort -n | head -n22 | tail -n1
56.690
```
5. 
题目都没读懂。。。
6. 





