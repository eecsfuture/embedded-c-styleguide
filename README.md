# 嵌入式C语言风格指南

本项目参考了开源项目**Google 开源项目风格指南 (中文版)**, 并在该项目上针对C语言和嵌入式应用领域的特点做了大幅删减, 以下是参考项目的一些资料:

* [Google 开源项目风格指南 (中文版)的GitHub 托管地址](https://github.com/eecsfuture/zh-google-styleguide), 该项目翻译自[Google 官方英文版](https://github.com/google/styleguide)
* **Google 开源项目风格指南 (中文版)** 的在线文档托管在 ReadTheDocs：[在线阅读最新版本](https://zh-google-styleguide.readthedocs.io/en/latest/)

# 0.1 前言

Google 经常会发布一些开源项目, 意味着会接受来自其他代码贡献者的代码. 但是如果代码贡献者的编程风格与 Google 的不一致, 会给代码阅读者和其他代码提交者造成不小的困扰. Google 因此发布了这份自己的编程风格指南, 使所有提交代码的人都能获知 Google 的编程风格.

规则的作用就是避免混乱. 但规则本身一定要权威, 有说服力, 并且是理性的. 我们所见过的大部分编程规范, 其内容或不够严谨, 或阐述过于简单, 或带有一定的武断性.

Google 保持其一贯的严谨精神, 数万字的指南涉及广泛, 论证严密. 翻译该系列指南的主因也正是其严谨性. 严谨意味着指南的价值不仅仅局限于它罗列出的规范, 更具参考意义的是它为了列出规范而做的谨慎权衡过程.

指南不仅列出你要怎么做, 还告诉你为什么要这么做, 哪些情况下可以不这么做, 以及如何权衡其利弊. 其他团队未必要完全遵照指南亦步亦趋, 如前面所说, 这份指南是 Google 根据自身实际情况打造的, 适用于其主导的开源项目. 其他团队可以参照该指南, 或从中汲取灵感, 建立适合自身实际情况的规范.

# 0.2 背景

本指南的目的是通过详细阐述 C 注意事项来驾驭其复杂性. 这些规则在保证代码易于管理的同时, 也能高效使用 C 的语言特性.

风格, 亦被称作可读性, 也就是指导 C 编程的约定. 使用术语 “风格” 有些用词不当, 因为这些习惯远不止源代码文件格式化这么简单.

使代码易于管理的方法之一是加强代码一致性. 让任何程序员都可以快速读懂你的代码这点非常重要. 保持统一编程风格并遵守约定意味着可以很容易根据 “模式匹配” 规则来推断各种标识符的含义. 创建通用, 必需的习惯用语和模式可以使代码更容易理解. 在一些情况下可能有充分的理由改变某些编程风格, 但我们还是应该遵循一致性原则，尽量不这么做.

注意: 本指南并非 C 教程, 我们假定读者已经对 C 非常熟悉.

# 0.3 开源许可
本项目和英文版一样, 使用 CC-By 3.0 许可证, 具体协议见https://creativecommons.org/licenses/by/3.0/


<a rel="license" href="http://creativecommons.org/licenses/by/3.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by/3.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by/3.0/">Creative Commons Attribution 3.0 Unported License</a>.

# 0.4 作者
  
原作者（**Google 官方英文版**）:	  
Benjy Weinberger  
Craig Silverstein  
Gregory Eitzmann  
Mark Mentovai  
Tashana Landray  

翻译（**Google 开源项目风格指南 (中文版)**）:	  
YuleFox  
Yang.Y  
acgtyrant  
lilinsanity  

修改者（**嵌入式C语言风格指南**）：  
eecsfuture

# 目录
- [1. 头文件](headers.md/#1.头文件)
  - [1.1. Self-contained 头文件](headers.md/##1.1.Self-contained头文件)
  - [1.2. #define 保护](headers.md/##1.2.#define保护)
  - [1.3. 前置声明](headers.md/##1.3.前置声明)
  - [1.4. ``#include`` 的路径及顺序](headers.md/##1.4.``#include``的路径及顺序)
- [2. 作用域](scoping.md/#2.作用域)
  - [2.1. 静态变量](scoping.md/##2.1.静态变量)
  - [2.2. 局部变量](scoping.md/##2.2.局部变量)
- [3. 函数](functions.md/#3.函数)
  - [3.1. 参数顺序](functions.md/##3.1.参数顺序)
  - [3.2. 编写简短函数](functions.md/##3.2.编写简短函数)
  - [3.2. 编写简短函数](functions.md/##3.2.编写简短函数)
- [4. 风格检查工具Cpplint](cpplint.md/#4.Cpplint)
- [5. 其他特性](others.md/#5.其他特性)
  - [5.1. 前置自增和自减](others.md/##5.1.前置自增和自减)
  - [5.2. ``const`` 用法](others.md/##5.2.``const``用法)
  - [5.3. 整型](others.md/##5.3.整型)
  - [5.4. 预处理宏](others.md/##5.4.预处理宏)
  - [5.5. ``0``, ``nullptr``和``NULL``](others.md/##5.5.``0``,``nullptr``和``NULL``)
- [6. 命名约定](naming.md/#6.命名约定)
  - [6.1.通用命名规则](naming.md/##6.1.通用命名规则)
  - [6.2.文件命名](naming.md/##6.2.文件命名)
  - [6.3.类型命名](naming.md/##6.3.类型命名)
  - [6.4.变量命名](naming.md/##6.4.变量命名)
  - [6.5.常量命名](naming.md/##6.5.常量命名)
  - [6.6.函数命名](naming.md/##6.6.函数命名)
  - [6.7.枚举命名](naming.md/##6.7.枚举命名)
  - [6.8.宏命名](naming.md/##6.8.宏命名)
  - [6.9.命名规则的特例](naming.md/##6.9.命名规则的特例)
- [7. 注释](comments.md/#7.注释)
  - [7.1.注释风格](comments.md/##7.1.注释风格)
  - [7.2.文件注释](comments.md/##7.2.文件注释)
  - [7.3.类注释](comments.md/##7.3.类注释(*本节为C++特性，可忽略*))
  - [7.4.函数注释](comments.md/##7.4.函数注释)
  - [7.5.变量注释](comments.md/##7.5.变量注释)
  - [7.6.实现注释](comments.md/##7.6.实现注释)
  - [7.7.标点,拼写和语法](comments.md/##7.7.标点,拼写和语法)
  - [7.8.TODO注释](comments.md/##7.8.TODO注释)
  - [7.9.弃用注释](comments.md/##7.9.弃用注释)
- [8. 格式](formatting.md/)
  - [8.1.行长度](formatting.md/##8.1.行长度)
  - [8.2.非ASCII字符](formatting.md/##8.2.非ASCII字符)
  - [8.3.空格还是制表位](formatting.md/##8.3.空格还是制表位)
  - [8.4.函数声明与定义](formatting.md/##8.4.函数声明与定义)
  - [8.5.Lambda 表达式](formatting.md/##8.5.Lambda表达式(*本节为C++特性，可忽略*))
  - [8.6.函数调用](formatting.md/##8.6.函数调用)
  - [8.7.列表初始化格式](formatting.md/## 8.7.列表初始化格式(*本节为C++特性，可忽略*))
  - [8.8.条件语句](formatting.md/##8.8.条件语句)
  - [8.9.循环和开关选择语句](formatting.md/##8.9.循环和开关选择语句)
  - [8.10.指针和引用表达式](formatting.md/##8.10.指针和引用表达式)
  - [8.11.布尔表达式](formatting.md/##8.11.布尔表达式)
  - [8.12.函数返回值](formatting.md/##8.12.函数返回值)
  - [8.13.变量及数组初始化](formatting.md/##8.13.变量及数组初始化)
  - [8.14.预处理指令](formatting.md/##8.14.预处理指令)
  - [8.15.类格式](formatting.md/##8.15.类格式(*本节为C++特性，可忽略*))
  - [8.16.构造函数初始值列表](formatting.md/##8.16.构造函数初始值列表(*本节为C++特性，可忽略*))
  - [8.17.命名空间格式化](formatting.md/##8.17.命名空间格式化(*本节为C++特性，可忽略*))
  - [8.18.水平留白](formatting.md/##8.18.水平留白)
  - [8.19. 垂直留白](formatting.md/##8.19.垂直留白)
- [9. 规则特例](exceptions.md/#9.规则特例)
  -[9.1.现有不合规范的代码](exceptions.md/##9.1.现有不合规范的代码)
  -[9.2.Windows代码](exceptions.md/##9.2.Windows代码)
- [10. 结束语](end.md/#10.结束语)