---
layout:     post
title:      "Java Annotation深入"
subtitle:   "\"Java Annotation study in deep\""
date:       2016-12-16
author:     "Govind"
header-img: "img/post_bg/1.jpg"
tags:
    - Annotation基础
---


## 注解

### Annotation基础与使用

java中元注解有四个：

1. @Retention：注解的保留位置
2. @Target:注解的作用目标
3. @Document：说明该注解将被包含在javadoc中
4. @Inherited：说明子类可以继承父类中的该注解

```
    例子：@interface是一个关键字，在设计annotations的时候必须把一 个类型定义为@interface
    @Target(ElementType.TYPE)
    @Retention(RetentionPolicy.RUNTIME)
    public @interface HttpBaseUrl {
        String value();
    }
```

```
RetentionPolicy
    public enum RetentionPolicy {
       SOURCE,   CLASS,   RUNTIME
      }
```

1. SOURCE，代表这个Annotation类型的信息只会保留在程序源码里，源码如果经过了编译之后，Annotation的数据就会消失,并不会保留在编译好的.class文件里面。
2. ClASS，代表这个Annotation类型的信息保留在程序源码里,同时也会保留在编译好的.class文件里面,在执行的时候，并不会把这一些信息加载到虚拟机(JVM)中去。**注意一下**：当你没有设定一个Annotation类型的Retention值时，系统默认值是CLASS.
3. RUNTIME，表示在源码、编译好的.class文件中保留信息，在执行的时候会把这一些信息加载到JVM中去的．　
举一个例子：如@Override里面的Retention设为SOURCE,编译成功了就不要这一些检查的信息;相反,@Deprecated里面的Retention设为RUNTIME,表示除了在编译时会警告我们使用了哪个被Deprecated的方法,在执行的时候也可以查出该方法是否被Deprecated.

```
ElementType
      public enum ElementType {
        TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL_VARIABLE, ANNOTATION_TYPE,PACKAGE
      }

 Annotation可被用于 packages、types（类、接口、枚举、Annotation类型）、类型成员（方法、构造方法、成员变量、枚举值）、方法参数和本地变量（如循环变量、catch参数）。例如：@Target( { ElementType.METHOD, ElementType.TYPE }),表示注解可被用于方法、类火接口或枚举或Annotation上。
```

Annotation类型里面的参数该怎么设定:

1. 只能用public或默认(default)这两个访问权修饰.例如,String value();这里把方法设为defaul默认类型.
2. 参数成员只能用基本类型byte,short,char,int,long,float,double,boolean八种基本数据类型和String,Enum,Class,annotations等数据类型,以及这一些类型的数组.例如,String value();这里的参数成员就为String.
3. 如果只有一个参数成员,最好把参数名称设为"value",后加小括号.例:上面的例子就只有一个参数成员.

###注解的解析

注解就相当于一个你的源程序要调用一个类，在源程序中应用某个注解，得事先准备好这个注解类。就像你要调用某个类，得事先开发好这个类。**<font color="red">注意我们的Annotation的Retention Policy 必须是RUNTIME，否则我们无法在运行时从他里面获得任何数据。</font>**

当一个Annotation类型被定义为运行时的Annotation后，该注解才能是运行时可见，当class文件被装载时被保存在class文件中的Annotation才会被虚拟机读取。AnnotatedElement 接口是所有程序元素（Class、Method和Constructor）的父接口，所以程序通过反射获取了某个类的AnnotatedElement对象之后，程序就可以调用该对象的如下四个个方法来访问Annotation信息：
- 方法1：**<T extends Annotation> T getAnnotation(Class<T> annotationClass)**: 返回改程序元素上存在的、指定类型的注解，如果该类型注解不存在，则返回null。
- 方法2：**Annotation[] getAnnotations()**:返回该程序元素上存在的所有注解。
- 方法3：**boolean is AnnotationPresent(Class<?extends Annotation> annotationClass)**:判断该程序元素上是否包含指定类型的注解，存在则返回true，否则返回false.
- 方法4：**Annotation[] getDeclaredAnnotations()**：返回直接存在于此元素上的所有注释。与此接口中的其他方法不同，该方法将忽略继承的注释。（如果没有注释直接存在于此元素上，则返回长度为零的一个数组。）该方法的调用者可以随意修改返回的数组；这不会对其他调用者返回的数组产生任何影响。

```
@Documented
@Target({ElementType.METHOD, ElementType.type})
@Retention(RetentionPolicy.RUNTIME)
public @interface MethodInfo {
    String author() default "hupeng";
    String version() default "1.0";
    String date();
    String comment();

}

@MethodInfo(author="exx")
public class AnnotationExample {
    @Override
    @MethodInfo(author = "xxx",version = "1.0",date = "2015/03/26",comment = "override toString()")
    public String toString() {
        return "AnnotationExample{}";
    }

    @Deprecated
    @MethodInfo(comment = "deprecated method", date = "2015/03/26")
    public static void oldMethod() {
        System.out.println("old method, don't use it.");
    }

    @SuppressWarnings({ "unchecked", "deprecation" })
    @MethodInfo(author = "Pankaj", comment = "Main method", date = "Nov 17 2012", version = "1.0")
    public static void genericsTest() {
        oldMethod();
    }
}

//************************************************
public class AnnotationParsing {
    public static void main(String[] args) {
        for (Method method: AnnotationExample.class.getMethods()) {
            if (method.isAnnotationPresent(MethodInfo.class)) {
                for (Annotation annotation:method.getAnnotations()) {
                    System.out.println(annotation + " in method:"+ method);
                }

                MethodInfo methodInfo = method.getAnnotation(MethodInfo.class);

                if ("1.0".equals(methodInfo.version())) {
                    System.out.println("Method with revision no 1.0 = "
                            + method);
                }
            }
        }
        //获取类的注解
        AnnotationExample annotation = (AnnotationExample) AnnotationExample.class.getAnnotation(AnnotationExample.class);
    }
}

```

###注解生命周期

当在Java源程序上加了一个注解，这个Java源程序要由javac去编译，javac把java源文件编译成.class文件，在编译成class时可能会把Java源程序上的一些注解给去掉，java编译器(javac)在处理java源程序时，可能会认为这个注解没有用了，于是就把这个注解去掉了，那么此时在编译好的class中就找不到注解了， 这是编译器编译java源程序时对注解进行处理的第一种可能情况，假设java编译器在把java源程序编译成class时，没有把java源程序中的注解去掉，那么此时在编译好的class中就可以找到注解，当程序使用编译好的class文件时，需要用类加载器把class文件加载到内存中，class文件中的东西不是字节码，class文件里面的东西由类加载器加载到内存中去，类加载器在加载class文件时，会对class文件里面的东西进行处理，如安全检查，处理完以后得到的最终在内存中的二进制的东西才是字节码，类加载器在把class文件加载到内存中时也有转换，转换时是否把class文件中的注解保留下来，这也有说法，**所以说一个注解的生命周期有三个阶段：java源文件是一个阶段，class文件是一个阶段，内存中的字节码是一个阶段**,javac把java源文件编译成.class文件时，有可能去掉里面的注解，类加载器把.class文件加载到内存时也有可能去掉里面的注解，因此在自定义注解时就可以使用Retention注解指明自定义注解的生命周期，自定义注解的生命周期是在RetentionPolicy.SOURCE阶段(java源文件阶段)，还是在RetentionPolicy.CLASS阶段(class文件阶段)，或者是在RetentionPolicy.RUNTIME阶段(内存中的字节码运行时阶段)，根据JDK提供的API可以知道默认是在RetentionPolicy.CLASS阶段 (JDK的API写到：the retention policy defaults to RetentionPolicy.CLASS.)
