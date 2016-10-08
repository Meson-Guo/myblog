---
title: Java 中AccessibleObject那些事儿
date: 2016-07-29 11:19:29
categories: 技术类
tags:
- java
- AccessibleObject
description: java 中AccessibleObject 和反射机制
---

<!--more-->

以前我很少写东西，感觉自己知道就可以了嘛，最近我改变了我的看法，把某些东西写出来可以让我们梳理一些对它的理解，更甚至有时候你会发现你看问题的盲点！

好了，进入正题，今天研究了一段代码，就是如何改变final类型的值。大家知道final类型的值一般情况下是不能改变的，但是经过楼主的不断尝试。发现它也是可以改变的。


这里我是用了AccessibleObject  ，在进行详细讲解之前，先来说一说AccessibleObject的相关知识。

public class AccessibleObject extends Object implements AnnotatedElement

AccessibleObject 类实现了AnnotatedElement，它是 Field、Method 和 Constructor 对象的基类。它提供了将反射的对象标记为在使用时取消默认 Java 语言访问控制检查的能力。对于公共成员、默认（打包）访问成员、受保护成员和私有成员，在分别使用 Field、Method 或 Constructor 对象来设置或获得字段、调用方法，或者创建和初始化类的新实例的时候，会执行访问检查。

AccessibleObject 的方法：
##isAccessible：

	public boolean isAccessible()获得此对象的 accessible 标志的值。

此对象的返回值 就是accessible的标志值，一般情况下无论 public，private,protected,默认等修饰的属性的access值均为false（注意他的意思并非是访问权限而是对该自己执行安全检查）。

##setAccessible：
```
	public static void setAccessible(object 　,　boolean flag) throws SecurityException
```

使用单一安全性检查（为了提高效率）为一对象设置 accessible 标志。如果存在安全管理器，则在 ReflectPermission("suppressAccessChecks") 权限下调用 checkPermission 方法。当flag 为 true，表示不开启安全检查，但是不能更改输入 object的任何元素的可访问性（例如，如果元素对象是 Class 类的 Constructor 对象），则会引发 SecurityException。如果发生 SecurityException，对于少于（不包括）发生异常的元素的数组元素，可以将对象的可访问性设置为 flag；对于超出（包括）引发异常的元素的那些元素，则不更改其可访问性.

##getAnnotation：
```
	public <T extends Annotation> T getAnnotation(Class<T> annotationClass)
```
从接口 AnnotatedElement 复制的描述 .如果存在该元素的指定类型的注释，则返回这些注释，否则返回 null。 
指定者：接口 AnnotatedElement 中的 getAnnotation 　　
参数： annotationClass - 对应于注释类型的 Class 对象 　　
返回：如果该元素的指定注释类型的注释存在于此对象上，则返回这些注释，否则返回 null

##isAnnotationPresent：
```
　　public boolean isAnnotationPresent(Class<? extends Annotation> annotationClass)
```
从接口 AnnotatedElement 复制的描述 ,如果指定类型的注释存在于此元素上，则返回 true，否则返回 false。此方法主要是为了便于访问标记注释而设计的。
指定者：接口 AnnotatedElement 中的 isAnnotationPresent 　　
参数：annotationClass - 对应于注释类型的 Class 对象 　　
返回：如果指定注释类型的注释存在于此对象上，则返回 true，否则返回 false

##getAnnotation：
　　public Annotation[] getAnnotations()从接口 AnnotatedElement 复制的描述 ,返回此元素上存在的所有注释。（如果此元素没有注释，则返回长度为零的数组。）该方法的调用方可以随意修改返回的数组；这不会对其他调用方返回的数组产生任何影响。 　　指定者：接口 AnnotatedElement 中的 getAnnotations 　　
返回： 此元素上存在的所有注释

##getDeclaredAnnotation：
	public Annotation[] getDeclaredAnnotations()从接口 AnnotatedElement 复制的描述 　　返回直接存在于此元素上的所有注释。与此接口中的其他方法不同，该方法将忽略继承的注释。（如果没有注释直接存在于此元素上，则返回长度为零的一个数组。）该方法的调用方可以随意修改返回的数组；这不会对其他调用方返回的数组产生任何影响。 　　
指定者： 接口 AnnotatedElement 中的 getDeclaredAnnotations 　　
返回： 直接存在于此元素上的所有注释
###我创建了一个Student的model:
```
	public class Student {
	private String name;
	private String no;
	public String nickname;

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public String getNo() {
		return no;
	}

	public void setNo(String no) {
		this.no = no;
	}

	public String getNickname() {
		return nickname;
	}

	public void setNickname(String nickname) {
		this.nickname = nickname;
	}
}

```
###首先：
```
	public class Reflect {
		public static void main(String[] args) {
			Student stu=new Student()；
			stu.setName("gzq");
			stu.setNickname("jiu ge");
			stu.setNo("100901016");
			Field field=stu.getClass().getDeclaredField("name");
			Field field1=stu.getClass().getField("name");
               field.isAccessible();
			field.setAccessible(true);
			field.set(stu, "九哥");
			System.out.println("field.get(stu):"+field.get(stu));
			 }
		}
```

###这样打印出的结果：
```
	java.lang.NoSuchFieldException: name
at java.lang.Class.getField(Class.java:1537)
at field.Reflect.main(Reflect.java:15
```
###原因是
getField不能访问private修饰的属性。
###将其改为：
```
Field field=stu.getClass().getDeclaredField("name");
Field field1=stu.getClass().getField("nickname");
```
正常操作：打印出的结果是：九哥，而不是jiuge,这说明值确实变了。
如果不放心，可以在加一段代码：
```
import java.lang.reflect.Field;
import java.util.Date;

public class Reflect {
	public static void main(String[] args) {
		Student stu = new Student();

		stu.setName("gzq");
		stu.setNickname("jiu ge");
		stu.setNo("100901016");
		try {
			Field field = stu.getClass().getDeclaredField("name");
			Field field1 = stu.getClass().getField("nickname");// getfield
																// 只能获取public字段
			System.out.println("field:" + field);
			System.out.println("field1:" + field1);
			System.out.println("field.isAccessible:" + field.isAccessible());
			field.isAccessible();
			field.setAccessible(true);
			field.set(stu, "九哥");
			System.out.println("field.get(stu):" + field.get(stu));
			System.out.println("stu.getName():" + stu.getName());

		} catch (NoSuchFieldException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (SecurityException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (IllegalArgumentException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		} catch (IllegalAccessException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
}
```
通过加了属性的getName方法看一看：打印结果：
```
field:private java.lang.String field.Student.name
field1:public java.lang.String field.Student.nickname
field.isAccessible:false
field.get(stu):九哥
stu.getName():九哥
```
getName()获得的值确实是修改后的值

总结;仔细想了一下，其实这里的name是string 类型的，我看了一下string的源码，发现string的value 是final类型

一般final类型是没办法修改的，但是通过这种方法可以进行修改的。

