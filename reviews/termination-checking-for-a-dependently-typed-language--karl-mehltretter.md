论文首先介绍了基于 size-decreasing principle 实现 Termination Check 的基础方法。
然后加上了 sized-datatype。

在介绍 size-decreasing principle 的时候，
首先将函数之间调用关系转化成 `CallMatrix`，
也就是一个函子：

- 函数的调用关系（graph） -> 矩阵
- 函数的调用关系复合（graph 中 path 的复合） -> 矩阵的复合

利用 `CallMatrix` 处理了：

- 间接递归函数（包括相互递归函数）
- Matrix 的元素是 semiring 定义了什么是「更小」
