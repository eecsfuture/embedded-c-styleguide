#### 目录
- [1. 头文件](headers.md/#1头文件)
  - [1.1. Self-contained 头文件](headers.md/#11self-contained头文件)
  - [1.2. #define 保护](#12define保护)
  - [1.3. 前置声明](headers.md/#13前置声明)
  - [1.4. ``#include`` 的路径及顺序](headers.md/#14include的路径及顺序)

# 1. 头文件

通常每一个 ``.c`` 文件都有一个对应的 ``.h`` 文件. 也有一些常见例外, 如单元测试代码和只包含 ``main()`` 函数的 ``.c`` 文件.

正确使用头文件可令代码在可读性、文件大小和性能上大为改观.

下面的规则将引导你规避使用头文件时的各种陷阱.

## 1.1. Self-contained 头文件

> **Tip**: 头文件应该能够自给自足（self-contained,也就是可以作为第一个头文件被引入），以 ``.h`` 结尾。

所有头文件要能够自给自足。换言之，用户和重构工具不需要为特别场合而包含额外的头文件。详言之，一个头文件要有 define-guard，统统包含它所需要的其它头文件，也不要求定义任何特别 symbols.

## 1.2. #define 保护

> **Tip**: 所有头文件都应该使用 ``#define`` 来防止头文件被多重包含, 命名格式应当是: ``<PROJECT>_<PATH>_<FILE>_H_`` .

为保证唯一性, 头文件的命名应该基于所在项目源代码树的全路径. 例如, 项目 ``foo`` 中的头文件 ``foo/src/bar/baz.h`` 可按如下方式保护:

```C
		#ifndef FOO_BAR_BAZ_H_
		#define FOO_BAR_BAZ_H_
		...
		#endif // FOO_BAR_BAZ_H_
```

## 1.3. 前置声明

> **Tip**: 尽可能地避免使用前置声明。使用 ``#include`` 包含需要的头文件即可。

**定义：**

所谓「前置声明」（forward declaration）是类、函数和模板的纯粹声明，没伴随着其定义.

**优点：**

* 前置声明能够节省编译时间，多余的 ``#include`` 会迫使编译器展开更多的文件，处理更多的输入。
* 前置声明能够节省不必要的重新编译的时间。 ``#include`` 使代码因为头文件中无关的改动而被重新编译多次。

**缺点：**

* 前置声明隐藏了依赖关系，头文件改动时，用户的代码会跳过必要的重新编译过程。
* 前置声明可能会被库的后续更改所破坏。前置声明函数或模板有时会妨碍头文件开发者变动其 API. 例如扩大形参类型，加个自带默认参数的模板形参等等。
* 前置声明来自命名空间 ``std::`` 的 symbol 时，其行为未定义。
* 很难判断什么时候该用前置声明，什么时候该用 ``#include`` 。极端情况下，用前置声明代替 ``includes`` 甚至都会暗暗地改变代码的含义：

```c
		// b.h:
		struct B {};
		struct D : B {};

		// good_user.cc:
		#include "b.h"
		void f(B*);
		void f(void*);
		void test(D* x) { f(x); }  // calls f(B*)
    
```

  如果 ``#include`` 被 ``B`` 和 ``D`` 的前置声明替代， ``test()`` 就会调用 ``f(void*)`` .

* 前置声明了不少来自头文件的 symbol 时，就会比单单一行的 ``include`` 冗长。
* 仅仅为了能前置声明而重构代码（比如用指针成员代替对象成员）会使代码变得更慢更复杂.

**结论：**

* 尽量避免前置声明那些定义在其他项目中的实体.
* 函数：总是使用 ``#include``.

至于什么时候包含头文件，参见 [1.4. ``#include`` 的路径及顺序](headers.md/##1.4.``#include``的路径及顺序) 。

## 1.4. ``#include`` 的路径及顺序

> **Tip**: 使用标准的头文件包含顺序可增强可读性, 避免隐藏依赖: 相关头文件, C 库, C++ 库, 其他库的 `.h`, 本项目内的 `.h`.

项目内头文件应按照项目源代码目录树结构排列, 避免使用 UNIX 特殊的快捷目录 ``.`` (当前目录) 或 ``..`` (上级目录). 例如, ``google-awesome-project/src/base/logging.h`` 应该按如下方式包含:

```C
    #include "base/logging.h"
```

又如, ``dir/foo.c`` 或 ``dir/foo_test.c`` 的主要作用是实现或测试 ``dir2/foo2.h`` 的功能, ``foo.c`` 中包含头文件的次序如下:

  1. ``dir2/foo2.h`` (优先位置, 详情如下)
  1. C 系统文件
  1. C++ 系统文件
  1. 其他库的 ``.h`` 文件
  1. 本项目内 ``.h`` 文件

这种优先的顺序排序保证当 ``dir2/foo2.h`` 遗漏某些必要的库时， ``dir/foo.c`` 或 ``dir/foo_test.c`` 的构建会立刻中止。因此这一条规则保证维护这些文件的人们首先看到构建中止的消息而不是维护其他包的人们。

``dir/foo.c`` 和 ``dir2/foo2.h`` 通常位于同一目录下 (如 ``base/basictypes_unittest.c`` 和 ``base/basictypes.h``), 但也可以放在不同目录下.

按字母顺序分别对每种类型的头文件进行二次排序是不错的主意。注意较老的代码可不符合这条规则，要在方便的时候改正它们。

您所依赖的符号 (symbols) 被哪些头文件所定义，您就应该包含（include）哪些头文件，`前置声明`__ (forward declarations) 情况除外。比如您要用到 ``bar.h`` 中的某个符号, 哪怕您所包含的 ``foo.h`` 已经包含了 ``bar.h``, 也照样得包含 ``bar.h``, 除非 ``foo.h`` 有明确说明它会自动向您提供 ``bar.h`` 中的 symbol. 不过，凡是 c 文件所对应的「相关头文件」已经包含的，就不用再重复包含进其 c 文件里面了，就像 ``foo.c`` 只包含 ``foo.h`` 就够了，不用再管后者所包含的其它内容。

举例来说, ``google-awesome-project/src/foo/internal/fooserver.c`` 的包含次序如下:

```C
	  #include "foo/public/fooserver.h" // 优先位置

		#include <sys/types.h>
		#include <unistd.h>

		#include <hash_map>
		#include <vector>

		#include "base/basictypes.h"
		#include "base/commandlineflags.h"
		#include "foo/public/bar.h"
```

**例外：**

有时，平台特定（system-specific）代码需要条件编译（conditional includes），这些代码可以放到其它 includes 之后。当然，您的平台特定代码也要够简练且独立，比如：

```C
		#include "foo/public/fooserver.h"

		#include "base/port.h"  // For LANG_CXX11.

		#ifdef LANG_CXX11
		#include <initializer_list>
		#endif  // LANG_CXX11
```

**译者 (YuleFox) 笔记**

1. 避免多重包含是学编程时最基本的要求;
1. 前置声明是为了降低编译依赖，防止修改一个头文件引发多米诺效应;
1. 标准化函数参数顺序可以提高可读性和易维护性 (对函数参数的堆栈空间有轻微影响, 我以前大多是相同类型放在一起);
1. 包含文件的名称使用 ``.`` 和 ``..`` 虽然方便却易混乱, 使用比较完整的项目路径看上去很清晰, 很条理, 包含文件的次序除了美观之外, 最重要的是可以减少隐藏依赖, 使每个头文件在 "最需要编译" (对应源文件处 :D) 的地方编译, 有人提出库文件放在最后, 这样出错先是项目内的文件, 头文件都放在对应源文件的最前面, 这一点足以保证内部错误的及时发现了.

**译者（acgtyrant）笔记**

1. 保持声明与定义分离的原则。
1. 在 ``#include`` 中插入空行以分割相关头文件, C 库, C++ 库, 其他库的 ``.h`` 和本项目内的 ``.h`` 是个好习惯。
