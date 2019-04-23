python之正则表达式模块re
----

####  re模块的match，search，findall方法怎么用？
* **match 只从字符串的开头开始匹配，如果匹配失败则停止向后查找**

匹配成功：
```python
>>>print(re.match('super', 'superisstitionsuper'))
<re.Match object; span=(0, 5), match='super'>
```
匹配失败：
```python
>>>print(re.match('super', 'isstitionsuper'))
None
```

* **search 将向前扫描字符串，报告它找到的第一个匹配项**

```python
>>>print(re.search('super', 'superstitionsuper').span())
(0, 5)
```
* **findall 搜索整个字符串，找出匹配的所有子串**

```python
>>>re.findall('super', 'superstitionsuper')
['super', 'super']
```
    
#### re如何执行多行搜索与单行搜索？
##### 方法1: re中match，search，findall等方法可以通过传入标志参数flags来确定单行还是多行搜索。
单行模式：re.DOTALL或re.D
多行模式：re.MULTILINE或re.M

单行模式下，.符号和可以匹配换行符\n，因而将多行字符串作为一个连贯的字符串搜索对象。
```python
>>>print(re.match(r'<.*>','<123\n456\n789>\n',flags=re.DOTALL))
<re.Match object; span=(0, 13), match='<123\n456\n789>'>
```
多行模式下，.符号不能匹配换行符\n,因而用.匹配任意字符串时到\n就结束了。re默认模式是单行模式。
```python
>>>print(re.match(r'<.*>','<123\n456\n789>\n'))
None
```


##### 方法2: re模块还支持在正则表达式中使用语法来开启单行或多行模式。
(?aiLmsux-imsx:…)，'a', 'i', 'L', 'm', 's', 'u', 'x'这些字符为表达式的其中一部分 设置 或者 去除 相应标记。m代表re.M (多行), s代表re.S (点匹配所有字符，单行模式)

```python
#单行模式：
>>>print(re.match(r'(?s:<.*>)','<123\n456\n789>\n'))
<re.Match object; span=(0, 13), match='<123\n456\n789>'>
```
```python
#多行模式：
>>>print(re.match(r'(?m:<.*>)','<123\n456\n789>\n'))
None
```

#### re如何执行或忽略分组提取？
findall函数对命名分组无法根据分组名提取组。
match和search函数可以对通过在正则表达式中增加括号的方法，分组提取符合括号内表达式的内容。这样获取的分组就是捕获组。
捕获组的内容可以通过group()方法访问。分组按照左括号的递增次序来区分分组。
```python
#group(0)表示匹配到的整个字符串
>>>print(re.match(r'<(<(.*)>)>','<<<456>>>\n',flags=re.DOTALL).group(0))
<<<456>>>
```

```python
#group(1)表示第一个捕获分组
>>>print(re.match(r'<(<(.*)>)>','<<<456>>>\n',flags=re.DOTALL).group(1))
<<456>>
```
```python
#group(2)表示第一个捕获分组
>>>print(re.match(r'<(<(.*)>)>','<<<456>>>\n',flags=re.DOTALL).group(2))
<456>
```

如果不想提取内容，则加括号的正则表示式就是非捕获组，意即分组所匹配的子字符串 不能 在执行匹配后被获取或是之后在模式中被引用。
非捕获组的语法为(?:exp)
```python
#虽然这个字符串匹配成功了，但是由于是非捕获组，访问group(1)失败
>>>print(re.match(r'(?:<.*>)','<123\n456\n789>\n',flags=re.DOTALL).group(0))
<123
456
789>
>>>print(re.match(r'(?:<.*>)','<123\n456\n789>\n',flags=re.DOTALL).group(1))
IndexError: no such group
```
非捕获组经常使用在需要括号构成的正则表达式中，消除括号的影响。

```python
#如果不添加非捕获组，想要同时匹配create table和create external table，
#结果是捕获的括号内的内容，而不是完整字段。
>>>name = "create table,create external table"

>>>print(re.findall(r'create( external)? table',name))
['', ' external']

#添加非捕获组，则括号内的内容忽略，匹配完整字段
>>>print(re.findall(r'create(?: external)? table',name))
['create table', 'create external table']
```

#### re如何进行中文匹配？
re中的.符号，在默认模式，匹配除了换行的任意字符。如果指定了标签 DOTALL ，它将匹配包括换行符的任意字符。
```python
>>>print(re.match(r'.*','中国hellobike\n').group(0))
中国hellobike
```
正则表达式也支持中文组成的表达式。
```python
>>>print(re.match(r'[中国]*.*','中国hellobike\n').group(0))
中国hellobike
```

#### re中的高级语法：前视，后视？
re提供了一类高级语法，英语定义是：lookahead assertion， negative lookahead assertion，positive lookbehind assertion，negative lookbehind assertion。
lookahead和lookbehind，顾名思义，就是说在匹配字符时向前或向后查看。

* **(?=exp) 前视断言**（lookahead assertion。匹配exp的内容，比如， Isaac (?=Asimov) 匹配 'Isaac ' 只有在后面是 'Asimov' 的时候。
* **(?!exp) 前视取反** （negative lookahead assertion ）。匹配exp不符合的情况。比如说， Isaac (?!Asimov) 只有后面 不 是 'Asimov' 的时候才匹配 'Isaac ' 。
* **(?<=exp) 正向后视断定** （positive lookbehind assertion）。匹配字符串的当前位置，它的前面匹配exp的内容到当前位置。 (?<=abc)def 会在 'abcdef' 中找到一个匹配，因为后视会往后看3个字符并检查是否包含匹配的样式。包含的匹配样式必须是定长的，意思就是 abc 或 a|b 是允许的，但是 a* 和 a{3,4} 不可以。注意以 positive lookbehind assertions 开始的样式，如 (?<=abc)def ，并不是从 a 开始搜索，而是从 d 往回看的。这是应该使用 search() 函数，而不是 match() 函数。
* **(?<!exp) 后视断定取非** （negative lookbehind assertion）。匹配当前位置之前不是exp的样式。类似正向后视断定，包含的样式匹配必须是定长的。由 (?<!exp)开始的样式可以从字符串搜索开始的位置进行匹配。

```python
#使用后视断言的例子，想要匹配表名，即a和b，需要查看表名前是否有table关键字，
#这时使用后视断定。re的前和后的方向，是指相对当前字符向右和向左。如果向左看，
#就是向后看，因为左边的字符已经匹配过，是“后面的”。
>>>name = "create table a,create external table b,create index c"
>>>print(re.findall(r'(?<=table )(\w*)',name))
['a', 'b']
#如果使用前视断言，断言内容为非捕获组，所以匹配到的内容是table
>>>print(re.findall(r' table (?=[a|b])',name))
[' table ', ' table ']
```

#### 贪婪与非贪婪是什么？

.* 表达式执行的是贪婪模式，例如：
```python
>>> s = '<html><head><title>Title</title>'
>>> len(s)
32
>>> print(re.match('<.*>', s).span())
(0, 32)
>>> print(re.match('<.*>', s).group())
<html><head><title>Title</title>
```
如果希望以最短模式匹配，则需要开启非贪婪模式。
解决方案是使用非贪婪的限定符 ***? 、 +? 、 ?? 或 {m,n}?*** ，匹配尽可能 少 的文字

```python
>>> print(re.match('<.*?>', s).group())
<html>
```



