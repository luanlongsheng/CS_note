# C++进阶

### 1. C到C++的过渡

- C++头文件共有87个

==C++ = C语言(头文件29个)+头文件(58个)+异常处理+类和对象+STL+模板+Lambda==

- C++同时支持四种编程范式：面向过程、面向对象、泛型、函数式编程（效率依次增大）

效率：写代码＋测试＋维护

面向对象的编程范式比面向过程的编程范式更容易维护

- C++不需要和格式占位符绑定

> cout输出默认去除浮点数后面多余的0
>
> cin和cout不是函数，它们是对象

```c++
int n;
double m;
cin >> n >> m;
cout << n << " " << m << endl;
```

##### queue类：队列

​	头文件：queue

​	命名空间：std

​	声明：queue<data_type> q;

**操作**

| q.front | 查看队首元素     |
| ------- | ---------------- |
| q.empty | 查看队列是否为空 |

##### stack类：栈

​	头文件：stack

​	命名空间：std

​	声明：stack<data_type> s;



##### string类：字符串

​	头文件：string

​	命名空间：std

​	声明：string s1, s2;

> 求字符串长度
>
> C++:s1.length(); 时间复杂度:O(1)
>
> C:strlen(s1);时间复杂度:O(n)

|      |      |
| ---- | ---- |
|      |      |

##### unordered_map类：哈希表(C++11标准)

​	头文件：unordered_map

​	命名空间：std

​	声明：unordered_map<key_type, value_type, hash_func> h;



- auto关键字 可以自动判断变量的类型

##### sort

> 底层使用的快速排序

```c++
sort(arr.begin(), arr.end());
sort(arr.begin(), arr.end(), cmp);
//cmp a在什么情况下排在b的前面
```

##### nth_element

> 底层实现是利用快速选择算法，也就是快排中的partion过程

求出数组中排序第k位的元素，可以使数组中的第k位就为排序后位于第k位的元素

```c++
nth_element(arr, arr + k, arr + n);
nth_element(
起始位置；
第k个位置；
终止位置；
)
```

