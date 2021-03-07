# Linux操作基础

### 数据提取操作

##### 常用命令

| 命令  |       说明       | 命令  |         说明         |
| :---: | :--------------: | :---: | :------------------: |
|  cut  |       切分       | grep  |         检索         |
| sort  |       排序       |  wc   | 统计字符、字数、行数 |
| uniq  |       去重       |  tee  |      双向重导向      |
| split |     文件切分     | xargs |       参数代换       |
|  tr   | 替换、压缩和删除 |       |                      |

##### cut 

```shell
cut [-dfc] <file>
 -d c    #以c字符分割
 -f num  #显示num字段的内容
 -f num-  #显示num字段及其后的内容
 -f num1-num2  #显示num1字段到num2字段的内容
 -b num  #字节
 -c num  #字符
```

##### grep

```shell
grep [-acinv] <string> <file>
-c #统计搜索到的次数
-i #忽略大小写
-n #顺序输出行号
-v #反向输出
```

##### sort

```shell
-f #忽略大小写
-M #以月份名称排序
-n #以纯数字方式排序
-r #反向排序
-u #uniq
-t #分隔符，默认Tab
-k #以哪个区间排序
```

eg:

```shell
cat /etc/passwd | sort -t : -k 3 -n
last | grep -v "^$" | grep -v "begins" | cut -d " " -f 1 | sort -u -c
```

##### wc

```shell
-l #仅列出行号
-w #仅列出多少字
-m #仅列出多少字符
```

eg:统计当前目录下代码的行数

```shell
find . -name "*.*" -exec cat {} \; | wc -l
```

##### uniq

```shell
-i #忽略大小写字符的不同
-c #进行计数
```

##### tee

```shell
echo "Hello World" > 1.txt #只能将内容写进文件，但是不能在终端显示
echo "Hello World" | tee 1.txt #既能写进文件，又能在终端显示
```

#####  xargs

有些命令不支持管道命令，就是不接受管道前的标准输入，比如`ls`

这时可以使用xargs转换参数

```shell
echo ./code | xargs ls
```

##### tr

```shell
tr [cdst] <字符集> <字符集>
-c #取代所有不属于第一字符集的字符
-d #删除所有属于第一字符集的字符
-s #将连续重复的字符以单独一个字符表示
-t #先删除第一个字符集较第二个字符集多出的字符
```

eg:

```shell
echo "abcefg" | tr "abc" "123"      #Out:123efg
echo "abcefg" | tr -c "abc" "123"      #Out:abc123
echo "abcefg" | tr -d "abc"         #Out:efg
echo "abc111" | tr -s "abc" "1"      #Out:abc1
```

##### 词频统计

```shell
cat 1.c | tr -c -s "a-zA-Z" "\n" | sort | uniq -c | sort -n -r | head -n 50
```

eg:字符串中的数字计算

```shell
echo "1 2 3 4 5 6 ha ha 你好 ./ 3 12 abc64" | tr -s -c "0-9" " " | xargs | tr " " "+" | bc
#bc是计算机
echo $[`echo "1 2 3 4 5 6 ha ha 你好 ./ 3 12 abc64" | tr -s -c "0-9" " " | xargs | tr " " "+"`]
```

### Shell编程

##### 变量与变量定义

> shell是弱类型语言，不用声明数据类型，注意不要有多余的空格
>
> a=12
>
> a=helloworld
>
> a=\`pwd\`

##### 特殊变量

- 位置变量

1. $0 获取当前执行脚本shell脚本文件名（包括路径）
2. $n 获取当前脚本执行的第n个参数
3. $# 获取执行当前脚本的参数个数（不包括文件名）
4. $* 获取当前shell的所有参数

- 状态变量

1. $? 判断上一指令是否成功执行，0为成功，非0为不成功

##### 字符串展开

|             命令              |          作用          |
| :---------------------------: | :--------------------: |
|   ${string/string1/string2}   | 替换第一个匹配的字符串 |
|  ${string//string1/string2}   |  替换所有匹配的字符串  |
|  ${string/#string1/string2}   |  替换开头的匹配字符串  |
|  ${string/%string1/string2}   |  替换结尾的匹配字符串  |
| \${string , ,}　${string ^ ^} |    转换为小写和大写    |
|  \${string , }　${string ^}   | 转换为首字母小写和大写 |

##### 输入输出

> read [-option] [variaies]

```shell
-p "字符串"   #在输入之前打印一段字符串
-s           #隐藏输入
-t 时间　　　 #规定输入时间
```

##### 函数

```shell
function _printf_ {
	echo $1
	return 
}

_printf_() {
	echo $1
	return
}

function _printf_() {
	echo $1
	return
}

_printf_ "lls is 23 years old!"
```

##### 流程控制

```shell
if [[ condition ]]; then
	#statements
fi

if [[ condition ]]; then
	#statements
	else
		#statements
fi

if [[ condition ]]; then
	#statements
elif [[ condition ]]; then
	#statements
elif [[ condition ]]; then
	#statements
	else
	#statements
fi
```

##### 循环

```shell
num=100
while [[ $num -ge 0 ]]; do
	echo ${num}
	num=$[ ${num} - 1 ]
done
```

```shell
#第一种
for (( i = 0; i <= 100; i++ )); do
	echo ${i}
done

#第二种
for i in `seq 0 100`;do
	echo $i
done
```

```shell
sum=0
until [[ ${sum} -ge 100 ]]; do
	echo $sum
	sum=$[ $sum + 1 ]
done
```

##### 数组

```shell
name=(luan long sheng)
echo ${name[1]}       #数组索引值
name[2]=haha          #可以更改数组元素
echo ${name[@]}		　#输出所有元素
echo ${#name[@]}	　#统计数组元素个数
echo ${!name[@]}	　#输出数组元素下标
name += (1 2 3)		　#数组累加
unset name[1], unset name #删除数组元素或整个数组
```

### 用户管理

##### 用户管理的重要配置文件

|   配置文件   |                         内容                          |
| :----------: | :---------------------------------------------------: |
| /etc/passwd  | 用户名 密码位 用户编号 归属组编号 姓名 \$HOME  $shell |
| /etc/shadow  |        用户名 已加密密码 密码改动信息 密码策略        |
|  /etc/group  |            群组名 密码位 群组编号 组内用户            |
| /etc/gshadow |                   群组密码相关文件                    |
| /etc/sudoers |                 用户名 权限定义 权限                  |

```shell
#更改用户的shell
cash -s shell <username>
eg:
cash -s /bin/bash luanlongsheng
```

##### 用户管理相关的命令

|  命令   |       说明        |  命令   |        说明        |
| :-----: | :---------------: | :-----: | :----------------: |
|   su    |     切换用户      |  sudo   | 临时切换为root用户 |
| passwd  |   切换用户密码    | gpasswd |    设定群组密码    |
| useradd | 新建用户(adduser) | usermod |    修改用户账号    |
|   id    |   显示用户信息    |         |                    |

> sudo -i  //切换到root

> passwd　[-dleSxnf]  <username>
>
> -d :清楚密码
>
> -l :锁定用户

> useradd [-dmMsugGnefcD] <username>

> user -r <username>

### 进程管理

##### 进程管理相关的命令

|  命令  |           说明            | 命令  |                 说明                  |
| :----: | :-----------------------: | :---: | :-----------------------------------: |
|  free  |  打印系统情况和内存情况   |  top  | 显示当前系统进程情况，内存、CPU等信息 |
| dstat  | 实时监控磁盘，CPU，网络等 |  ps   |           报告当前进程状态            |
| pstree |  以树状显示进程派生关系   | pgrep |              查找进程ID               |
|  kill  |  删除执行中的程序和工作   | pkill |        批量按照进程名杀死进程         |

- free

> -h 查看内存（G）
>
> -m 查看内存（M）
>
> -s 2 间隔2s输出一次

- top

> 了解top中的各部分含义

- ps

> ps -aux 
>
> ps -ef 两个命令用法一致，常搭配grep使用

- pgrep

> pgrep [-onlpgtu] <进程名>
>
> -o 起始进程号
>
> -n 结束进程号
>
> -l 显示进程名称

- kill

> kill -9 pid 强制杀死进程
>
> kill pid 杀死进程

### 任务管理

##### 任务管理相关的命令

| 命令  |              说明              |
| :---: | :----------------------------: |
| cron  |            定时任务            |
|  at   | 在指定的时间执行一个指定的任务 |
| sleep |              睡眠              |

- cron定时任务

> crontab -e 编辑
>
> crontab -l 显示列表

- at

> at 20:00
>
> echo "Hello pjy!"
>
> ctrl + D
>
> at -l 查看任务
>
> at -c 任务号

##### linux中的小技巧

1. 按列插入

	- ctrl + v进入可视化块

	- 按shift+i按列插入

	- 输入插入内容

	- 按esc退出

2. 按列删除

	- ctrl + v进入可视化块

	- 选中删除内容

    - 输入x删除

3. 批量修改

   普通模式下键入：

```
:%s/原字符串/目标字符串
```

