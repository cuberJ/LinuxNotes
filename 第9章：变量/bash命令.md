# bash命令

## bash变量

### 存储内容

1. <font color = red>存储的形式均为字符串</font>，且可被修改

2. 变量名命名规则：

   1. 第一个字符为字母
   2. 其他字符可以为数字和下划线或者字母

   

3. 赋值语句：

   ```shell
   addr=20.1.1.234
   ```

   赋值作为一条单独的命令，且等号两侧<font color = red>***不允许有多余的空格***</font>，带有空格，会被认为执行了一个叫做addr的指令，且参数为 `= 20.1.1.234`

   

4. 引用变量

   `$addr` 或者 `${addr}`

   ```shell
   ftp $addr     # 相当于执行了ftp 20.1.1.234这个指令
   ```

   如果要直接在某个变量后加上一个字符（比如在addr后加上字符a），为了防止被误解读为`$addra`,应写为
   
```shell
   echo ${addr}IPv4  # 相当于打印了变量addr中的字符串20.1.1.234`并在结尾添加了字符串IPv4
```

   

   赋值的时候，如果等号右侧字符串中含有特殊字符，应采用双引号括起整个字符串

   ```shell
   unit="beijing university"
   ```



5. 引用未定义的变量，变量值是空字符串。
   1. `set -u`指令，使引用一个未定义的变量产生报错
   2. `set +u`指令，使未定义的变量为空字符串



### echo指令

打印输入的参数以及转义字符，但在不同的Unix系统之间的兼容性差

```shell
echo arg1 arg2 arg3……
# 打印各个命令行参数，每两个之间用一个空格分开，最后打印一个换行符
```

如果需要打印的字符串之间存在大量的空格，且需要体现出这一点，需要对打印的字符串用双引号括起来

```shell
echo Beijing       China  
# 虽然有很多空格，但打印的结果只包含一个:
# Beijing China
echo "Beijing      China"
# 打印结果为：Beijing      China
```



#### echo的用途

用于检查赋值变量的值以及打印不可打印字符（后者在Linux下需要使用-e）

```shell
echo -e '\033test'  # 最后的打印结果是est，因为test会被之前的转义字符删除
```



## 脚本中编辑文件

### read指令（内部指令）

读取用户的输入并使用输入的信息作为变量

```shell
 ~ read test
fortest
 ~ echo $test
fortest
```



## 环境变量

<font color = red>默认创建的shell变量都是局部变量</font>，可以通过export将某个变量转为环境变量

```shell
export proto  # 将把proto转为环境变量
```



#### 环境变量的作用

1. shell启动的子进程只继承环境变量（深拷贝）

2. 子进程对环境变量的修改，不影响父进程中的同名变量

#### 系统的环境变量

1. HOME：用户主目录的路径名

2. PATH：给出命令查找的路径

   ```shell
   PATH=/bin:/usr/bin:/etc
   # 系统会根据PATH自动在/bin   /usr/bin   /etc三个目录下依次寻找可执行文件
   ```

3. TERM：终端类型

   

#### 查看系统环境变量的指令：env或者set

## 替换

### Shell替换

#### 文件名生成

遵循文件通配符规则，按照字典顺序排列：

```shell
ls *.c  # 实际执行的是 ls a.c b.c x.c
```



#### 变量替换

```shell
ls $HOME => ls /home/jiang
```



#### 命令替换（反撇号:与键盘上波浪线~是同一个按键）

```shell
now=`date`
```

date是一个可执行文件，将这个文件的<font color = red>输出结果</font>直接赋值给变量now

\`date\`实质上是将两个反撇号之间的可执行文件的结果，直接作为一个参数进行运算

```shell
~: now=`date +"%Y-%m-%d-%H-%M"`
~: mv order.txt `whoami`-${now}.txt
# 执行结果：将order.txt文件名替换成了crj-2020-06-08-10-08.txt
# 注意，不能在同一个选项内同时出现两组反撇号，否则系统会对反撇号的判定混乱。即：不能写成如下形式：
mv order.txt `whoami`-`date`.txt
```



与反撇号有相似替代效果的命令：`$()`



### Shell内部变量

1. `$0`    脚本文件自身的名字
2. `$1 $2 ……`    分别表示1号，2号……的命令行参数
3. `$#`   命令行参数的个数
4. `"$*"`    等同于"$1 $2 $3……"，把所有的参数捆绑在一起作为一整个字符串进行传递
5. `"$@"`    等同于"$1"   "$2"  "$3" …… ，将所有参数分开，独立的作为参数进行传递

## Shell元字符（特殊字符）

### 常见元字符

1. 空格，制表符tab：命令行参数的分隔符
2. 回车：执行命令
3. `<>|` :重定向和管道
4. `；`：一行输入多个命令
5. `&` ：后台运行（./abc &  :abc这个程序将会在后台运行）
6. `$` :引用变量
7. `*[]?~  ` ：文件通配符
8. `() `  :定义shell函数
9. `/`:文件路径分隔符

<font color = red>注意：Shell的元字符里不包含圆点`.`,和正则式的元字符不同</font>

shell特有的元字符：`&|<>;$()~  以及反撇号`

通用的元字符：`*[]?`



### 转义符号

#### 反斜杠

取消元字符的特殊作用（一个反斜杠只能取消一个元字符）

如果反斜杠加在非元字符前，则相当于没有输入反斜杠

#### 双引号

能够取消几乎所有的元字符的特殊含义

只有 $ ，双引号自身，反斜杠和 反撇号 ` 的特殊含义保留

#### 单引号

完全取消所有的元字符的含义，不允许两个单引号之间带有单引号



#### 运用

```shell
grep "test*" main.c  # 在main.c中寻找符合正则式的句子。由于采用了双引号，所以取消了双引号之间的*的含义，shell在匹配时不会识别出这个*，也就不会做文件匹配
```

```shell
echo "My Home dir is $HOME"
echo 'MY Home dir is $HOME' 
打印结果如下：
My Home dir is /Users/cairenjie
MY Home dir is $HOME
```

```shell
# 目的：打印字符串  Don't remove Peter's Windows dir "C:\PETER"!
echo  Don\'t remove Peter\'s Windows dir '"C:\PETER"!' 
```

```shell
echo "`whoami`'s \$HOME is \"$HOME\""
# 打印结果
cairenjie's $HOME dir is "/Users/cairenjie"
```



# Shell 的逻辑判断

## 条件判断

### 返回码

判定指令是否执行成功，是通过进程的返回值是否为0来判断。为0表示执行成功

查看返回码的方式：echo

```shell
echo $?  # $?表示上一个命令的返回码，为内部变量

# 以下为实例：
~： echo $?
0
~： ls main
ls: main: No such file or directory
~： echo $?
1
```

如果是用管道的方式连接在一起的命令，则<font color = red>以最后一个命令的返回码</font>为最终返回码



### 复合逻辑

使用`&&`或者`||`来连接两个命令

通过复合逻辑，可以使用<font color = red>短路计算</font>来实现简单条件

```shell
cmd1 && cmd2  # 如果cmd1执行成功，才执行cmd2，否则直接忽略cmd2
cmd1 || cmd2  # 如果cmd1执行失败，才执行cmd2，否则直接忽略cmd2
```

系统中的命令`/bin/true`和`/bin/false`的返回码，前者总为0，后者总不为0

```shell
~: ls main || true
ls: main: No such file or directory
~: echo $?
0
```



## test指令与方括号

`[`指令要求命令行参数的最后一个参数必须为`]`

除此之外，方括号和test指令的功能完全相同

```shell
test -r /etc/motd
[ -r /etc/motd ]
# 两个指令的效果完全一致
```



#### 功能

test执行的是判断功能，由于shell中if只是一个标识符，不是一个可执行文件，所以需要使用test进行实际的判断功能



#### 使用

##### 字符串比较

``` shell
str1 = str2  # 注意这里的等号两侧必须要有空格
str1 != str2
```

```shell
➜  test "$now" = "2020-06-08-10-08"  # 注意，进行字符串比较的时候，变量两侧必须加双引号
➜  echo $?
0
```

<font color=red>等号两侧带有空格表示判断，没有空格表示赋值</font>

## 命令组合

使用花括号`{}`将多个执行语句组合在一起，视为一个执行的整体

```shell
test "$number" = "0" && { echo NUMBER IS ZERO; ls -a}  # 注意左边的花括号后面必须有一个空格
# 执行结果如下：
NUMBER IS ZERO
.                              cairenjie-2020-06-08-10-08.txt test.c
..                             path.log                       test1.c
```

使用圆括号`()`的话，效果类似，但执行的方式不一样

花括号是在当前shell中执行一组命令，而圆括号是在子shell中执行一组命令



## if条件

### 结构

```shell
if condition1
	then list1
elif condition2
	then list2
else
	list3
fi
```

<font color = red>if,then,elif,else,fi均为关键字，必须要放在一行的开头，否则无法识别</font>



## case结构

```shell
case word in
pattern1) list1;;
pattern2) list2;;
esac
```

1. `;;`是一个整体，中间不能加空格
2. 与case进行匹配的`word`采用的是shell匹配模式，而不是正则匹配模式
3. 可以通过`|`来表示多个模式



## 表达式运算

### expr命令

1. 运算包括如下几种：
   1. 括号：`()`
   2. 五种算术运算：`+-*/%`
   3. 关系运算：`> >= < <= = !=`
   4. 逻辑运算：`| &`
   5. 正则表达式运算：`:`

2. 等号两侧对空格的要求：<font color=red>等号两侧带有空格表示判断，没有空格表示赋值</font>

实例：

```shell
# 执行语句x=a*(b+c)，abc均为变量
x=`expr $a \* \($b + $c \)`
# 注意要对shell中同名的元字符取消转义
```



3. 正则表达式运算

   1. 用法：

      ```shell
      expr string : pattern
      # 用pattern匹配字符串，打印匹配的长度
      # pattern中用\(和\)括起一部分，能匹配时打印括号内能匹配的部分，否则为空字符串
      ```

      

   2. 具体使用：

      ```shell
      str=`ping 192.168.1.1 | grep from`
      # 接下来从提取出的句子中抽取time=0.68ms的信息
      expr "$str" : '^.* time=\([0-9.]*\) ms$'
      ```

      

## while循环

### 结构

```shell
while condition
	do list
done
```

注意点：

1. do如果和while写在一行里，需要带上分号：

   ```shell
   while condition; do list;done
   ```



## for循环

### 结构

```shell
for name in word1 word2……  # 每次循环依次从word里取
do list
done
```

还有一种表示方法为：

```shell
for name  # 相当于 for name in $1 $2 ……，即从命令行参数中获取循环的参数
 do list
done
```

还可以通过seq指令实现循环：

```shell
for i in `seq 1 254`
		do
			ping 202.10.1.$i
		done
```

如果需要遍历文件，则在for的参数中采用shell的文件匹配模式即可



### 循环控制

#### break

```shell
break $n  #打破n层循环
```

#### exit

结束脚本的执行，参数为该进程执行结束后，希望返回的返回码

```SHELL
exit 1
```



## 函数

### 语法

```shell
name() { list1; list2;}  #函数声明，将list1,list2两个指令作为一个整体，以后将会被叫做name的函数调用
```

### 参数

调用时，函数名后附加上0到多个参数

在函数体内，可以通过$1 $2或者$*, $@的方式引用

## 内部命令eval

功能：将输入的字符串或者程序执行的结果作为执行命令

