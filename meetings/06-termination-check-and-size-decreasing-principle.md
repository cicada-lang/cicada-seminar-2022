---
title: Termination-check and size-decreasing principle
date: 2022-10-23
---

# 需要能写 recursion

不能写 recursion 的话，用 `recursion` 语法关键词定义 `fibonacci`：

```js
// current <- next
// next <- current + next
function fibonacci_iter(count: Nat): (current: Nat, next: Nat) -> Nat {
  return recursion (count) {
    case zero =>
      (current, next) => current
    case add1(_prev, almost) =>
      (current, next) => almost.prev(next, add(current, next))
  }
}

function fibonacci(n: Nat): Nat {
  return fibonacci_iter(n, zero, add1(zero))
}
```

用 recursion 定义 `fibonacci`（不考虑时间复杂度）：

```js
function fibonacci(n: Nat): Nat {
  case (zero) => zero
  case (add1(zero)) => add1(zero)
  case (add1(add1(m))) => add(fibonacci(add1(m)), fibonacci(m))
}
```

Alternative syntax design：

```js
function fibonacci(A: Type, n: Nat): Nat {
  fixpoint fib(n: Nat): Nat {
    case (zero) => zero
    case (add1(zero)) => add1(zero)
    case (add1(add1(m))) => add(fib(add1(m)), fib(m))
  }
}
```

```js
claim fibonacci(n: Nat): Nat
case fibonacci(zero) => zero
case fibonacci(add1(zero)) => add1(zero)
case fibonacci(add1(add1(m))) => add(fibonacci(add1(m)), fibonacci(m))
```

我们的语法模仿了 haskell 的 function clauses，因而不能用 `match`：

```js
function fibonacci(n: Nat): Nat {
  return match (n) {
    case (zero) => zero
    case (add1(zero)) => add1(zero)
    case (add1(add1(m))) => add(fibonacci(add1(m)), fibonacci(m))
  }
}
```

```js
function fibonacci(n: Nat): Nat {
  let n = deepWalk(n)

  return match (n) {
    case (zero) => zero
    case (add1(zero)) => add1(zero)
    case (add1(add1(m))) => add(fibonacci(add1(m)), fibonacci(m))
  }
}
```

没有改变参数，直接拿来 `match`：

```js
fibonacci
fibonacciAux
```

# Recursion is not an option (without termination-check)

保持 recursion 收敛，需要 termination-check。

实现 Termination-check 的最简单方式是 size-decreasing principle。

- 类似费马无穷递降法：https://en.wikipedia.org/wiki/Proof_by_infinite_descent

首先是一个限制：

- 递归函数必须是顶层的。
- 假设每个递归函数定义都是 `function { case ... }`，如上面的 `fibonacci`，
  每个 case 称为一个 function clause（"case" 被 JS 用掉了）。
  - 也许语法关键词应该有别于 `function`，比如换成：
    - `match function`
    - `rec fixpoint`
    - `fixpoint`

Size-decreasing principle 在于：

对于每个直接或间接的递归函数而言，
每个 function clause 中，直接或间接的递归调用
都要作用于比输入 pattern 更小的 pattern 所构成的 exp。

这个原则很朴素，但是如何实现？

难点在于：

- 如何处理间接递归函数（包括相互递归函数）？
- 如何定义「更小」？
  - 对于单个参数的函数 -- `f(x)`，定义很简单。
  - 对于多个参数的函数 -- `f(x, y)`，
    有可能某次调用的时候 `x` 变大 `y` 变小，
    而另一次调用的时候 `x` 变小 `y` 变大。

Mugda paper 处理这些实现难点的方式是使用 `CallMatrix`。

# CallMatrix

CallMatrix

- extraction 根据函数的定义，生成矩阵。
- completion 处理间接的递归函数。
  - 这一步可能需要 lazy 相关的技巧，而不能真的生成完整的集合闭包。

和 completion 的 idea 相似的东西：

- https://en.wikipedia.org/wiki/Floyd%E2%80%93Warshall_algorithm

。。。

# 总结

函子：

- 函数的调用关系 -> 矩阵
- 函数的调用关系复合 -> 矩阵的复合

由于使用数学里这抽象概念，
我发现我一眼就看明白了论文的核心 idea，
而且我忘不掉了，而且我还把它和很多别的有趣的 ideas 联系起来了。

Extraction 这个步骤和下面的想法类似：

- 微积分，纤维丛，局部线性化。
- 代数拓扑。
  - Top -> Group

# 。。。
