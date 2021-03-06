#### 目录
- [5. 其他特性](others.md/#5.其他特性)
  - [5.1. 前置自增和自减](others.md/#51前置自增和自减)
  - [5.2. ``const`` 用法](others.md/#52const用法)
  - [5.3. 整型](others.md/#53整型)
  - [5.4. 预处理宏](others.md/#54预处理宏)
  - [5.5. ``0``, ``nullptr``和``NULL``](others.md/#550nullptr和null)
  - [5.6. sizeof](others.md/#56sizeof)

# 5.其他特性

## 5.1.前置自增和自减

> **Tip**: 对于迭代器和其他模板对象使用前缀形式 (``++i``) 的自增, 自减运算符.

**定义**:

对于变量在自增 (``++i`` 或 ``i++``) 或自减 (``--i`` 或 ``i--``) 后表达式的值又没有没用到的情况下, 需要确定到底是使用前置还是后置的自增 (自减).

**优点**:

不考虑返回值的话, 前置自增 (``++i``) 通常要比后置自增 (``i++``) 效率更高. 因为后置自增 (或自减) 需要对表达式的值 ``i`` 进行一次拷贝. 如果 ``i`` 是迭代器或其他非数值类型, 拷贝的代价是比较大的. 既然两种自增方式实现的功能一样, 为什么不总是使用前置自增呢?

**缺点**:

在 C 开发中, 当表达式的值未被使用时, 传统的做法是使用后置自增, 特别是在 ``for`` 循环中. 有些人觉得后置自增更加易懂, 因为这很像自然语言, 主语 (``i``) 在谓语动词 (``++``) 前.

**结论**:

对简单数值 (非对象), 两种都无所谓. 对迭代器和模板类型, 使用前置自增 (自减).

## 5.2.``const``用法

> **Tip**: 我们强烈建议你在任何可能的情况下都要使用 ``const``.

**定义**:

在声明的变量或参数前加上关键字 ``const`` 用于指明变量值不可被篡改 (如 ``const int foo`` ).

**优点**:

大家更容易理解如何使用变量. 编译器可以更好地进行类型检测, 相应地, 也能生成更好的代码. 人们对编写正确的代码更加自信, 因为他们知道所调用的函数被限定了能或不能修改变量值. 即使是在无锁的多线程编程中, 人们也知道什么样的函数是安全的.

**缺点**:

``const`` 是入侵性的: 如果你向一个函数传入 ``const`` 变量, 函数原型声明中也必须对应 ``const`` 参数 (否则变量需要 ``const_cast`` 类型转换), 在调用库函数时显得尤其麻烦.

**结论**:

``const`` 变量, 数据成员, 函数和参数为编译时类型检测增加了一层保障; 便于尽早发现错误. 因此, 我们强烈建议在任何可能的情况下使用 ``const``:

- 如果函数不会修改传你入的引用或指针类型参数, 该参数应声明为 ``const``.
- 尽可能将函数声明为 ``const``. 访问函数应该总是 ``const``. 其他不会修改任何数据成员, 未调用非 ``const`` 函数, 不会返回数据成员非 ``const`` 指针或引用的函数也应该声明成 ``const``.
- 如果数据成员在对象构造之后不再发生变化, 可将其定义为 ``const``.

然而, 也不要发了疯似的使用 ``const``. 像 ``const int * const * const x;`` 就有些过了, 虽然它非常精确的描述了常量 ``x``. 关注真正有帮助意义的信息: 前面的例子写成 ``const int** x`` 就够了.

``const`` 的位置:

有人喜欢 ``int const *foo`` 形式, 不喜欢 ``const int* foo``, 他们认为前者更一致因此可读性也更好: 遵循了 ``const`` 总位于其描述的对象之后的原则. 但是一致性原则不适用于此, "不要过度使用" 的声明可以取消大部分你原本想保持的一致性. 将 ``const`` 放在前面才更易读, 因为在自然语言中形容词 (``const``) 是在名词 (``int``) 之前.

这是说, 我们提倡但不强制 ``const`` 在前. 但要保持代码的一致性! (Yang.Y 注: 也就是不要在一些地方把 ``const`` 写在类型前面, 在其他地方又写在后面, 确定一种写法, 然后保持一致.)

## 5.3.整型

> **Tip**: C++ 内建整型中, 仅使用 ``int``. 如果程序中需要不同大小的变量, 可以使用 ``<stdint.h>`` 中长度精确的整型, 如 ``int16_t``.如果您的变量可能不小于 2^31 (2GiB), 就用 64 位变量比如 ``int64_t``. 此外要留意，哪怕您的值并不会超出 int 所能够表示的范围，在计算过程中也可能会溢出。所以拿不准时，干脆用更大的类型。

**定义**:

C++ 没有指定整型的大小. 通常人们假定 ``short`` 是 16 位, ``int`` 是 32 位, ``long`` 是 32 位, ``long long`` 是 64 位.

**优点**:

保持声明统一.

**缺点**:

C++ 中整型大小因编译器和体系结构的不同而不同.

**结论**:

``<stdint.h>`` 定义了 ``int16_t``, ``uint32_t``, ``int64_t`` 等整型, 在需要确保整型大小时可以使用它们代替 ``short``, ``unsigned long long`` 等. 在 C 整型中, 只使用 ``int``. 在合适的情况下, 推荐使用标准类型如 ``size_t`` 和 ``ptrdiff_t``.

如果已知整数不会太大, 我们常常会使用 ``int``, 如循环计数. 在类似的情况下使用原生类型 ``int``. 你可以认为 ``int`` 至少为 32 位, 但不要认为它会多于 ``32`` 位. 如果需要 64 位整型, 用 ``int64_t`` 或 ``uint64_t``.

对于大整数, 使用 ``int64_t``.

不要使用 ``uint32_t`` 等无符号整型, 除非你是在表示一个位组而不是一个数值, 或是你需要定义二进制补码溢出. 尤其是不要为了指出数值永不会为负, 而使用无符号类型. 相反, 你应该使用断言来保护数据.

如果您的代码涉及容器返回的大小（size），确保其类型足以应付容器各种可能的用法。拿不准时，类型越大越好。

小心整型类型转换和整型提升（acgtyrant 注：integer promotions, 比如 ``int`` 与 ``unsigned int`` 运算时，前者被提升为 ``unsigned int`` 而有可能溢出），总有意想不到的后果。

**关于无符号整数**:

有些人, 包括一些教科书作者, 推荐使用无符号类型表示非负数. 这种做法试图达到自我文档化. 但是, 在 C 语言中, 这一优点被由其导致的 bug 所淹没. 看看下面的例子:

```C
    for (unsigned int i = foo.Length()-1; i >= 0; --i) ...
```

上述循环永远不会退出! 有时 gcc（编译器） 会发现该 bug 并报警, 但大部分情况下都不会. 类似的 bug 还会出现在比较有符合变量和无符号变量时. 主要是 C 的类型提升机制会致使无符号类型的行为出乎你的意料.

因此, 使用断言来指出变量为非负数, 而不是使用无符号型!

## 5.4.预处理宏

> **Tip**: 使用宏时要非常谨慎, 尽量以内联函数, 枚举和常量代替之.

宏意味着你和编译器看到的代码是不同的. 这可能会导致异常行为, 尤其因为宏具有全局作用域.

值得庆幸的是, C++ 中, 宏不像在 C 中那么必不可少. 以往用宏展开性能关键的代码, 现在可以用内联函数替代. 用宏表示常量可被 ``const`` 变量代替. 用宏 "缩写" 长变量名可被引用代替. 用宏进行条件编译... 这个, 千万别这么做, 会令测试更加痛苦 (``#define`` 防止头文件重包含当然是个特例).

宏可以做一些其他技术无法实现的事情, 在一些代码库 (尤其是底层库中) 可以看到宏的某些特性 (如用 ``#`` 字符串化, 用 ``##`` 连接等等). 但在使用前, 仔细考虑一下能不能不使用宏达到同样的目的.

下面给出的用法模式可以避免使用宏带来的问题; 如果你要宏, 尽可能遵守:

- 不要在 ``.h`` 文件中定义宏.
- 在马上要使用时才进行 ``#define``, 使用后要立即 ``#undef``.
- 不要只是对已经存在的宏使用 ``#undef``，选择一个不会冲突的名称；
- 不要试图使用展开后会导致 C++ 构造不稳定的宏, 不然也至少要附上文档说明其行为.
- 不要用 ``##`` 处理函数，类和变量的名字。

## 5.5.``0``,``nullptr``和``NULL``

> **Tip**: 整数用 ``0``, 实数用 ``0.0``, 指针用 ``nullptr`` 或 ``NULL``, 字符 (串) 用 ``'\0'``.

整数用 ``0``, 实数用 ``0.0``, 这一点是毫无争议的.

对于指针 (地址值), 到底是用 ``0``, ``NULL`` 还是 ``nullptr``. C++11 项目用 ``nullptr``; C++03 项目则用 ``NULL``, 毕竟它看起来像指针。实际上，一些 C++ 编译器对 ``NULL`` 的定义比较特殊，可以输出有用的警告，特别是 ``sizeof(NULL)`` 就和 ``sizeof(0)`` 不一样。

字符 (串) 用 ``'\0'``, 不仅类型正确而且可读性好.

## 5.6.sizeof

> **Tip**: 尽可能用 ``sizeof(varname)`` 代替 ``sizeof(type)``.

使用 ``sizeof(varname)`` 是因为当代码中变量类型改变时会自动更新. 您或许会用 ``sizeof(type)`` 处理不涉及任何变量的代码，比如处理来自外部或内部的数据格式，这时用变量就不合适了。

```C
    Struct data;
    Struct data; memset(&data, 0, sizeof(data));
```
> **Warning**:

```C
    memset(&data, 0, sizeof(Struct));
```

```C
    if (raw_size < sizeof(int)) {
        LOG(ERROR) << "compressed record not big enough for count: " << raw_size;
        return false;
    }
```

**译者（acgtyrant）笔记**

1. 注意初始化 const 对象时，必须在初始化的同时值初始化。
1. 用断言代替无符号整型类型，深有启发。