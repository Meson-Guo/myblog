---
title: Java之代理篇
date: 2016-10-08 18:00:45
categories: 学习笔记
tags:
- java 
- Proxy
description: 代理是基本的设计模式之一，他是为你提供不同的操作，用插入的代替实际的对象的对象。动态代理可以动态的创建代理并动态的处理对所代理方法的调用
---
<!-- more -->

## 代理

如果说在某些情况下，比如说需要隐藏实现，解藕等等，我们不想直接访问某一个对象，我们可以采用代理的方法来达到我们的目的。代理主要分为静态代理和动态代理。

### 静态代理
静态代理即为在程序运行之前，代理已经存在了。主要由开发人员编写或者是编辑器生成的代理都可以叫做静态代理。
举个例子：
定义一个接口
```
	public interface Interface {
	void doSomthing();
	void sayhello(String args);
}
```
定义一个委托类实现这个接口，即为被代理的类
```
public class RealObject implements Interface {

	@Override
	public void doSomthing() {
		// TODO Auto-generated method stub
		System.out.println("do something");
	}

	@Override
	public void sayhello(String args) {
		// TODO Auto-generated method stub
		System.out.println("hello :"+args);
	}

}
```
定义代理类，且实现Interface接口
```
public class SimpleProxy implements Interface {
	Interface proxied;
	public SimpleProxy(Interface proxied) {
		// TODO Auto-generated constructor stub
		this.proxied=proxied;
	}
	@Override
	public void doSomthing() {
		// TODO Auto-generated method stub
		System.out.println("simpleproxy do something");
		proxied.doSomthing();

	}

	@Override
	public void sayhello(String args) {
		// TODO Auto-generated method stub
		System.out.println("simpleproxy hello "+args);
		proxied.sayhello(args);

	}

}
```
proxy 测试类
```
public class SimpleProxyTest {
	public static void consume(Interface it){
		it.doSomthing();
		it.someThingElse("Meson");
	}
	public static void main(String[] args) {
		RealObject real=new RealObject();
		consume(real);
	}
}
```

### 动态代理
动态代理又分为两种：
1、JDK动态代理。这种代理只能代理实现接口的类。
2、cglib动态代理可以用于没有实现接口的类，cglib的原理就是对指定的目标类生成一个子类，覆盖其中方法实现增强，由于采用的是继承的关系，所以final修饰的类就无法代理。
当然这里我们只谈JDK代理，关于cglib后期补上。
#### JDK动态代理
如果你学过spring的话，那你应该知道spring有两大核心，一是IOC，也叫依赖注入。另一个是AOP。可能我们会用sping中的AOP思想，但是我们可能并不知道它的到底是怎么实现的。其实AOP就是一个典型的动态代理机制。
动态代理有两个很重要的类或接口，一是InvocationHandler接口，而是Proxy类。
1、InvocationHandler接口
InvocationHandler接口是负责链接代理类和被代理类的中间类必须实现的接口。我们可以先看一下JDK文档里是如何描述这个接口的
```
	InvocationHandler is the interface implemented by the invocation handler of a proxy instance. 

	Each proxy instance has an associated invocation handler. When a method is invoked on a proxy instance, the method invocation is encoded and dispatched to the invoke method of its invocation handler.
```
每一个实现InvocationHandler接口的类必须实现接口中唯一的一个方法invoke，这个方法主要负责转发代理对象对被代理对象方法的调度动作。
实现一个invoke方法：
```
	Object invoke(Object proxy, Method method, Object[] args) throws Throwable{//TODO}
```
TODO的内容这里我先说一下，这里主要实现invoke调度动作，并且可以在调度动嘴之前或者之后可以加上我们自己想做的事情。
我们看到invoke方法有三个参数：
proxy: 指被代理的对象，通过 Proxy.newProxyInstance() 生成的代理类对象
method: 代理对象被调用的函数
args: 代理对象被调用的函数的参数
接下来会有实例说明
2、Proxy类
JDK文档说明如下：
```
	Proxy provides static methods for creating dynamic proxy classes and instances, and it is also the superclass of all dynamic proxy classes created by those methods. Returns an instance of a proxy class for the specified interfaces that dispatches method invocations to the specified invocation handler.
```
作用就是创建动态的代理对象
生成动态代理有两种方法，一是使用newProxyInstance方法，而是使用getProxyClass方法。大都是使用newProxyInstance方法来生成动态代理
```	
	public static Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces,  InvocationHandler h)  throws IllegalArgumentException
```
这里也有三个参数，
loder: 类加载器，定义了由那个类加载器对生成的代理对象进行加载。
interfaces: 一个接口数组，代理对象要实现的接口
h: InvocationHandler实现类对象，负责连接代理类和委托类的中间类
接下来我们写个例子来看一看：
定义一个接口：
```
	public interface Interface {
	void doSomthing();
	void sayhello(String args);
}
```

定义代理类，且实现Interface接口
```
public class SimpleProxy implements Interface {
	Interface proxied;
	public SimpleProxy(Interface proxied) {
		// TODO Auto-generated constructor stub
		this.proxied=proxied;
	}
	@Override
	public void doSomthing() {
		// TODO Auto-generated method stub
		System.out.println("simpleproxy do something");
		proxied.doSomthing();

	}

	@Override
	public void sayhello(String args) {
		// TODO Auto-generated method stub
		System.out.println("simpleproxy hello "+args);
		proxied.sayhello(args);

	}

}
```
定义动态代理类且必须实现InvocationHandler接口
```
	public class DynamicProxy implements InvocationHandler {
	private Object proxied;
	public DynamicProxy(Object proxied) {
		this.proxied=proxied;
		// TODO Auto-generated constructor stub
		//构造方法，给代理对象赋值
	}

	@Override
	public Object invoke(Object proxy, Method method, Object[] args) 
			throws Throwable {
		// TODO Auto-generated method stub
		/*System.out.println("***proxy: "+proxy.getClass());
		System.out.println("*****method: "+method);
		System.out.println("****args: "+args);
		if(args!=null)
			for(Object arg:args)
				System.out.println("  arg: "+arg);*/
		//long start=System.nanoTime();
		//可以再invoke前后添加一些自己的操作
		Object ret= method.invoke(proxied, args);
		//当代理对象调用被代理对象的方法时，会自动东跳转到代理对象关联的handler对象的invoke方法调用
		//long end=System.nanoTime();
		//System.out.println("call-time: "+(end-start));
		return ret;
	}

}
```
测试类
```
	public class SimpleDynamicProxy {
	public static void consume(Interface it){
		it.doSomthing();
		it.sayhello("Meson");
	}
	public static void main(String[] args) {
		RealObject real=new RealObject();
		//consume(real);
		Interface pr=(Interface)Proxy.newProxyInstance(Interface.class.getClassLoader(),
				new Class[]{Interface.class}, new DynamicProxy(real));
		consume(pr);
	}
}
```
控制台打印出来
```
	***proxy: class $Proxy0
	*****method: public abstract void proxy.Interface.doSomthing()
	****args: null
	do something
	call-time: 449141
	***proxy: class $Proxy0
	*****method: public abstract void proxy.Interface.sayhello(java.lang.String)
	****args: [Ljava.lang.Object;@7d6b5518
	arg: Meson
	hello :Meson
	call-time: 39038
```
当然这里我加了一些其他的操作，把所有的信息都打印出来了，包括方法的调用次数。
这里有一个$Proxy0，是由System.out.println("***proxy: "+proxy.getClass())，Proxy.newProxyInstance返回的对象我们强制转化为Interface,因为newProxyInstance的第二个参数
提供一些代理对象要实现的的接口数组，而Interface为其中一个，所以可以转化为Interface型
通过 Proxy.newProxyInstance 创建的代理对象是在jvm运行时动态生成的一个对象，它并不是我们的InvocationHandler类型，也不是我们定义的那组接口的类型，而是在运行是动态生成的一个对象，并且命名方式都是这样的形式，以$开头，proxy为中，最后一个数字表示对象的标号
