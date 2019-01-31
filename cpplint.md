# 4. Cpplint

**总述**

使用 ``cpplint.py`` 检查风格错误.

**说明**

``cpplint.py`` 是一个用来分析源文件, 能检查出多种风格错误的工具. 它不并完美, 甚至还会漏报和误报, 但它仍然是一个非常有用的工具. 在行尾加 ``// NOLINT``, 或在上一行加 ``// NOLINTNEXTLINE``, 可以忽略报错. 

某些项目会指导你如何使用他们的项目工具运行 ``cpplint.py``. 如果你参与的项目没有提供, 你可以单独下载 [cpplint.py](http://github.com/google/styleguide/blob/gh-pages/cpplint/cpplint.py).