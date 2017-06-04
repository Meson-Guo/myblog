---
title: Java中Class对象和实例的详解
date: 2016-10-10 15:23:45
categories: 学习笔记
tags:
- class
description: 详解class实例和对象的不同，及获取Class对象的三种方式
---
<!-- more -->

## Class 对象

什么叫Class对象？什么又叫实例？Class对象是一个对象，但是它很特殊，是用来创建其他对象（java类的实例）的对象。换句话说，Class对象就是java类编译过后所生成的.class文件，里面含有与类相关的所有信息。
实例是通过一般是new关键字调用构造器生成。这里我举个例子来说明一下，我对他的理解。
我声明一个Play类

Play p;//只是声明了一个引用，此时和实例并没有关系。
Play p=new Play();//此处new Play创建了一个实例。并且让让对象的引用和实例关联。

我们知道当我们使用一个类的时候，会有类加载机制把该类的class文件加载到内存。其实更确切的说是第一次创建该类的对象的时候，JVM通过类加载器对该类进行加载。当然我们也能自己获得某个类的class对象。
**获取Class类的三种方法**
### Class.forName("包名+类名")
这种方式我们只需要知道类的名字就可以创建类的对象。
### 类名.class 
它有一个官方的叫法，叫做类字面常量法
### 实例对象.getClass
为了方便对比，我把三种方法写在一个程序里面：
**三个操作类如下**
```
class Banana {
	public Banana() {
		System.out.println("Banana 的构造函数");
	}
	static {
		System.out.println("静态参数初始化Banana");
	}
	{
		System.out.println("非静态参数初始化Banana");
	}
}

class Grape {
	public Grape() {
		System.out.println("Grape 的构造函数");
	}
	
	static {
		System.out.println("静态参数初始化Grape");
	}
	
	{
		System.out.println("非静态参数初始化Grape");
	}
}

public class Apple {
	public Apple() {
		System.out.println("apple 的构造函数");
	}
	static {
		System.out.println("静态参数初始化apple");
	}
	
	{
		System.out.println("非静态参数初始化apple");
	}
}
```
**class对象测试类如下：**
```
public class ClassTest {
		public static void main(String[] args) {

		try {
			System.out.println("*******Class.forName before***********");;
			Class<?> cla = Class.forName("classtype.Apple");
			System.out.println("class.forname :" + cla);
		} catch (ClassNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		System.out.println("*******Class.forName after***********");

		System.out.println("********.class before**********");
		Class<?> banana = Banana.class;
		System.out.println("banana: " + banana);
		System.out.println("**********.class after********");
		System.out.println("**********.getclass before********");
		Grape grape = new Grape();
		System.out.println("grape: " + grape.getClass());
		System.out.println("*********.getclass after*********");
	}
}
```
**控制台输出如下：**
```
*******Class.forName before***********
静态参数初始化Apple
class.forname :class classtype.Apple
*******Class.foName after***********
********.class before**********
banana: class classtype.Banana
**********.class after********
**********.getclass before********
静态参数初始化Grape
非静态参数初始化Grape
Grape 的构造函数
grape: class classtype.Grape
*********.getclass after*********
```
由输出可以看出，只有class.forName和getClass打印了一次静态初始化语句，而.class并没有加载Banana的静态初始化，我们知道static的初始化时跟随类的加载而加载的，所以说这里Banana类没有进行加载。所以类字面常量法创建Class对象时时不会自动初始化该Class对象。
Class.forName只是初始化了类的静态模块，并未调用构造函数或者初始化类的费静态模块。而实例对象.getClass因为它要先产生实例对象，所以在这个过程会对类的静态模块、非静态模块、参数初始化（调用构造函数）。
由以上结果我们可以看到类字面常量法并没有对类进行加载，更谈不上初始化了。如果用这种方法想实现类的加载和初始化，可以使用newInstance()方法。可以生成一个实例等同于new一个对象。
**程序如下：**
```
class Banana {
	public Banana() {
		System.out.println("Banana 的构造函数");
	}
	
	static {
		System.out.println("静态参数初始化Banana");
	}
	
	{
		System.out.println("非静态参数初始化Banana");
	}
}
	测试类如下
public class ClassTest {
		public static void main(String[] args) {
	
		System.out.println("********.class before**********");
		Class<?> banana = Banana.class;
		try {
			Object  ba=banana.newInstance();
			System.out.println("banana: " + ba);
		} catch (InstantiationException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (IllegalAccessException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		
		System.out.println("**********.class after********");
		}
}
```
**控制台输出如下：**
```
********.class before**********
静态参数初始化Banana
非静态参数初始化Banana
Banana 的构造函数
banana: classtype.Banana@2a163765
**********.class after********
```
我们来看看这三种方法对同一个类操作会产生什么样的结果呢？
**看如下程序：**
```
public class Apple {
	public Apple() {
		System.out.println("Apple 的构造函数");
	}
	
	static {
		System.out.println("静态参数初始化Apple");
	}
	
	{
		System.out.println("非静态参数初始化Apple");
	}

	
}
	测试类如下：
public class ClassTest {
	public static void main(String[] args) {

		try {
			System.out.println("*******Class.forName before***********");
			Class<?> cla = Class.forName("classtype.Apple");
			System.out.println("Class.forName :" + cla);
		} catch (ClassNotFoundException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		System.out.println("*******Class.forName after***********");

		System.out.println("********.class before**********");
		Class<?> apple = Apple.class;
		
			System.out.println("Apple: " + apple);
		
		
		System.out.println("**********.class after********");
		System.out.println("**********.getclass before********");
		Apple apple1 = new Apple();
		System.out.println("apple: " + apple1.getClass());
		System.out.println("*********.getclass after*********");
	}
}
```
**控制台输出：**
```
*******Class.forName before***********
静态参数初始化Apple
Class.forName :class classtype.Apple
*******Class.forName after***********
********.class before**********
Apple: class classtype.Apple
**********.class after********
**********.getclass before********
非静态参数初始化Apple
Apple 的构造函数
apple: class classtype.Apple
*********.getclass after*********
```
## 总结
使用Class.forName加载类的时候，静态的模块属性会被初始化，至于非静态模块在new对象实例时才会被初始化。但是我们可以看出new实例对象时并有再次打印静态模块，也就是说此时没有对静态模块进行加载。为什么呢？因为这和类的加载机制有关系当类进行加载的时候，JVM会对该类的class对象进行加载检测，如果已经加载了，就不再加载，如果没有被加载，则通过类加载器进行加载。前面Class.forName已经加载class对象，所以后面new实例对象时就不会再次加载class对象。而静态模块的加载时随着类的加载而加载的。所以才会有一行结果。.class的从结果来看没有对类进行初始化，我们也可以换个说法.class的初始化被延迟到了对静态方法或者非静态方法的首次引用时才执行。另外我们可以看出这三种方式其实就生成了一个Class对象。
