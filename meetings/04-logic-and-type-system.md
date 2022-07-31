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

皮尔士所给出的定义，可以看是逻辑表达式的语义，
或者说，是我们（像一个解释器一样）解释逻辑表达式的方式。

- 注意，「鸟在叫」并不是「到早晨了」的原因，
  这二者之间有很强的相关性，这个相关性足以让我们相信规则。

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

上面提到的 `apply` 推理规则，是逻辑的核心，应该改写为：

```
ctx |- f: (-> A B)
ctx |- x: A
---------------- apply
ctx |- (f x): B
```

在此之外还有一个看似平凡的推理规则 `lambda`，
这个推理就是说，带有 `ctx` 的断言，可以被转化为不带 `ctx` 的断言：

```
ctx, x: A |- b: B
-------------------------------- lambda
ctx |- (lambda (x) b): (-> A B)
```

上面两个推理规则都是需要前提的，
还有一个不需要前提的推理规则 `variable`：

```
------------------- variable
ctx, x: A |- x: A
```

## 证明的例子

**命题 K** `(-> A B A)`

- 它是 `(-> A (-> B A))` 的缩写。

证明：

```
x: A, y: B |- x: A
--------------------
x: A |- (lambda (y) x): (-> B A)
--------------------------
|- (lambda (x y) x): (-> A B A)
```

**命题 S** `(-> (-> A B C) (-> A B) A C)`

- 它是 `(-> (-> A B C) (-> A B) (-> A C))` 的缩写。

证明：

```
f: (-> A B C), g: (-> A B), x: A |- (f x (g x)): C
----------------------------------------------------
f: (-> A B C), g: (-> A B) |- (lambda (x) (f x (g x))): (-> A C)
---------------------------------------------------------
f: (-> A B C) |- (lambda (x g) (f x (g x))): (-> (-> A B) A C)
----------------------------------------------------------
|- (lambda (f g x) (f x (g x))): (-> (-> A B C) (-> A B) A C)
```

- **练习** 上面的第一步省略了很多 `apply` 的步骤，
  如何设计良好的语法来详细表达这些步骤？

- 上面的 `K` 与 `S` 来自[组合子演算](https://en.wikipedia.org/wiki/SKI_combinator_calculus)。

## 结论

我们发现用来记录证明的语法，就是函数式编程的语法。

Lambda 演算中的每个表达式，就对应于一个推理规则：

- `variable` 在 context 中查找变元的类型；
- `lambda` 将 context 中的绑定的变元与其类型，记录到表达式中；
- `apply` 逻辑的核心推理规则，对应于函数作用。

所以 **程序即证明**，**命题即类型**。

> Program as proof, proposition as type.

这样我们就把逻辑与编程，综合成立一个新的学科，叫类型论（type theory）。

类型论的基本原则就是，同时考虑 命题的表达式 与 证明的表达式。

## 归纳推理 与 合情推理

TODO 天才的 Peirce。

TODO 归纳推理是不完全的推理。

TODO 和情推理是关于「猜测」的推理。

TODO 介绍 Polya 的书。

## 两种语法风格

TODO application v.s. composition

TODO natural deduction v.s. sequent calculus

空格代表函数作用：

```
f: A -> B
x: A
-----------
f x: B
```

空格代表函数复合：

```
f: A -> B
g: B -> C
-----------
f g: A -> C
```

```
swap: A B -> B A
dup: A -> A A
drop: A ->
```

TODO Forth 以及基于栈的语言。

# 类型检查

我们已经明白了 **程序就是证明**，**命题就是类型**。

因此 **类型检查的过程，就是检查证明是否正确的过程**。

我们已经明白如何实现解释器了，
现在我们想要要实现类型检查器。

首先把断言 `ctx |- x: A` 命名为 `check`，
然后用纯粹的前缀表达式，写为 `(check ctx x A)`。

为了熟悉一下用前缀表达式所写的断言，
我们重复上面的两个证明如下：

**命题 K** `(-> A B A)`

证明：

```
(check ((x A) (y B)) x A)
--------------------------
(check ((x A)) (lambda (y) x) (-> B A))
--------------------------
(check () (lambda (x y) x) (-> A B A))
```

**命题 S** `(-> (-> A B C) (-> A B) A C)`

证明：

```
(check ((f (-> A B C))
        (g (-> A B))
        (x A))
  (f x (g x))
  C)
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

解释器是一个叫做 `evaluate` 的函数，其类型为：

```typescript
evaluate(env: Env, exp: Exp): Value
```

类型检查器是一个叫做 `check` 的函数，其类型为：

```typescript
check(ctx: Ctx, exp: Exp, t: Type): boolean
```

其中 `Exp` 是 Lambda 演算的表达式，
而 `Type` 的定义如下：

```typescript
abstract class Type {}
class Atom extends Type {
  name: string;
}
class Arrow extends Type {
  art_t: Type;
  ret_t: Type;
}
```

- `Atom` 为原子类型；
- `Arrow` 为函数类型。

其实就是把上面的 `check` 断言，实现为一个 **谓词**（predicate）。

- 返回类型为 `boolean` 的函数叫做谓词。

在实际的实现中，`check` 需要将类型检查过程中出现的错误反馈给用户，
因此返回值其实不是简单的 `boolean`。

我们用 throw expection 的方式来实现报错功能，
因此改写 `check` 的类型如下：

```typescript
check(ctx: Ctx, exp: Exp, t: Type): void
```

- 类型检查成功时，不返回值；
- 类型检查失败时，通过 `throw new TypeError(...)` 的方式来实现报错。

每条推理规则，都对应于 `check` 需要处理的一种情况，
也对应于一个种表达式。

回顾 Lambda 演算的三种表达式：

| 表达式名 | 推理规则名 |
| -------- | ---------- |
| `Ap`     | `apply`    |
| `Fn`     | `lambda`   |
| `Var`    | `variable` |

首先考虑 `apply` 这条推理规则：

```
ctx |- f: (-> A B)
ctx |- x: A
---------------- apply
ctx |- (f x): B
```

写为前缀表达式：

```
(check ctx f (-> A B))
(check ctx x A)
------------------- apply
(check ctx (f x) B)
```

- 我们想要检查 `(f x)` 的类型为 `B`；
- 我们必须能够推导出 `f` 的类型为 `(-> A B)`；
- 如果我们能够推导出 `f` 的类型，那么我们就也能推导出 `(f x)` 的类型。

由此，我们发现，对于 `apply` 这条推理规则，
我们不光可以实现 `check`，还可以实现一个更强的函数 `infer`。

```typescript
infer(ctx: Ctx, exp: Exp): Type
```

说 `infer` 「更强」，就在于 `check` 可以用 `infer` 来实现，
因为 `check` 可以分解为 `infer` + `check_equal_type`。

- 功能上，如果一个语言的类型检查器可以推导出表达式的类型，
  而不需要用户声明这些类型，那看起来也是「更强」。

```typescript
check(ctx: Ctx, exp: Exps.Ap, t: Type): void {
  const inferred_t = infer(ctx, exp)
  check_equal_type(inferred_t, t)
}

infer(ctx: Ctx, exp: Exps.Ap): Type {
  const target_t = infer(ctx, exp.target)
  if (!(target_t instanceof Types.Arrow)) {
    throw new TypeError()
  }

  const { arg_t, ret_t } = target_t
  check(ctx, exp.arg, arg_t)
  return ret_t
}
```

再考虑 `variable` 这条推理规则：

```
------------------- variable
ctx, x: A |- x: A
```

写为前缀表达式：

```
------------------------------ variable
(check (extend ctx x A) x A)
```

```
(check-equal-type (lookup ctx x) A)
------------------------------------ variable
(check ctx x A)
```

显然对于 `variable` 我们不光可以实现 `check`，也可以实现 `infer`：

```typescript
check(ctx: Ctx, exp: Exps.Var, t: Type): void {
  const inferred_t = infer(ctx, exp)
  check_equal_type(inferred_t, t)
}

infer(ctx: Ctx, exp: Exps.Var): Type {
  return lookup(ctx, exp.name)
}
```

最后考虑 `lambda` 这条推理规则：

```
ctx, x: A |- b: B
-------------------------------- lambda
ctx |- (lambda (x) b): (-> A B)
```

写为前缀表达式：

```
(check (extend ctx x A) b B)
------------------------------------ lambda
(check ctx (lambda (x) b) (-> A B))
```

我差点儿以为我可以一路把 `infer` 实现下去，
但是这里想法破灭了。

我们不可能从 `(lambda (x) b)` 推导出 `(-> A B)` 中的 `A`。

- 假设我们只用表达式自身所携带的信息，
  外加 `ctx` 中的信息（这些都算是局部信息），
  来实现 `infer`。

- 假设我们不能给 `(lambda (x) a)` 做类型标注（type annotation）
  而写成 `(lambda ((x A)) a)`。

  - 不带类型标注的 Lambda 演算，叫做「Curry 风格」；
  - 带有类型标注的 Lambda 演算，叫做「Church 风格」。

对于 `lambda` 这条推理规则，看来我们必须直接实现 `check` 了。

```typescript
check(ctx: Ctx, exp: Exps.Fn, t: Type): void {
  if (!(t instanceof Types.Arrow)) {
    throw new TypeError()
  }

  const { arg_t, ret_t } = t
  check(ctx.extend(exp.name, arg_t), exp.ret, ret_t)
}
```

## 回顾

### 总结与重新叙述

总结一下我们的经历：

- 我们只是想实现 `check`；
- 但是我们发现，为了实现 `check`，我们有时需要 `infer`；
- 具体需要 `infer` 的，是 elimination rule 的 target。


elimination rule 的一般模式是：

```
(infer) premise about target
(check) premise
(check) premise
...
----------
(infer) conclusion
```

construction rule 的一般模式是：

```
(check) premise
(check) premise
...
----------
(check) conclusion
```

### 一路 infer 下去

We can also just provide enough information in the syntax,
i.e. add annotation to the bound variable,
then we can infer function abstraction.

```
(infer (extend ctx x A) b) => B
------------------------------------------ lambda
(infer ctx (lambda ((x A)) b)) => (-> A B)
```

Note that, we do not need to annotate the return type of function,
we only need to annotate the argument type.

### 未来会用到的推广

回顾一下我们所实现的类型检查函数 `check` 与 `infer`。

- `Fn` 是函数类型的构造子（data constructor）；
- `Ap` 是函数类型的分解子（data eliminator）。

由于函数类型的推理规则，是类型系统的核心，
所以我们可以把这个属性 **推广** 到其他的类型中，即：

- 对于所有的 data constructor 都应该 `check`；
- 对于所有的 data eliminator 都应该 `infer`。

## 结论

上面的技巧叫做 **Bidirectional type checking**，
可以让我们把逻辑中的推理规则，
转化为类型系统中的类型检查算法（类型检查函数）。

当用纯粹的前缀表达式来表示断言（judgment）时，
我们给了断言一个名字叫 `check`。

Bidirectional type checking 就在于，
断言应该被分为两类，一类是 `check` 另一类是 `infer`。

TODO 关于 关系 与 函数，以及函数的单值性。
