---
title: "Test"
date: 2021-06-26T16:08:07+09:00
draft: false
---

てすと

ああああ
| key | value |
|-----|-------|
| k   | v  !  |

```scala
sealed trait Exp[A]
case object True extends Exp[Boolean]
case object Flase extends Exp[Boolean]
case class Eq(e1: Exp[Int], e2: Exp[Int]) extends Exp[Boolean]
```
