---
layout: post
title:  "Java 经验法则11 谨慎的复写clone"
date:   2014-01-16
image: password.jpg
description: 容易出错的一个方法
categories: [android, java]
tags: [android, java, clone]

---

Java的cloneable是Java对象的一种拷贝技术，类似于serializable。在Java中，所有的对象都是存储与堆中的，cloneable就是在堆中新建一个与原来对象完全相同的对象。Java中实现cloneable有以下几个步骤：

1. 要进行cloneable的类必须实现Cloneable接口
2. 如果有必要，重写Object类的clone()方法。
3. 在重写的clone()方法中，注意类所引用对象的深拷贝与浅拷贝问题。

clone方法的通俗约定：

创建和返回该对象的一个拷贝。这个拷贝的精确含义取决于该对象的类。一般的含义是，对于任何对象x，表达式

`x.clone() != x`

将会是true，并且表达式

`x.clone().getClass() == x.getClass()`

将会是true，但这些都不是绝对的要求。虽然通常情况下，表达式

`x.clone().equals(x)`

将会是true，但是，这也不是一个绝对的要求。拷贝对象往往会导致创建它的类的一个新实例，但它同时也会要求拷贝内部的数据结构。这个过程中没有调用构造方法。

这个约定存在几个问题。不调用构造方法的规定太强硬，行为良好的clone方法可以调用构造方法来创建对象，构造之后再复制内部数据。如果这个类是final的，clone甚至可能会返回一个由构造方法创建的对象。

