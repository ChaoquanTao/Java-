---
title: 面试知识点汇总
date: 2019-09-04 09:27:46
updated: 2019-09-04 09:27:46
tags: 面经
categories: Java
---

> 持续更新到2020校招结束

### 自动拆装箱

#### 为什么Java中有自动拆装箱

Java是一门面向对象的语言，如果只有八种基本类型`byte`,  `short`,`int`, `long`, `float`, `double`, `char`, `bool`，在计算上是简单的，但是在面向对象的操作上是麻烦的，比如向集合类添加元素就只能添加包装类型而不能添加基本类型。反之，包装类型是对象，存放在堆上，占用空间大，而基本类型存放在栈中。

#### 自动拆装箱中的缓存机制

在自动装箱的时候，高频区域的数值存在缓存，会直接使用已有的对象。各个包装类型的缓存范围如下

| 包装类型  | 缓存范围 |
| --------- | -------- |
| Byte      | -128~127 |
| Short     | -128~127 |
| Character | 0~127    |
| Long      | -128~127 |
| Integer   | -128~127 |

看以下代码：

```
public static void main(String[] args) {
        Integer a = 100;
        Integer b = 100;
        System.out.println(a==b);
        System.out.println(a.equals(b));

        Integer c= 200;
        Integer d = 200;
        System.out.println(c==d);
        System.out.println(c.equals(d));
    }
```

输出：

```
true
true
false
true
```

`==`比较的是对象地址，`equals`比较的是值。可以发现，在缓存范围内，对象a和b的地址是相同的。

#### 包装类型和基本类型之间的互相比较

+ 包装类型和包装类型比较，`==`比较的是对象的地址, `equals`比较的是值；
+ 包装类型和基本类型比较，无论是`==`还是`equals`，比较的都是值，先将包装类型拆箱再同基本类型比较。



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

#### `HashMap` `ConcurrentHashMap`

[Java集合类之HashMap](<https://chaoquantao.github.io/2019/07/09/%E8%B0%88%E4%B8%80%E8%B0%88Java%E9%9B%86%E5%90%88%E7%B1%BB%E4%B9%8BHashMap/>)

#### `TreeMap`

底层由红黑树实现。映射根据其键的自然顺序进行排序，或者根据创建映射时提供的 Comparator进行排序，具体取决于使用的构造方法。所以是个有序的key-value集合。

#### 常见集合类接口

set, list, map

其中set, list都实现了Collection接口， 而map没有

 Collection   
├List   
│├`LinkedList   `
│├`ArrayList   `
│└Vector   
│　└Stack   
└Set 

#### Collection 和 Collections

Collection是顶层接口，而Collections是一个工具类，里面提供了一些静态方法，比如排序啊，逆序啊等等，不能被实例化。

#### `ArrayList`, `LinkedList` 和Vector

`ArrayList`底层由数组实现，查找快，但是删除和中间插入牵扯到其他元素的移动，速度慢；

`LinkedList`底层由链表实现，删除和插入方便，但是查找起来没有`ArrayList`快；

Vector线程安全，它的修改结构的方法由synchronized关键字修饰，并且它可以往里面插入不同类型的数据。它的扩容粒度和`ArrayList`也不一样，`ArrayList`扩容1.5倍，Vector扩容一倍或者增量扩容传入的参数。Vector是Java中的遗留容器。

#### `HashMap` 和 `HashTable`

它们都实现了Map接口，

`HashTable`的方法由synchronized修饰，是线程安全的，

`HashTable`键值不能为空，而`HashMap`可以。

#### fail fast 和 fail safe

它俩都是Java集合中的错误检测机制。

当多个线程对非fail safe的集合进行结构上的修改时，就有可能产生fail fast机制，这时候会抛出`ConcurrentModificationException`异常。事实上单线程环境下也有可能出现该异常，主要是由于`modCount`和`expectedModCount`不一致造成的。

fail safe对集合结构的修改是在集合的一个复制品上进行的，所以不会抛出上述错误。但是这种方式更耗内存，而且并不能保证读到的数据是原始数据。

[Java中的fail fast]([https://inewbie.top/2019/09/29/Java%E4%B8%AD%E7%9A%84fail-fast/#more](https://inewbie.top/2019/09/29/Java中的fail-fast/#more))

#### Iterator 和 `ListIterator`

开局一张图

<img src="https://s2.ax1x.com/2019/09/29/uGlP78.png" alt="uGlP78.png" style="zoom:80%;" />

`ListIterator`是List专有的迭代器，继承自Iterator, 前向后向都可以访问（向前访问也是基于当前位置的，所以当前位置如果是0的话，你懂的），并且还多了set, add等方法

Iterator除了List可以用，Set也可以用，只能后向访问，且只能remove，不能add,可以看出`ListIterator`更强大一些（毕竟也是继承下来的对吧），在list中，给两种迭代器都提供了方法。

### 并发

#### Java中的锁

[Java中的锁](<https://chaoquantao.github.io/2019/09/13/Java%E4%B8%AD%E7%9A%84%E9%94%81/>)

#### `synchronized`

[Java中的synchronized关键字](https://chaoquantao.github.io/2019/09/13/Java%E4%B8%AD%E7%9A%84synchronized%E5%85%B3%E9%94%AE%E5%AD%97/)



