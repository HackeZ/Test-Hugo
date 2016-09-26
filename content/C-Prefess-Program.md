+++
Categories = ["Book", "C"]
Description = ""
Tags = ["Development", "golang"]
date = "2016-07-30T22:47:07+08:00"
menu = ""
title = "C Prefess Program Note"

+++
# 《C专家编程》 笔记本

### 《C专家编程》是每一位程序员应该读的第二本C语言书籍！

> 讲了各种c的缺陷，推荐go用户好好体验，很多都在go里做了修正 —— [ggarlic]()

> 书很好，翻译一般，校对不负责任。

1. **新西兰** 是关于时间编程的一个特殊地点；

2. 编译器设计者的金科玉律 **效率（几乎）就是一切**；

3. B语言是 **解释模式** 语言，而C语言是 **编译模式并引入了类型系统** ，使效率大大提高；

4. C语言是为了 **编译器设计者** 而生的，这就是为什么很多编程语言在初期都是使用C语言编译器的原因；

5. 为什么数组 **下标从0开始** ？
    - 在计算资源缺乏的过去，0标号的写法可以节省编译时间
    - 现代语言中0标号可以更优雅的表示数组字串
    - 在支持指针的语言中，标号被视作是偏移量，因此从0开始更符合逻辑

6. 将 **常量放在比较表达式的前面** 可以大大减少因打字出错将 `==` 输入为 `=` 的情况 `if(3 == x) {...}`

7. **Bourne Shell** 的出现促成了 The International Obfuscated C Code Competition（国际C语言混乱代码大赛）；

8. **ANSI C** 美国国家标准化组织所定下的C语言标准；

9. 语言律师 —— “可以从200多页的手册中提炼出5句话，并起来放到你面前，你只要一看就能明白自己问题答案的人”；

10. 关键字 `const` 并不能把变量变成常量！在一个符号前面加上 `const` 只是表示这个符号不能被赋值，也就是变为 **Read Only** 。其最有用之处在于限定函数的形参，这样该函数将不会修改实参指针所指的数据，但其他的（没有用 `const` 的）函数却有可能修改它。

11. 尽量不要在你的程序中使用 **无符号类型** ，以免增加不必要的复杂性。尤其是，不要仅仅因为其不存在负值（如年龄、 国债）而用它来表示数量。因为在某些情况下，会出现以下 BUG：
    - -1 会被翻译成非常巨大的正整数。
    - -1 会比 1 大。

12. `malloc(strlen(str))` 几乎永远是错误的，因为 不要忘记还有 `'\0'` ；

13. NUL  用于结束有一个 ASCII 码零的正确术语；
    NULL 用于表示什么都不指向；

14. 如果需要使用一些临时变量的时候，请把它放在块的开始处！

15. 缺省采用 "Fall Through"，在 **97%** 的情况下都是错误的！
```c
// Fall Through : End without break;
switch (number) {
    case 1: printf("case 1\n");
    case 2: printf("case 2\n");
    case 3: printf("case 3\n");
    ...
}
```

16. 一种简单的方法，使一段代码第一次执行时的行为与以后的执行的行为不同；
```c
generate_initializer(char * string) 
{
    static char separator = ' ';
    printf("%c %s\n", separator, string);
    separator = ',';
}
```

17. 重载之过：
```c
// 这是多少个乘号？
p = N * sizeof * q;
r = malloc(p);
// 答案：1个，sizeof操作符将指针q指向的东西作为操作数，它返回q所指对象的类型的字节数

// 这是int的长度乘以p？还是把未知类型的指针强制转换为int？
apple = sizeof(int) * p;
```

18. 什么是 **结合性** ？

> 在几个操作符具有相同优先级的时候决定先运行哪一个。

19. 为什么要使用 fgets() 而不是 gets() ？

20. 注释符缺陷：
```c
a //*
//*/ b
```
> means a/b in C but a in C++

21. 早用line，勤用lint。当你做错事的时候，他会告诉你哪里不对，应该始终使用lint程序，按照它的道德标准办事。像使用 `go-lint` 一样写出优秀的代码。

22. 将结构的声明与变量的定义分开可以使代码更加容易阅读：
```c
struct veg { int weight, price_per_lb; };
struct veg onion, radish, turnip;
```

23. `union` 与 `struct` 不同的是：

> 在内存布局中，struct 是将每个成员依次存储，而在 union 中，所有的成员都从偏移地址零开始存储。这样，每个成员的位置都重叠在一起：在某个时刻，只有一个成员真正存储于该地址。

> 所以 union 一般用于节省空间，因为 有些数据是不可能同时出现的，如果同时存储他们，显然颇为浪費。可以将互斥的两个字段存储于一个 union 中来节省空间：

```c
union secondary_characteristics {
    char has_fur;
    short num_of_leg_in_excess_of_4;
};
struct creature {
    char has_backbone;
    union secondary_characteristics form;
};
```

> 这种方法在存储 2*10^7 只动物的时候可以节省 20MB 磁盘空间。

> union 也可以将同一个数据解释成两个不同的东西：

```c
union bits32_tag {
    int whole;  /* 一个32位的值 */
    struct {char c0, c1, c2, c3; } byte; /* 4个8位的字节 */
} value;
```

24. `enum` 也就是 Golang 中的 `toao`：
```c
enum sizes { small = 7, medium, large = 10, humungous };
// medium = 8 ; humungous = 11;
```

25. 理解C语言声明的优先级规则
    - A 声明从它的名字开始读取，然后按照优先级顺序依次读取。
    - B 优先级从高到低依次是：
        + 1 声明中被括号括起来的那部分
        + 2 后缀操作符：
            + 括号 () 表示这是一个函数；
            + 方括号 [] 表示这是一个数组；
        + 3 前缀操作符：星号 * 表示 “指向...的指针”
    - C 如果 const 和（或）volatie 关键字的后面紧跟类型说明符（如int，long等），那么它作用于类型说明符。在其他情况下，const 和（或）volatie关键字作用于它左边紧邻的指针星号。

```c
char * const *(*next) ();
```

> next 是一个指向函数的指针，该函数返回另一个指针，该指针指向一个只读的指向char的指针。

26. 使字符串的比较看上去更自然：

> strcmp() 函数用于比较两个字符串，当他们相等返回 0

```c
// 这看起来有点不符合语法
if(!strcmp(s, "volatile")) return QUALIFIER;

// 也许我们可以这样做
#define STRCMP(a, R, b) (strcmp(a, b) R 0)
if(STRCMP(s, == , "volatile"))
```

27. 数组与指针的区别： `P102`

28. 
