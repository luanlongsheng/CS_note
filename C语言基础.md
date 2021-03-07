# C语言基础

### 1.printf家族

##### stdin stdout stderr

> 它们三个存储的是三个地址，指向文件类型的指针

- stdin: 标准输入
- stdout: 标准输出
- stderr: 标准错误输出

> scanf输入流中没有数据时，将会返回EOF=-1

##### sprintf

> 可以做拼接字符串的操作

```c
char str[100];
sprintf(str, "%03d", n);
printf("str=%s\n", str);

In:5
Out:str=5
```

例1.拼接IP地址

```c
char str[100];
sprintf(str, "%d.%d.%d.%d", 192, 168, 1, 10);
printf("str=%s\n", str);

Out:str=192.168.1.10
```

##### fprintf

> 向文件中输出内容

```C
char str[100] = "i love pjy!"
FILE *fout = fopen("output", "w");
//第一个参数代表文件路径名，第一个参数代表要执行的操作`w`代表写操作，`r`代表读操作
fprintf(fout, "%s\n", str);
//stdout也是一个文件指针,所以也可以通过fprintf将内容打印到stdout中，也就是打印在终端上
//stderr标准错误输出，也可以通过fprintf将内容打印出来
//./a.out > output 将会将标准输出打印到指定文件中

Out:在文件output中,成功写入 i love pjy
```



### 2.数学运算

##### inttypes.h

> 在不同的编译环境中int占的字节数不一样，可以使用inttypes.h中的一些数据类型，它们在不同的编译环境中所占的位数统一，增强代码的可移植性

```c
int8_t  INT8_MIN INT8_MAX 代表8位整型的最小值和最大值
int16_t INT16_MIN INT16_MAX
int64_t INT64_MIN INT64_MAX
    
输出的格式化占位符分别为：
PRId8---代表十进制
PRId16
PRId64
PRIx8---代表16进制
PRIx16
PRIx64
PRIo8---代表8进制
PRIo16
PRIo64
```

例2：输出64位整型

```C
#include <stdio.h>
#include <inttypes.h>

int main() {
    int64_t a = 123;
    printf("%" PRId64 "\n", a);
    printf("%" PRId8 "%" PRId8 "\n", INT8_MIN, INT8_MAX);
    return 0;
}

Out: 123
    -128 127
```

##### 左值和右值

> 左值和右值的区分可以看程序在运行到下一行时，这个值还是否可以访问的到来判断

```c
int a, b = 3, c = 2, d = 3, e = 4;
b = (++c);//这里面的b和c均为左值，因为b,c的值在下一行都可以访问到
a = (++(d + e));//(d + e)为右值，因为(d + e)将产生的中间变量赋值给a，在程序的下一行无法访问到
```

##### 运算符优先级

[运算符优先级](https://baike.baidu.com/item/%E8%BF%90%E7%AE%97%E7%AC%A6%E4%BC%98%E5%85%88%E7%BA%A7/4752611?fr=aladdin)

##### 位运算

> ~ : 按位取反，将每一位的0,1取反
>
> ! : 取非，只返回0,1两个结果

例3：循环读入值

```c
while(~scanf("%d", &a)){}//使用~可以实现循环读入，直到EOF(-1)停止，因为只有-1按位取反后的值为0
//相当于while(scanf("%d", &a) != EOF);
```

> ^  : 异或运算

例4：

```c
int main() {
	int a = 5, b = 6;
	int c = a ^ b;
    printf("c = %d\n", c);
	printf("c ^ b = %d\n", c ^ b);
	printf("c ^ a = %d\n", c ^ a);
	printf("a ^ b = %d\n", a ^ b);
	reutrn 0;
}

Out:c = 3 
	c ^ b = 5
    c ^ a = 6
    a ^ b = 3
//通过结果可以看出，由两个数取亦或得到第三个数，则三个数任意两个数取亦或可以得到另一个数
```

### 3.函数

##### 递归函数

> 程序调用自身的==编程技巧==叫递归

递归的组成部分：

- 边界条件处理
- 针对问题的处理过程和递归过程
- 结果返回

> scanf("%d", &n);//取地址符目的是允许传入的变量进行更改

函数递归类似数学归纳法的思想，如果通过f(n)成立可以推出f(n+1)成立，又验证f(0)成立，则n等于任意值均成立。

##### 函数指针

存储函数地址的指针变量为函数指针

```c
void g(int (*f)(int));//*f指向函数的指针，前后的int代表函数的传入传出参数
```

对分段函数十分友好

```c
int g(int (*f1)(int), int (*f2)(int), int (*f3)(int), int x) {
	if (x < 0) {
	return f1(x);
	}
	if (x < 100) {
		return f2(x);
	}
	return f3(x);
}
```

例题：

![](/home/luanlongsheng/图片/C++/2020-10-22 13-38-43屏幕截图.png)

```c
#include <stdio.h>
#include <stdlib.h>
#include <inttypes.h>

int64_t Triangle(int64_t n) {//三角数
    return n * (n + 1) / 2;
}

int64_t Pentagonal(int64_t n) {//五角数
    return n * (3 * n - 1) / 2;
}

int64_t Hexagonal(int64_t n) {//六角数
    return n * (2 * n - 1);
}

int64_t binary_search(int64_t (*func)(int64_t), int64_t x) {//二分法
    int64_t head = 1, tail = x, mid;
    while (head <= tail) {
        mid = (head + tail) >> 1;
        if (func(mid) == x) return mid;
        if (func(mid) < x) head = mid + 1;
        else tail = mid - 1;
    }
    return -1;
}

int main() {
    int n = 144;
    for (;;n++) {
        if (binary_search(Triangle, Hexagonal(n)) == -1) continue;
        if (binary_search(Pentagonal, Hexagonal(n)) == -1) continue;
        printf("Hexagonal(%d) = " "%" PRId64 "\n", n, Hexagonal(n));
        break;
    }
    return 0;
}

Out :Hexagonal(27693) = 1533776805 
```

##### 变参函数

> 像scanf,printf等函数可以传入任意多的参数，这类函数被称作是变参函数

```c
int max_int(int a, ...);//...代表可以传入任意多的参数
```

> 获取变参函数参数列表中的相应值，可以通过va一族工具，包含于`stdarg.h`

|  va一族  |              作用              |
| :------: | :----------------------------: |
| va_list  |      获取a往后的参数列表       |
| va_start |   定位a后面第一个参数的位置    |
|  va_arg  | 获取下一个可变参数列表中的位置 |
|  va_end  | 结束整个获取可变参数列表的动作 |

```c
#include <stdio.h>
#include <stdlib.h>
#include <stdarg.h>
#include <inttypes.h>

int max_int(int n, ...) {
    if (n <= 0) return 0;
    va_list arg;//定义一个可变参数列表
    va_start(arg, n);//获取n后面第一个参数的位置
    int32_t ans = INT32_MIN;
    for (int i = 0; i < n; i++) {//求最大值
        int32_t temp = va_arg(arg, int);
        if (temp > ans) ans = temp;
    }
    return ans;
}

int main() {
    printf("%d\n", max_int(3, 1, 5, 3));
    printf("%d\n", max_int(3, 1, 5, 3, 7));
    printf("%d\n", max_int(2, 3, 7));
    return 0;
}

Out:5
    5//虽然传入的可变参数是4个，但是由于第一个传入的参数是3，故只循环3次，即不能遍历到7
    7
```

##### 欧几里得算法

> - 又名辗转相除法
>- 迄今为止最古老的算法
> - 用于快速计算两个数字的最大公约数
> - 还可以用于快速求解 `a*x+b*y=1`方程的一组整数解

> a和b的最大公约数＝b和a%b的最大公约数

证：

 ​		gcd(a, b) = c

 ​		a = x * c,  b = y * c 且 gcd(x, y) = 1

 ​		a % b = a - k * b = x * c - k * y * c = (x - k * y) * c

 ​		由此得c是b和a%b的公约数

​		下面证明c是最大公约数：

​		设：gcd(y, x - k*y) = d

​		y = n * d, x - ky = m * d

​		x = m * d + k * n * d = (m + k * n) * d 

​		这时可以看出x和y有公因数d，又因为gcd(x, y) = 1,故d=1

​		即c也是b与a%b的最大公约数

```c
#include <stdio.h>

int gcd(int a, int b) {
    return (b ? gcd(b, a % b) : a);
}

int main() {
    int a, b;
    while (~scanf("%d%d", &a, &b)) {
        printf("%d\n", gcd(a, b));
    }
	return 0;
}
```

​		

### 4.数组基础知识

```c
int a[100] = {0} //给数组中的每一个数全都设为0
```

> 数组连续存储，可以根据下标索引值

> 数组可以做整型(下标)到任意类型(数组元素)的映射
>
> 函数可以做任意类型到任意类型的映射

##### 素数筛

```c
#include <stdio.h>
#include <stdlib.h>

#define max_n 100
int prime[max_n + 5] = {0};

void is_prime() {
    for (int i = 2; i <= max_n; i++) {
        if (!prime[i]) prime[++prime[0]] = i;
        for (int j = i; j <= max_n; j += i) {
            prime[j] = 1; 
        }
    }
}

int main() {
    is_prime();
    for (int i = 1; i <= prime[0]; i++) {
        printf("%d\n", prime[i]);
    }
    return 0;
}

```

##### 数组

> int arr[100];
>
> arr就代表arr[0]的地址，arr+1代表arr[1]的地址
>
> 每移动一位，向后移动所指向类型大小的字节数
>
> int类型每次加１向后跳动４个字节，char类型每次加１向后跳１个字节，每个字节都有对应的地址

> 地址的格式化占位符为%p
>
> ```c
> int arr[100]
> printf("arr[0] = %p\n", &arr[0]);
> ```

传出参数

```c
int func(int a) {//形参
	a = 5;
}

int main() {
    int n = 0;
	func(n);//实参
    printf("%d\n", n);
	return 0;
}

Out: 5 //改变形参的值并不会改变形参的值

//若想在函数内部改变实参的值，则需要引入指针变量，存储变量的地址。这样改变地址里的值，就会使传入的值发生变化，这类参数被称为是传出参数

int func(int *a) {
    *a = 5;
    return 0;
}

int main() {
    int n = 0;
    func(&n);
    printf("%d\n", n);
    return 0;
}

Out: 5
    
//这也正是为什么scanf要传入地址的原因啦
```

```c
//数组表示的几种等价形式
arr + i ; 取值*(arr + i)
&arr[i]; arr[i]
```

函数中传入数组作为参数

```c
//一维数组
int func(int *a) {}
func(arr);
int func(int n, int a[n]) {}
func(100, arr);
//二维数组
int func(int (*a)[200]) {}
int arr[100][200];
func(arr);
//三维数组
int func(int (*a)[200][300]) {}
int arr[100][200][300]
func(arr);

//函数在传递数组时只能省略第一维，否则会使a[1] != arr[1]
```

##### 二分查找法

```c
int binary_search(int *arr, int x, int n) {
    int head = 0, tail = n - 1, mid;
    while (head <= tail) {
        mid = (head + tail) >> 1;
        if (arr[mid] == x) return mid;
        if (arr[mid] < x) head = mid + 1;
        else tail = mid - 1;
    }
    return -1;
}

//函数是压缩了的数组，数组是展开了的函数
//函数问题是给y值求取x值，y=f(x);数组是给了元素值，求下标
>　函数 y = x ^ 2
> 数组　1 2 3 4  5
> 　　　1 4 9 16 25

int binary_search(int (*arr)(int), int x, int n) {
    int head = 0, tail = n - 1, mid;
    while (head <= tail) {
        mid = (head + tail) >> 1;
        if (arr(mid) == x) return mid;
        if (arr(mid) < x) head = mid + 1;
        else tail = mid - 1;
    }
    return -1;
}
```

### 5.预处理命令

> 把公有的东西封装起来

##### 几种常见的预处理命令

```c
//宏定义
#define PI 3.1415926
//定义傻瓜表达式, 只做简单的替换
#define MAX(a, b) (a) > (b) ? (a) : (b)
//定义代码段
#define P(a) {\
	printf("%d\n", a);\
}
```

##### 预定义的宏


|       预定义的宏        |          说明           |
| :---------------------: | :---------------------: |
|       __ DATA __        |    日期：M mm dd yy     |
|       __ TIME __        |     时间：hh:mm:ss      |
|       __ LINE __        |          行号           |
|       __ FILE __        |         文件名          |
|       __ func __        |      函数名/非标准      |
|       __ FUNC __        |      函数名/非标准      |
| __ PRETTY _ FUNCTION __ | 更详细的函数信息/非标准 |

##### 条件式编译

> 一般用于根据不同环境去编译不同代码

|       函数       |        说明         |
| :--------------: | :-----------------: |
|  #if_def DEBUG   |  是否定义了DEBUG宏  |
|  #ifndef DEBUG   |  是否没定义DEBUG宏  |
|  #if MAX_N == 5  |  宏MAX_N是否等于５  |
| #elif MAX_N == 4 | 否则MAX_N是否等于４ |
|      #else       |                     |
|      #endif      |                     |

例题：实现没有Bug的MAX(a, b)宏

```c
#include <stdio.h>
#include <stdlib.h>

#define MAX(a, b) ({\　　//'\'用于链接
    __typeof(a) _a = (a);\　// __typeof()用于生成和其中元素相同的数据类型
    __typeof(b) _b = (b);\
    _a > _b ? _a : _b;\
})

#define P(a) {\
    printf("%s = %d\n", #a, a);\　//#a可以将任意类型转换为字符串类型
}

int main() {
    int a = 7;
    P(MAX(2, 3));
    P(5 + MAX(2, 3));
    P(MAX(2, MAX(3, 4)));
    P(MAX(2, 3 > 4 ? 3 : 4));
    P(MAX(a++, 6));
    P(a);
    return 0;
}

//通过gcc -E可以将宏展开
```

例题：实现一个打印LOG的函数，需要输出所在函数及行号等信息

> 变参宏

```c
#define log(frm, args...) { \ //args可以任意取名字 
	printf("[%s : %d] ", __func__, __LINE__);\
	printf(frm, args);\
}
```

```c
#include <stdio.h>

#define log(frm, args...) {\
    printf("[%s : %d] ", __func__, __LINE__);\
    printf(frm, ##args);\ //##代表将前后进行一个连接
    printf("\n");\
} 

int main() {
    int a = 7;
    log("%d", a);
    log("hello world");
    return 0;
}
```

##### 字符串讲解

> 定义字符数组：char str[size]

```c
//初始化字符数组
char str[] = "hello world";
char str[size] = {'h', 'e', 'l', 'l', 'o'};
```

> 字符串数组都是以'\0'结尾的，它的下标代表元素的个数

字符串相关操作：

- 头文件：string.h

|          函数          |                             说明                             |
| :--------------------: | :----------------------------------------------------------: |
|      strlen(str)       |               计算字符串的长度，以\0作为结束符               |
|   strcmp(str1, str2)   |                          字符串比较                          |
|   strcpy(dest, src)    |                          字符串拷贝                          |
| strncmp(str1, str2, n) |                       安全的字符串比较                       |
| strncpy(str1, str2, n) |                       安全的字符串拷贝                       |
| memcpy(str1, str2, n)  |                           内存拷贝                           |
| memcmp(str1, str2, n)  |                           内存比较                           |
| ==memset(str1, c, n)== | 内存设置(str1代表首地址， c代表设置的值， n代表连续设置的字节数) |

```c
int arr[100];
memset(arr, 0, sizeof(arr)); //将数组中每个元素设置为0
memset(arr, -1, sizeof(arr)); //将数组中每个元素设置为-1
```

- 头文件：stdio.h

|            函数            |         说明         |
| :------------------------: | :------------------: |
| sscanf(str1, format, ...)  | 从字符串str1读入内容 |
| sprintf(str1, format, ...) |  将内容输出到str1中  |

例题：使用字符串相关操作方法，计算一个16进制表示的位数

```c
#include <stdio.h>
#include <string.h>

int main() {
    int n;
    char str[10];
    while (~scanf("%d", &n)) {
        sprintf(str, "%x", n);
        printf("%s has %lu digits\n", str, strlen(str));
    }
    return 0;
}
```

### 6.复杂结构与指针

##### 结构体

> 结构体每次申请以结构体中最大字节的元素为基本开辟空间

```c
struct person {
    char name[20];//姓名
    int age;	  //年龄
    char gender;  //性别
    float height; //身高
};
```

```c
struct node1 {//占用8字节
	char a;
	char b;
	int c;
};

struct node2 {//占用12字节
	char a;
	int c;
	char b;
};
```

##### 共用体

```c
union register { //共同占用4个字节
	struct {
		unsigned char byte1;
		unsigned char byte1;
		unsigned char byte1;
		unsigned char byte1;
	} bytes;
	unsigned int number;
};
```

##### 指针与地址

> 地址即是一个元素的所在位置，例如一个整型数组的地址是它第一个元素所在的位置

> 指针变量也是变量，它存储的是地址；任何类型的指针变量都占8字节（64位系统）

```c
int a;
int *p = &a; //执行整型的指针变量
```

等价形式转换

> - *p = a（原始变量）
> - p + 1 = &p[1]
> - p->filed = (*p).filed = a.filed   //这里p为结构体指针变量

##### 函数指针

```c
int (*add)(int, int);
typedef int (*add)(int, int); //函数指针类型
```

##### typedef 的用法

> 定义类型别名，一般格式为==typedef 原类型名 类型别名==
>
> 作用：将变量变成类型

- 内建类型的重命名

```c
typedef long long lint;
typedef char * pchar;
```

- 结构体类型的重命名

```c
typedef struct __node {
	int x, y;
} Node, *PNode;
```

- 函数指针的命名

```c
typedef int (*func)(int);
```

> define只是做简单的替换，typedef是定义了一个类型

```c
#define char * pchar
typedef char * ppchar;

pchar p1, p2;
ppchar p3, p4;

//输出所占字节数
printf("sizeof(p1) = %lu, sizeof(p2) = %lu\n", sizeof(p1), sizeof(p2));
printf("sizeof(p3) = %lu, sizeof(p4) = %lu\n", sizeof(p3), sizeof(p4));

Out: sizeof(p1) = 8, sizeof(p2) = 1;  //由于展开后为char *p1, p2;这里p2为int型
	 sizeof(p3) = 8, sizeof(p4) = 8;
```

##### main函数参数

```c
int main();
int main(int argc, char *argv[]); //第一个参数代表命令行参数的个数；第二个参数代表命令行参数，其中argv[0]=文件名
int main(int argc, char *argv[], char **env); //第三个参数代表环境变量
```

