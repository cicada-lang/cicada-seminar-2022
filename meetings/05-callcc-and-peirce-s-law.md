---
title: call/cc and Peirce's Law
date: 2022-07-31
---

# 先给出一个结论：

call/cc 与 Peirce’s law 对应
只有 lam, app, var 的 stlc 与直觉主义逻辑对应
加上 call/cc 后则对应于经典逻辑

# 先讲什么是 CPS

要讲 call/cc 是什么，首先得先理解 CPS 变换。

CPS(continuation-passing style) 是一种现式地操作控制流的编码风格，与之对应的则叫做 direct style

``` lisp
;; 比如对于这段代码
(+ (* 3 2)
   (+ 4 1)
   5)

;; 3 对应的 context 就是
(+ (* _ 2)
   (+ 4 1)
   5)

;; 写成函数的形式就是
(λ (x) (+ (* x 2)
          (+ 4 1))
          5)
;; 我们称其为 continuation(延续)
;; 通常我们会称 x 为 hole
```

CPS 版本的代码会将延续现式地传给函数，函数将返回值直接传给延续。这样讲可能有点抽象，我们可以看看实例。

首先假定 `+&` 和 `*&` 是 `+` 和 `*` 的 cps 版本，它们会比原来多一个参数（延续）。

于是上面这段代码的 CPS 版本会变成这样。

``` lisp
(λ (k)
  (*& 3 2 (λ (x1)
    (+& 4 1 (λ (x2)
      (+& x1 x2 5 k)))))
```

再给出一个 factorial 一个 fibonacci 作为参考

``` lisp

(define (fact n)
  (if (= n 0)
      1
      (* n (fact (- n 1)))))

(define (fact& n k)
  (if (= n 0)
      (k 1)
      (fact& (- n 1)
        (λ (x) (k (* x n))))))

(define (fib n)
  (if (<= n 1)
      1
      (+ (fib (- n 1))
         (fib (- n 2)))))

(define (fib& n k)
  (if (<= n 1)
      (k 1)
      (fib& (- n 1)
        (λ (x1) (fib& (- n 2)
                  (λ (x2) (k (+ x1 x2))))))))
```

# 然后是 call/cc

(call/cc func&) 会把当前表达式中 call/cc 对应的 context 取出来，作为 continuation 传给 func&。

``` lisp
(define (func& k)
  (k 2)
  3)

(+ 1 (call/cc func&))
;; result: 3
```

# 回到逻辑系统上

我之前一直在想 call/cc 在语义上和 Peirce’s law 对应的地方...

但是实际上 call/cc 除了类型恰好是 ((P -> Q) -> P) -> P 之外没有什么特别之处。

那么...现在除了分析一下 call/cc 的类型以外我们**无事可做**。

首先我们假定 hole 的类型为 P ，那么 call/cc 的类型就是 @TypeOf(func&) -> P

然后 k 的参数是 hole；返回值是整个 continuation 的返回值，可以完全于 P 无关，所以我们用一个新的变量 Q。

所以现在 call/cc 的类型可以被确定到 ((P -> Q) -> _) -> P

最后的空位可以借助 call/cc 的一种特殊情况处理：我们可以接收一个 continuation，但不把任何值传递给 continuation，这样函数就会像普通函数一样正常的结束。

``` lisp
(define (func& k)
  (+ 2 3))

(call/cc func&)
;; result: 5
```

由此我们可以知道，`func&` 的返回值类型应与 hole 相同，所以整个 call/cc 的类型就是 ((P -> Q) -> P) -> P。
