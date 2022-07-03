---
title: SICP学习笔记-用高阶函数做抽象
tags:
  - SICP
  - lisp
date: 2018-05-11 11:07:44
---


## 前言

人们对于功能强大的程序设计语言有一个必然的要求，就是能够为公共模式命名，建立抽象，然后直接在抽象的层次上面工做。过程提供了这种能力，这也是为什么除了最简单的那些语言外，其他语言都包含定义过程的机制的原因。

然而，即使在数值计算过程中，如果将过程限制为只能以数作为参数，那么也会严重地限制我们建立抽象的能力。经常存在一些相同的程序设计模式能够用于若干不同的过程。为了将这种模式描述为相应的概念，我们就需要构造这样的过程，让它们以过程作为参数，或者以过程为返回值，这类能操作过程的过程称为 *高阶过程* 。

<!--more-->

### 过程作为参数

```
;求整数的和
(define (sum_int a b)
  (if (> a b)
      0
      (+ a (sum_int (+ a 1) b))))

;求整数的三次幂的和
(define (sum_cube_int a b)
  (if (> a b)
      0
      (+ (cube a) (sum_cube_int (+ a 1) b))))

(define (cube x) (* x x x))

;逼近pi
(define (pi_sum a b)
  (if (> a b)
      0
      (+ (/ 1.0 (* a (+ a 2))) (pi_sum (+ a 4) b))))
```

这里可以看出这三个过程采用同一种公用的基础模式，它们的很大一部分是可以公用的，按照下面的模式编写代码既可以实现公用下面代码。
```
(define (sum term a next b)
  (if (> a b)
      0
      (+ (term a)
         (sum term (next a) next b))))
```

> 这里增加了过程参数 term 和 next

用这种方式去定义sum_cube_int如下
```
(define (inc a)
  (+ a 1))

(define (sum_cube_int a b)
  (sum cube a inc b))
```

### 用lambda构造过程

如果不需要显示的定义某些简单的过程，这里引入一种lambda的特殊形式完成这类描述，这种特殊形式能够创建所需要的过程。（理解为内部函数）

一般而言lambda与define创建过程完全一样，但不为有关过程提供过程名字。格式如下：

```
(lambda (过程参数) <过程体>)
```

### 使用let创建局部变量

let表达式的一般形式是：
```
(let ((<var_1> <exp_1>)
     (<var_2> <exp_2>)
     .
     .
     .
     (<var_n> <exp_n>))
  <body>)
```
这里由let表达式描述的变量的作用域就是该let的体，这表明：

  * let能让我们可以在尽可能接近其使用的地方建立局部变量的约束。
  * 变量的值是在let表达式之外计算的。

### 过程作为一般性的方法

在前面介绍了复合过程，是作为一种将若干操作的模式抽象出来的机制，使所描述的计算不再依赖于所涉及的特定的数值。有了高阶过程，这里将看到一种更加强大的抽象。

#### 通过区间折半法寻找方程的根

```
;检测x和y是否足够接近
(define (close_enough? x y)
  (< (abs (- x y)) 0.001))

;通过区间折半法求方程的根
(define (half_interval_method f a b)
  (let  ((a_value (f a))
        (b_value (f b)))
    (cond ((and (negative? a_value) (positive? b_value))
           (search f a b))
          ((and (negative? b_value) (positive? a_value))
           (search f b a))
          (else
            (error "Value are not of opposite sign" a b)))))

;求平均值函数
(define (average a b)
  (exact->inexact (/ (+ a b) 2)))

;遍历函数
(define (search f neg_point pos_point)
  (let ((midpoint (average neg_point pos_point)))
    (if (close_enough? neg_point pos_point)
        midpoint
        (let ((test_value (f midpoint)))
          (cond ((positive? test_value)
                  (search f neg_point midpoint))
                ((negative? test_value)
                  (search f midpoint pos_point))
                (else midpoint))))))

(half_interval_method sin 2.0 4.0)
```

#### 找出函数的不动点

```
;允许偏差值
(define tolerance 0.00001)

;找出函数的不动点
(define (fixed_point f first_guess)
  (define (close_enough? v1 v2)
    (< (abs (- v1 v2)) tolerance))
  (define (try guess)
    (let ((next (f guess)))
      (if (close_enough? guess next)
          next
          (try next))))
  (try first_guess))

(fixed_point cos 1.0)
```

### 过程作为返回值

将过程作为参数传递，能够显著的增强我们的程序设计语言的表现能力。通过创造另外一种返回值为过程的过程，还需要得到进一步的表达能力。当我们利用这些抽象描述该计算过程时，其中的想法如何变得更加清晰。将一个计算过程形式化为一个过程。

#### 牛顿法

```
;求导数
(define dx 0.00001)

(define (deriv g)
  (lambda (x)
    (/ (- (g (+ x dx)) (g x)) dx)))

(define (cube x) (* x x x))

((deriv cube) 5)

;通过牛顿法求不动点
(define (newtons_transform g)
  (lambda (x)
    (- x (/ (g x) ((deriv g) x)))))

(define (newtons_method g guess)
  (fixed_point (newtons_transform g) guess))
```

#### 抽象和一级过程

作为编程者，我们应该对这类可能性保持敏感，设法从中识别出程序的基本抽象，并基于这些基本抽象去进一步构造，并推广它们以创造威力更加强大的抽象。当然，这并不是说总应该采用尽可能的抽象方式去编写程序，程序设计专家们知道如何根据工作中的情况去选择合适的抽象层次。但是能基于这种抽象去思考确实是重要的。只有这样才可能在新的上下问中去应用它们。高阶过程的重要性就在于使我们能够显式的应用程序设计语言要素去描述这些抽象，使得我们能够像操作其他的计算元素一样去操作它们。

一般而言，程序设计语言总会对计算元素的可能使用方式强加上某些限制。带上最少限制的元素被称为具有 *第一级* 的状态。第一级元素的某些“权利或者特权”包括：

  * 可以使用变量名；
  * 可以提供给过程作为参数；
  * 可以由过程作为结果返回；
  * 可以包含在数据结构中；
