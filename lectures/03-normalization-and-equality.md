---
title: Normalization by Evaluation
---

# 实现 NbE

- implement `readback` -- for `normalize` (and `equal`)

- `Neutral` & `NotYetValue`

  - 在 evaluate 时候，表达式就被分成了两类，
    其中与 constructor（intro rule）对应的，被 evaluate 成了 Value，
    而与 eliminator（elim rule）对应的，被 evaluate 成了 Neutral，
    这些 Neutral 被「嵌入」在 NotYetValue 中，而重新成了 Value。

  - 当我们使用 NotYetValue 时，我们的 evaluate 其实就算是在可以进行 partial evaluation。

# 关于等价关系

- implement `(assert-equal)`

- 因为 normal form 是用 equivalence 来定义的，
  而 equivalence 是就某个类型而言的。

- untyped lambda calculus 中，
  用 reduction 所定义的 equivalence
  不能另我们满意的例子：https://en.wikipedia.org/wiki/Deductive_lambda_calculus#Example

- 我们获得 normal form，是为了判断 Value 之间的等价关系，
  这个等价关系同时涵盖了 definitional equivalence 与 computational equivalence，
  而不涵盖 propositional equivalence。
  propositional equivalence 是需要给出额外证明的。

关于「equivalence 是就某个类型而言的」：

- Bishop 的集合论：

  [一個構造主義者的宣言](https://readonly.link/articles/xieyuheng/xieyuheng/-/translations/zh/a-constructivist-manifesto.md)

  - 一個集合 不是只有空想存在之物
    只有當定義了之後 一個集合才存在
    爲定義一個集合
    我們要說明

    1. 如何構造其元素
    2. 如何判斷兩個元素相等

  - 函數也如此
    爲定義一個從 集合 A 到 集合 B 的函數
    我們需要說明

    1. 一個在有限時間內結束的算法
       把 A 的元素 轉化成 B 的元素

    2. 並且證明
       相等的 A 的兩個元素
       被轉化成
       相等的 B 的兩個元素

- Bishop 构造主义四原则：

  [當代數學之精神分裂症](https://readonly.link/articles/xieyuheng/xieyuheng/-/translations/zh/schizophrenia-in-contemporary-mathematics.md)

  - **First.** Mathematics is common sense.
  - **Second.** Do not ask whether a statement is true until you know what it means.
  - **Third.** A proof is any completely convincing argument.
  - **Fourth.** Meaningful distinctions deserve to be maintained.

  - 一：數學即常識。
  - 二：別問一個論斷是否真實，除非你明瞭論斷的意思。
  - 三：使你信服的論證皆爲證明。
  - 四：有意義的區別值得保留。

  注意 Bishop 对 proof 的定义是「主观的」。

- 直觉主义的数学基础。

  - 布劳维尔。

# 用递归组合子，定义处理递归函数

- introduce `Y` combinator
- try to define `factorial` by `Y`
- we will find that our reduction strategy -- call-by-value -- can not define `Y` easily
  - the normal form is infinite
- we need call-by-need reduction strategy -- `LazyValue`

# 递归函数之间的等价

- we find that `(assert-equal)` can not be used on lambda term whose normal form is infinite
- introduce `(fixpoint)` syntax keyword
  - 类似 Coq `Fixpoint`
