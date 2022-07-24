---
title: 逻辑与类型系统
date: 2022-07-24
---

# 逻辑

## 逻辑的定义

逻辑是研究推理的学问。

下面是皮尔士在 "Fixation of Belief" 中对「推理」的定义：

> 推理在于，通过思考我们已经知道的，来发现我们还不知道的。
> 因此，好的推理在于，给以正确的前提，它能得出正确的结论。
> 所以，推理是否有效，纯粹在于事实，而不在于思考本身。
> 设 A 为前提 B 为结论，问题在于，它们是否真的如此相关，
> 以至于，如果 A 为真，那么 B 也为真。
> 如果是的，那么推理就是有效的，否则就是无效的。
> 问题一点也不在于，当我承认了前提之后，是否也有承认结论的冲动。
> 我们按照自己的这种天性去做推理，一般也是正确的，但这不是必然的，
> 不论我们是否有冲动去承认它，真的结论依然是真的；
> 就算我们忍不住去相信它，假的结论也还是假的。

原文：

> The object of reasoning is to find out, from the consideration of what
> we already know, something else which we do not know. Consequently,
> reasoning is good if it be such as to give a true conclusion from true
> premises, and not otherwise. Thus, the question of validity is purely
> one of fact and not of thinking. A being the premises and B being the
> conclusion, the question is, whether these facts are really so related
> that if A is B is. If so, the inference is valid; if not, not. It is
> not in the least the question whether, when the premises are accepted
> by the mind, we feel an impulse to accept the conclusion also. It is
> true that we do generally reason correctly by nature. But that is an
> accident; the true conclusion would remain true if we had no impulse
> to accept it; and the false one would remain false, though we could
> not resist the tendency to believe in it.
>
> -- Charles Sanders Peirce, "Fixation of Belief"

## 推理的例子

我睡醒了，
发现天还是黑的，
但是听到窗外有小鸟在叫，
我就知道应该已经是早晨四五点了。

```
（规则）如果窗外有鸟叫，那么就到早晨了。
（前提）我听到窗外有鸟在叫。
------------------------------
（结论）到早晨了。
```

按照皮尔士的定义，我们应该这样理解上面的这段推理：

- 我知道一个推理规则 (或者说规律)，「如果窗外有鸟叫，那么就到早晨了」。
- 我发现了一个事实（证据），「我听到窗外有鸟在叫」。
- 我相信「到早晨了」，虽然我还没有通过看表或者看到阳光而发现这一事实。

按照皮尔士对「好的推理」的定义，
如果每当我们发现「我听到窗外有鸟在叫」这个证据，
都可以观察到「到早晨了」这个结论，
那么我们所使用的推理规则就是有效的。

皮尔士所给出的定义，可以看是解释逻辑表达式的语义，
或者说，是我们（像一个解释器一样）解释逻辑表达式的方式。

- 注意，「鸟在叫」并不是「到早晨了」的原因，
  这二者之间有很强的相关性，这个相关性足以让我们相信大前提。

  想要获得因果关系，我们必须有一个「模型」，
  或者说我们必须对「鸟」与「早晨」有更系统的知识。
  比如，鸟对光线更加敏感，比人类提前发现天亮了，所以在四五点就开始叫了。

## 推理规则

我们可以使用变元，来给出 **推理规则**（inference rule）的一般形式：

```
（规则）如果 A，那么 B
（前提）A
------------------------------
（结论）B
```

或者写成符号化的逻辑表达式：

```
(-> A B)
A
-------
B
```

- 注意，我们使用了前缀表达式 `(-> A B)`，
  而没有用中缀表达式 `A -> B`。
  此时 `(-> A (-> B C))` 可以缩写为 `(-> A B C)`，
  也就是说尾的 `(-> ...)` 可以省略。

## 证明的定义

我们还可以设计一些表达式，来作为记录推理步骤的语法。

```
f: (-> A B)
x: A
-----------
(f x): B
```

冒号右边的是命题，冒号左边的是证明。

对推理步骤的记录，就是 **证明**（或者说形式证明）。

其中，基础的「一步推理」就是推理规则。
比如上面的推理规则可以命名为 `apply` 如下。

```
f: (-> A B)
x: A
----------- apply
(f x): B
```

注意，我们对 `(-> A B)` 这类逻辑表达式的解释，
本身就带有了「假设」这一概念。
当我们说「如果窗外有鸟叫，那么就到早晨了」，
此时可能并没有鸟叫，「窗外有鸟叫」只是我们的假设。

在日常的推理中，我们总会有一些已知的假设，或者说假设的已知，
它们可以看作是上下文，或者语境（context）。

正如物理理论，是真实世界的模型；
形式逻辑，是人类推理行为的模型。

为了更好地模拟人类的推理行为，
我们引入假设，来扩展一下推理的对象，
不是单单写 `x: A` 而是写 `ctx |- x: A`，
并称其为「断言」（judgment）。

- `ctx` 是 "context" 的缩写，包含了我们当前已知的一系列假设。
- `ctx |- x: A` 是一个完整的断言。

上面提到的 `apply` 推演规则，是逻辑的核心，应该改写为：

```
|- f: (-> A B)
|- x: A
----------- apply
|- (f x): B
```

在此之外还有一个看似平凡的推演规则 `lambda`，
这个推演就是说，带有 `ctx` 的断言，可以被转化为不带 `ctx` 的断言：

```
x: A |- b: B
----------------------------- lambda
|- (lambda (x) b): (-> A B)
```

上面两个推演规则都是需要前提的，
还有一个不需要前提的推演规则 `variable`：

```
-------------- variable
x: A |- x: A
```

## 证明的例子

想要证明命题 `(-> A B A)`：

```
x: A, y: B |- x: A
------------------
x: A |- (lambda (y) x): (-> B A)
--------------------------
|- (lambda (x y) x): (-> A B A)
```

也可以把断言 `ctx |- x: A` 命名为 `check`，
然后用纯粹的前缀表达式：

```
(check ((x A) (y B)) x A)
------------------
(check ((x A)) (lambda (y) x) (-> B A))
--------------------------
(check () (lambda (x y) x) (-> A B A))
```

想要证明命题 `(-> (-> A B C) (-> A B) A C)`：

```
f: (-> A B C), g: (-> A B), x: A |- (f x (g x)): C
----------------------------------------------------
f: (-> A B C), g: (-> A B) |- (lambda (x) (f x (g x))): (-> A C)
---------------------------------------------------------
f: (-> A B C) |- (lambda (x g) (f x (g x))): (-> (-> A B) A C)
----------------------------------------------------------
|- (lambda (f g x) (f x (g x))): (-> (-> A B C) (-> A B) A C)
```

纯粹的前缀表达式：

```
(check ((f (-> A B C))
        (g (-> A B))
        (x A))
  (f x (g x)) C)
----------------------------------------------------
(check ((f (-> A B C))
        (g (-> A B)))
  (lambda (x) (f x (g x)))
  (-> A C))
---------------------------------------------------------
(check ((f (-> A B C)))
  (lambda (g x) (f x (g x)))
  (-> (-> A B) (-> A C)))
----------------------------------------------------------
(check ()
  (lambda (f g x) (f x (g x)))
  (-> (-> A B C) (-> A B) A C))
```

## 结论

我们发现用来记录证明的语法，就是函数式编程的语法。

lambda 演算中的每个表达式，就对应于一个推演规则：
- `variable` 在 context 中查找变元的类型；
- `lambda` 将 context 中的绑定的变元与其类型，记录到表达式中；
- `apply` 逻辑的核心推演规则，对应于函数作用。

所以 **程序就是证明**，**命题就是类型**。

## 归纳推理 与 合情推理

TODO 天才的 Peirce。

TODO 归纳推理是不完全的推理。

TODO 和情推理是关于「猜测」的推理。

TODO 介绍 Polya 的书

## 两种语法风格

TODO application v.s. composition。

TODO forth 以及基于栈的语言。

# 推理规则

## 推理规则 与 类型检查算法

TODO
