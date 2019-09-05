---
title: 面试知识点汇总
date: 2019-09-04 09:27:46
updated: 2019-09-04 09:27:46
tags: 面经
categories: Java

---

### 自动拆装箱

- 为什么Java中有自动拆装箱

Java是一门面向对象的语言，如果只有八种基本类型`byte`,  `short`,`int`, `long`, `float`, `double`, `char`, `bool`，在计算上是简单的，但是在面向对象的操作上是麻烦的，比如向集合类添加元素就只能添加包装类型而不能添加基本类型。反之，包装类型是对象，存放在堆上，占用空间大，而基本类型存放在栈中。

- 包装类型和基本类型之间的比较

包装类型和包装类型比较，比较的是对象的地址；包装类型和基本类型比较，是先将包装类型拆箱再同基本类型比较。



### `String`, `StringBuffer` 和 `StringBuilder`

#### 1. 比较

`String`定义的字符串，不可修改；`StringBuffer`和`StringBuilder`定义的字符串，可以修改，但是`StringBuffer`是线程安全的。

解释：

上述三者底层都是通过定义了一个字节数组`byte[] value`来实现的，不同的是,对于`String`类型，数组被`final`修饰，不可修改，而`StringBuffer`和`StringBuilder`没有这个修饰。

`StringBuffer`和`StringBuilder`内容都可以修改，但是`StringBuffer`是线程安全的，因为它的`append()`方法被[`synchronized`](#synchronized)关键字修饰。

#### 2. 关于`String`

- `String s = new String("taochq");`定义了几个对象？

检查字符串常量池是否有`“taochq”`这个变量，有则返回其引用，此时定义了一个对象，没有则先创建后返回，定义了两个对象。

- 如何理解`String`的`intern`方法

检查当前字符串常量是否在常量池中，不在的话放入常量池并返回引用，在的话直接返回引用。它更多的是用于将那些只有在运行时才能确定的字符串放入常量池。

- `String s = "a"+"b"`定义了几个对象

一个，编译器进行了优化，就是`"ab"`。

- String s= new String("tao")+"chq"定义了几个对象

如果常量池中没有`"tao"`和`"chq"`的话，三个。

jdk7以后，字符串常量池放在了堆中。



### `equals `和 `==`

#### 1. `==`

对于基本数据类型，`==`比较的是值

对于医用数据类型，`==`比较的是内存中的存放地址

#### 2. `equals`

`equals()`方法是`Object`类中的方法，其最初的实现是

```
public boolean equals(Object obj) {
        return (this == obj);
}
```

所以其最初比较的也是引用的地址，但是后来有些继承类比如String，有了自己的实现，用来比较内容了。



### `equals()` 和 `hashcode()`

#### 1. 缘起

这两个方法都是定义在万类之祖------`Object`类中的方法，所以每个类都继承到了并且可以重写。

上文中已经讲到了`equals()`方法，其最开始也是比较两个对象的地址的，但是`String`等对象重写了，用来比较内容。

`hashcode()`最初的实现是用c++实现的，它返回每个对象的内存地址。

#### 2. 关系

一般来讲，这两个方法没有太大的关系，我们自己实现的一些类可能会需要重写`equals()`方法,对于重写`equals()`方法，有以下要求：

```
1. 对称性：如果x.equals(y)返回是"true"，那么y.equals(x)也应该返回是"true"。
2. 反射性：x.equals(x)必须返回是"true"。
3. 类推性：如果x.equals(y)返回是"true"，而且y.equals(z)返回是"true"，那么z.equals(x)也应该返回是"true"。
4. 一致性：如果x.equals(y)返回是"true"，只要x和y内容一直不变，不管你重复x.equals(y)多少次，返回都是"true"。
5. 非空性，x.equals(null)，永远返回是"false"；x.equals(和x不同类型的对象)永远返回是"false"。
```

那么`hashcode()`用在哪里呢？一个最广泛的用途就是哈希表，比如`HashMap`,`HashSet`和`HashTable`,在哈希表中，哈希值用来当作索引，所以考虑以下：对于两个相等（`equals()`返回`true`）的对象，它们在哈希表中应该只存在一个，对于两个不相等但是哈希值相等的对象，它们在哈希表索引相同，这种情况下，如果不重写`hashcode()`,那它们计算出来的值不一样（因为`hashcode()`本来返回的是内存地址），这就不是我们想要的哈希表了。所以说，**在`HashMap中重写equals()`也要重写`hashcode()`**.



### Java集合类





### `synchronized`

