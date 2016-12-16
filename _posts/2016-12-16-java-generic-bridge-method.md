---
layout:     post
title:      "Java泛型与桥方法"
subtitle:   "\"Java Bridge Methd and Java Generics\""
date:       2016-12-16
author:     "Govind"
header-img: "img/post_bg/11.jpg"
tags:
    - Java Bridge Methd and Java Generics
---


## Java桥方法与泛型

>Java语言规范:"Any constructs introduced by the compiler that do not have a corresponding construct in the source code must be marked as synthetic, except for default constructors and the class initialization method."

Java中的桥方法是合成方法（synthetic methods），合成方法对于实现Java语言特征是必需的。最广为人知的例子就是协变返回类型和泛型中的案例，在泛型中案例基方法的参数被擦除后与实际被调用的方法不同时会使用到桥方法。

### JVM如何理解Java泛型类

```
    public class Pair<T> {
           private T first=null;
           private T second=null;

           public Pair(T fir,T sec) {
                this.first=fir;
            this.second=sec;
           }
           public T getFirst() {
                 return this.first;
           }
           public T getSecond(){
                 return this.second;
           }
           public void setFirst(T fir) {
             this.first=fir;
           }
    }

```

1. 创建泛型类对象

```
        Pair<String> pair1=new Pair("string",1);           ...①
        Pair<String> pair2=new Pair<String>("string",1)    ...②
```

其中第一个行代码在编译起见不会报错，而第二行在编译时会报错；下面分析编译泛型时候的过程，**JVM本身并没有泛型对象这样的一个特殊概念。所有的泛型类对象在编译器会全部变成普通类对象**，①,②两个代码编译器全部调用的是Pair(Object fir, Object sec)，第一行代码编译时没有问题是因为"String"和1都是Object类型，但是当调用pair1.getSecond()方法时，JVM会根据第一个参数"string"推断出T的类型是String，因此getSecond方法应该返回String类型，而事实上得到确实Integer类型，因此不符合JVM运行要求，终止程序并报错；第二行代码会在编译时就报错，因为指明了创建对象是应该是String类型，编辑期编译器会检查参数是否符合要求，因此会直接报错。

<font color="blue">**总结**：在创建泛型对象时，要指定泛型类型T的具体类型，让错误在编译期就暴露而避免在JVM运行时出错。</font>

2. JVM如何理解泛型概念--类型擦除(Type Erasure)
JVM并不知道泛型的存在，因为泛型在编译阶段就已经被处理成普通的类和方法；处理机制是通过类型擦除，擦除规则：
- 若泛型类型没有指定具体类型<T>，用Object作为原始类型；
- 若有限定类型<T extends XClass>，使用XClass作为原始类型；
- 若有多个限定<T exnteds XClass1 & XClass2>，使用第一个边界类型XClass1作为原始类型；

例如：上述泛型类Pair<T>编译后的结果为
```
        //编译阶段：类型变量的擦除
        public class Pair {
               private Object first=null;
               private Object second=null;

               public Pair(Object fir,Object sec) {
                   this.first=fir;
                   this.second=sec;
               }
              public Object getFirst(){
                   return this.first;
              }
              public void setFirst(Object fir) {
                   this.first=fir;
              }
           }
```

###  继承泛型类型的多态方法问题

问题：子类没有覆盖住父类的方法
解决方法：编译器生成桥方法

```
        class SonPair extends Pair<String> {
                  public void setFirst(String fir){....}
        }
```

Pair<String>在编译的时候已经被类型擦除，Pair的setFirst方法变为了setFirst(Object fir)，这样SonPair的setFirst(Stirng fir)方法就无法覆盖父类中的setFirst(Object fir)方法，因为参数不同，不是同一个方法；
为了解决多态中的这个问题，编译器会自动在SonPair中生成一个桥方法(Bridge Method)：

```
         public void setFirst(Object fir) {
                           setFirst((String) fir)
         }
```

这样SonPair的桥方法会覆盖泛型父类中的setFirst(Object fir)方法，桥内部调用的是子类中定义的setFirst(String fir)方法；

同样若我们先覆盖父类中的getFirst方法，编译器会自动生成一个桥方法，如下：

```
     String getFirst()   // 自己定义的方法
     Object getFirst()  //  编译器生成的桥方法
```

<font color="red">编译器允许出现方法签名相同的多个方法存在于一个类中吗？</font>事实上，编译器是通过方法签名(方法名+参数列表)判断方法是否一样；然而存在一种灰色地带即**只有编译器自己能够创建出方法签名一样而返回类型不同的方法**，如果编译出了这样的方法，在执行时，<font color="blue">JVM会用参数类型和返回类型来确定一个方法，JVM能够根据不同返回类型来确定方法签名相同的方法。</font>

总结：在继承泛型类型的时候，桥方法的合成是为了避免类型变量擦除所带来的多态灾难；当一个类实现了一个参数化的接口或是继承了一个参数化的类时，需要引入桥方法
