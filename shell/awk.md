## 基本架构

~~~shell
awk 'BEGIN{}{}END{}' datafile
#其中BEGIN{}表示执行循环之前所要执行的命令
#{}内表示每一行所执行的命令
#END{}表示处理完所有行后执行的命令

-F fs #指定文件分隔符
-f scriptfile #读取指定文件中的awk命令执行
-v var=value #变量赋值
~~~



## 内置变量

~~~shell
$n #当前行的第n个字段，一般是空格分隔
$0 #完整行
FNR #文件分别计数的行号
FS #字段分隔符
NF #一条记录的字段数目
NR #行号，当前记录数
IGNORECASE #如果为真，则进行忽略大小写的匹配
~~~





## 条件语句

~~~shell
if ()
{}
else if ()
{}
else
{}

#运算符同
#||, &&, !, ~, !~, >, >=, <, <=, =, !=, ==, =-*/%, in, 
~~~



## 输入输出问题

~~~shell
printf("%s %d",var1,var2)
#按照指定格式输出变量到屏幕
#其中var1输出为字符串，var2输出为数字
~~~





## 循环语句

~~~
~~~





## shell与awk的变量交换问题

~~~shell
#awk->sell 
eval $(awk '')
eg: eval $(awk '{pringf("var1=%s;var2=%s;var3=%s;",$1,$2,$3)}' input.txt)
	#它会在屏幕上打印'var1=$1;var2=$2;var3=$3'然后使用eval语句表达式化
#shell->awk
"'$var'"
'"$var"'
"'"$var"'"
awk -v awk_var="$var"

~~~



