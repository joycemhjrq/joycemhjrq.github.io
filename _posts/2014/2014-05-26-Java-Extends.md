---
layout: post
title: java Extends
categories: java
tags: extends
---

## Java Extends

>理解继承是理解面向对象程序设计的关键.在Java中,通过关键字extends继承一个已有的类,被继承的类称为父类(超类，基类),新的类称为子类(派生类).在Java中不允许多继承.code:

{% highlight java %}

class Animal
{
	int height,weight;
	void eat()
	{
	System.out.println("Animal eat!");
}
void sleep()
{
	System.out.println("Animal sleep!");
}
void breathe()
{
	System.out.println("Animal breathe!");
}
}
class Fish extends Animal
{
	
}
class DoMain
{
	public static void main(String[] args)
	{
		Animal an=new Animal();
		Fish fn=new Fish();
		
		an.breathe();
		fn.breathe();
		fn.height=30;
		fn.weight=20;
	}
}

{% endhighlight %}

Result:

	F:\Java Develop>javac Animal.java

	F:\Java Develop>java DoMain
	Animal breathe!
	Animal breathe!

(这说明派生类继承了父类的所有方法和成员变量.)

## 方法的覆盖(override)

>在子类中定义一个与父类同名,返回类型,参数类型均相同的一个方法,称为方法的覆盖,方法的覆盖发生在子类与父类之间.

code:

{% highlight java %}

class Animal
{
	int height,weight;
	void eat()
	{
	System.out.println("Animal eat!");
}
void sleep()
{
	System.out.println("Animal sleep!");
}
void breathe()
{
	System.out.println("Animal breathe!");
}
}
class Fish extends Animal
{
	int weight,height;   //隐藏了父类的weight,height;
	void breathe()  //override method breathe()
	{
		super.breathe();  //用super调用父类的构造方法
		System.out.println("Fish bubble");
	}
}
class DoMain
{
	public static void main(String[] args)
	{
		// Animal an=new Animal();
		Fish fn=new Fish();
		
		an.breathe();
		fn.breathe();
		fn.height=30;
		fn.weight=20;
	}
}

{% endhighlight %}

输出结果:

	F:\Java Develop>javac Animal.java

	F:\Java Develop>java DoMain
	Animal breathe!
	Fish bubble

## 特殊变量super

* 使用特殊变量super提供对父类的访问
* 可以使用super访问父类被子类隐藏的变量或覆盖的方法
* 每个子类构造方法的第一条语句都是隐含的调用super,如果父类没有这种形式的构造函数就会报错.

code:

{% highlight java %}

class Animal
{
	int height,weight;
	Animal()
	{
		System.out.println("Animal construct");
	}
	void eat()
	{
		System.out.println("Animal eat!");
	}
	void sleep()
	{
		System.out.println("Animal sleep!");
	}
	void breathe()
	{
		System.out.println("Animal breathe!");
	}
}

class Fish extends Animal
{
	Fish()
	{
		System.out.println("Fish construct");
	}
	void breathe()  //override method breathe()
	{
		System.out.println("Fish bubble");
	}
}

class DoMain
{
	public static void main(String[] args)
	{
		//Animal an=new Animal();
		Fish fn=new Fish();
		
		//an.breathe();
		//fn.breathe();
		//fn.height=30;
		//fn.weight=20;
	}
}

{% endhighlight %}

输出结果:

	F:\Java Develop>javac Animal.java

	F:\Java Develop>java DoMain
	Animal construct
	Fish construct

