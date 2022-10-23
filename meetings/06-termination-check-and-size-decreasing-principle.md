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

# Recursion is not an option (without termination-check)

保持 recursion 收敛，需要 termination-check。

Termination-check 的实现方式之一是简单的 size-decreasing principle。

首先是一个限制：

- 假设每个递归函数定义都是 `function { case ... }`，如上面的 `fibonacci`，
  每个 case 称为一个 function clause（"case" 被 JS 用掉了）。
  - 也许语法关键词应该有别于 `function`，比如换成 `match function`。

Size-decreasing principle 在于：

对于每个直接或间接的递归函数而言，
每个 function clause 中，直接或间接的递归调用
都要作用于比输入 pattern 更小的 pattern 所构成的 exp。

这个原则很朴素，但是如何实现？

难点在于：

- 如何处理间接递归函数？
- 如何定义「更小」？
  - 对于单个参数的函数 -- `f(x)`，定义很简单。
  - 对于多个参数的函数 -- `f(x, y, z)`，
    有可能某次调用的时候 `x` 变大 `y` 变小，
    而另一次调用的时候 `x` 变小 `y` 变大。

Mugda paper 处理这些实现难点的方式是使用 `CallMatrix`。

# CallMatrix

# 类似的东西

微积分，纤维丛，局部线性化。

代数拓扑。
