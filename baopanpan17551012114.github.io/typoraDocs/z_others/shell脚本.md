http://c.biancheng.net/view/1120.html

https://www.runoob.com/linux/linux-shell-array.html



https://blog.csdn.net/w918589859/article/details/108752592

# Shell 变量

定义变量：***变量名和等号之间不能有空格***

```shell
your_name="runoob.com"
```

使用变量：***在变量名前面加美元符号即可***

```shell
your_name="qinjx"
echo $your_name
echo ${your_name}
```

Bash let 命令，它用于执行一个或多个表达式，变量计算中不需要加上 $ 来表示变量，具体可查阅：[Bash let 命令](https://www.runoob.com/linux/linux-comm-let.html)

```shell
#!/bin/bash
j=0
for i in {0..60}
do
  let j=i*3000000
  sh output_action_with_storeid.sh $j
  echo $j
done
```



# Shell 传递参数

可以在执行 Shell 脚本时，向脚本传递参数，脚本内获取参数的格式为：**$n**。**n** 代表一个数字，1 为执行脚本的第一个参数，2 为执行脚本的第二个参数，以此类推……，其中 **$0** 为执行的文件名（包含文件路径）

```shell
#!/bin/bash

echo "Shell 传递参数实例！";
echo "执行的文件名：$0";
echo "第一个参数为：$1";
echo "第二个参数为：$2";
echo "第三个参数为：$3";
```



# Shell 数组

Shell 是弱类型的，它并不要求所有数组元素的类型必须相同

```shell
#!/bin/bash

my_array=(A 1 "C" D)

echo "第一个元素为: ${my_array[0]}"
echo "第二个元素为: ${my_array[1]}"
echo "第三个元素为: ${my_array[2]}"
echo "第四个元素为: ${my_array[3]}"

echo "数组的元素为: ${my_array[*]}"
echo "数组的元素为: ${my_array[@]}"

echo "数组元素个数为: ${#my_array[*]}"
echo "数组元素个数为: ${#my_array[@]}"
```



# Shell 字符串

```shell
str1=c.biancheng.net
str2="shell script"
str3='C语言中文网'
```

基本形式：

1) 由单引号`' '`包围的字符串：

- 任何字符都会原样输出，在其中使用变量是无效的。
- 字符串中不能出现单引号，即使对单引号进行转义也不行。


2) 由双引号`" "`包围的字符串：

- 如果其中包含了某个变量，那么该变量会被解析（得到该变量的值），而不是原样输出。
- 字符串中可以出现双引号，只要它被转义了就行。


3) 不被引号包围的字符串

- 不被引号包围的字符串中出现变量时也会被解析，这一点和双引号`" "`包围的字符串一样。
- 字符串中不能出现空格，否则空格后边的字符串会作为其他变量或者命令解析。

```shell
#!/bin/bash
n=74
str1=c.biancheng.net$n 
str2="shell \"script\" $n"
str3='C语言中文网 $n'
echo $str1
echo $str2
echo $str3
```

***获取字符串长度***：

${#string_name}

```shell
str="http://c.biancheng.net/shell/"
echo ${#str}
```

***字符串拼接：***

```shell
#!/bin/bash
name="Shell"
url="http://c.biancheng.net/shell/"
str1=$name$url  #中间不能有空格
str2="$name $url"  #如果被双引号包围，那么中间可以有空格
str3=$name": "$url  #中间可以出现别的字符串
str4="$name: $url"  #这样写也可以
str5="${name}Script: ${url}index.html"  #这个时候需要给变量名加上大括号
echo $str1
echo $str2
echo $str3
echo $str4
echo $str5
```

***字符串截取：***

这里需要强调两点：

- 从左边开始计数时，起始数字是 0（这符合程序员思维）；从右边开始计数时，起始数字是 1（这符合常人思维）。计数方向不同，起始数字也不同。
- 不管从哪边开始计数，截取方向都是从左到右。

从左向右：${string: start :length}

从右向左：${string: 0-start :length}

```shell
url="c.biancheng.net"
echo ${url: 2: 9}
echo ${url: 2}  #省略 length，截取到字符串末尾

echo ${url: 0-13: 9}
echo ${url: 0-13}  #省略 length，直接截取到字符串末尾
```

从指定字符（子字符串）开始截取：

${string#*chars}

```shell
url="http://c.biancheng.net/index.html"
echo ${url#*:}
```

