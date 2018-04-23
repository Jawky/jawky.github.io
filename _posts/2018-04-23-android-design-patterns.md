---
title: Android设计模式
subtitle: "Android 开发中常用设计模式"
auth-r: Jawky
lay-ut: post
date: 2018/4/23 17:39:01  
tags:
    - 学习
---

#	Android 开发中常见的设计模式

对于开发人员来说，设计模式有时候就是一道坎，但是在开发中设计模式又非常有用，跨过了这道坎，可以让你的水平提升一个档次。而在android开发中，了解一些设计模式又是非常有必要的。对于想系统的学习设计模式的同学，推荐去看Head First系列的Head Hirst Design Pattern和大话设计模式，一开始可能比较难懂，多读几遍。

## 单例模式
> 确保某一个类只有一个实例，而且自行实例化并向整个系统提供这个实例。

有以下几个优点：

-	对于那些比较耗内存的类，只实例化一次可以大大提高性能，尤其是在移动开发中。
-	保持程序运行的时候该中始终只有一个实例存在内存中。

其实单例有很多种实现方式，但是个人比较倾向于其中1种。

代码如下
```java
public class Singleton {

    	private static volatile Singleton instance = null;

    	private Singleton(){
    	}
 
    	public static Singleton getInstance() {

        	if (instance == null) {

            	synchronized (Singleton.class) {

                	if (instance == null) {

                   instance = new Singleton();
                	}
            	}
        	}
        	return instance;
    	}
}
```

要保证单例，需要做一下几步

-	必须防止外部可以调用构造函数进行实例化，因此构造函数必须私有化。
-	必须定义一个静态函数获得该单例
-	单例使用volatile修饰
-	使用synchronized 进行同步处理，并且双重判断是否为null，我们看到synchronized (Singleton.class)里面又进行	了是否为null的判断，这是因为一个线程进入了该代码，如果另一个线程在等待，这时候前一个线程创建了一个实例出来完毕	后，另一个线程获得锁进入该同步代码，实例已经存在，没必要再次创建，因此这个判断是否是null还是必须的。

#	Build模式

>  将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示

看完这个定义，但还是一脸懵逼，依然不知道什么叫Builder模式，下面我们通过一个例子来说明。学习设计模式，不要过度在意其定义，定义往往是比较抽象的，学习它最好的例子就是通过样例代码。

我们通过一个例子来引出Builder模式。假设有一个Person类，我们通过该Person类来构建一大批人，这个Person类里有很多属性，最常见的比如name，age，weight，height等等，并且我们允许这些值不被设置，也就是允许为null，该类的定义如下。

<pre>
public class Person {
    private String name;
    private int age;
    private double height;
    private double weight;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public double getHeight() {
        return height;
    }

    public void setHeight(double height) {
        this.height = height;
    }

    public double getWeight() {
        return weight;
    }

    public void setWeight(double weight) {
        this.weight = weight;
    }
}
</pre>

然后我们为了方便可能回定义这样一个构造方法。

<pre>
public Person(String name, int age, double height, double weight) {
	this.name = name;
	this.age = age;
	this.height = height;
	this.weight = weight;
}
</pre>

或许为了方便new对象，你还会定义一个空的构造方法

<pre>
public Person() {
}
</pre>

甚至有时候你很懒，只想传部分参数，你还会定义如下类似的构造方法。

<pre>
public Person(String name) {
	this.name = name;
}

public Person(String name, int age) {
	this.name = name;
	this.age = age;
}

public Person(String name, int age, double height) {
	this.name = name;
	this.age = age;
	this.height = height;
}
</pre>

于是你创建对象就变成这样了

<pre>
Person p1=new Person();
Person p2=new Person("张三");
Person p3=new Person("李四",18);
Person p4=new Person("王五",21,180);
Person p5=new Person("赵六",17,170,65.4);
</pre>

可以想象一下这样创建的坏处，最直观的就是四个参数的构造函数的最后面的两个参数到底是什么意思，可读性不怎么好，如果不点击看源码，鬼知道哪个是weight哪个是height。还有一个问题就是当有很多参数时，编写这个构造函数就会显得异常麻烦，这时候如果换一个角度，试试Builder模式，你会发现代码的可读性一下子就上去了。

我们给Person增加一个静态内部类Builder类，并修改Person类的构造函数，代码如下。

<pre>
public class Person {
    private String name;
    private int age;
    private double height;
    private double weight;

    privatePerson(Builder builder) {
        this.name=builder.name;
        this.age=builder.age;
        this.height=builder.height;
        this.weight=builder.weight;
    }
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public double getHeight() {
        return height;
    }

    public void setHeight(double height) {
        this.height = height;
    }

    public double getWeight() {
        return weight;
    }

    public void setWeight(double weight) {
        this.weight = weight;
    }

    static class Builder{
        private String name;
        private int age;
        private double height;
        private double weight;
        public Builder name(String name){
            this.name=name;
            return this;
        }
        public Builder age(int age){
            this.age=age;
            return this;
        }
        public Builder height(double height){
            this.height=height;
            return this;
        }

        public Builder weight(double weight){
            this.weight=weight;
            return this;
        }

        public Person build(){
            return new Person(this);
        }
    }
}
</pre>

从上面的代码中我们可以看到，我们在Builder类里定义了一份与Person类一模一样的变量，通过一系列的成员函数进行设置属性值，但是返回值都是this，也就是都是Builder对象，最后提供了一个build函数用于创建Person对象，返回的是Person对象，对应的构造函数在Person类中进行定义，也就是构造函数的入参是Builder对象，然后依次对自己的成员变量进行赋值，对应的值都是Builder对象中的值。此外Builder类中的成员函数返回Builder对象自身的另一个作用就是让它支持链式调用，使代码可读性大大增强。

于是我们就可以这样创建Person类。

<pre>
Person.Builder builder=new Person.Builder();
Person person=builder
		.name("张三")
		.age(18)
		.height(178.5)
		.weight(67.4)
		.build();
</pre>

有没有觉得创建过程一下子就变得那么清晰了。对应的值是什么属性一目了然，可读性大大增强。

其实在Android中， Builder模式也是被大量的运用。比如常见的对话框的创建

<pre>
AlertDialog.Builder builder=new AlertDialog.Builder(this);
AlertDialog dialog=builder.setTitle("标题")
		.setIcon(android.R.drawable.ic_dialog_alert)
		.setView(R.layout.myview)
		.setPositiveButton(R.string.positive, new DialogInterface.OnClickListener() {
			@Override
			public void onClick(DialogInterface dialog, int which) {

			}
		})
		.setNegativeButton(R.string.negative, new DialogInterface.OnClickListener() {
			@Override
			public void onClick(DialogInterface dialog, int which) {

			}
		})
		.create();
dialog.show();
</pre>

#	观察者模式

>  定义对象间的一种一对多的依赖关系，当一个对象的状态发送改变时，所有依赖于它的对象都能得到通知并被自动更新