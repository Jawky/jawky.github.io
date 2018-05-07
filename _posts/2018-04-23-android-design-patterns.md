---
title: Android设计模式
subtitle: "Android 开发中常用设计模式"
author: Jawky
layout: post
date: 2018/4/23 17:39:01  
tags:
    - 学习
---

# Android 开发中常见的设计模式


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

再说说单例模式中懒汉模式与饿汉模式的区别

饿汉模式实现代码：
```java
public class HungrySingle {

    private static final HungrySingle sInstance = new HungrySingle();

    private HungrySingle() {

    }

    public static HungrySingle getInstance() {
        return sInstance;
    }

}
```

懒汉模式实现代码：
```java
public class LazySingle {

    private static LazySingle sInstance = null;

    private LazySingle() {

    }

    public static LazySingle getInstance() {
        if (sInstance == null) {
            synchronized (LazySingle.class) {
                if (sInstance == null) {
                    sInstance = new LazySingle();
                }
            }
        }
        return sInstance;
    }

}
```

一般来说大多数情况不加锁并没有任何问题，但是在多线程并发执行的时候就很容易出现安全隐患，第一个线程在判断newInstance==null时，还没有new出实例时，第二个线程也进来，判断的newInstance也是null，然后也会new出实例，这就不是我们想要的单例模式了，所以就需要加锁，使用synchronized关键字，加锁更能解决安全问题，但加锁同时也会出现一个问题，那就是每次都需要判断锁，这样性能就会降低，所以为了提高性能，我们应该尽量减少锁判断的次数，加上双重判断，也就是上图代码，个人推荐选择饿汉模式，直白点就是简单，安全。

## Build模式

>  将一个复杂对象的构建与它的表示分离，使得同样的构建过程可以创建不同的表示

看完这个定义，但还是一脸懵逼，依然不知道什么叫Builder模式，下面我们通过一个例子来说明。学习设计模式，不要过度在意其定义，定义往往是比较抽象的，学习它最好的例子就是通过样例代码。

我们通过一个例子来引出Builder模式。假设有一个Person类，我们通过该Person类来构建一大批人，这个Person类里有很多属性，最常见的比如name，age，weight，height等等，并且我们允许这些值不被设置，也就是允许为null，该类的定义如下。

```java
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
```

然后我们为了方便可能回定义这样一个构造方法。

```java
public Person(String name, int age, double height, double weight) {
	this.name = name;
	this.age = age;
	this.height = height;
	this.weight = weight;
}
```

或许为了方便new对象，你还会定义一个空的构造方法

```java
public Person() {
}
```

甚至有时候你很懒，只想传部分参数，你还会定义如下类似的构造方法。

```java
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
```

于是你创建对象就变成这样了

```java
Person p1=new Person();
Person p2=new Person("张三");
Person p3=new Person("李四",18);
Person p4=new Person("王五",21,180);
Person p5=new Person("赵六",17,170,65.4);
```

可以想象一下这样创建的坏处，最直观的就是四个参数的构造函数的最后面的两个参数到底是什么意思，可读性不怎么好，如果不点击看源码，鬼知道哪个是weight哪个是height。还有一个问题就是当有很多参数时，编写这个构造函数就会显得异常麻烦，这时候如果换一个角度，试试Builder模式，你会发现代码的可读性一下子就上去了。

我们给Person增加一个静态内部类Builder类，并修改Person类的构造函数，代码如下。

```java
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
```

从上面的代码中我们可以看到，我们在Builder类里定义了一份与Person类一模一样的变量，通过一系列的成员函数进行设置属性值，但是返回值都是this，也就是都是Builder对象，最后提供了一个build函数用于创建Person对象，返回的是Person对象，对应的构造函数在Person类中进行定义，也就是构造函数的入参是Builder对象，然后依次对自己的成员变量进行赋值，对应的值都是Builder对象中的值。此外Builder类中的成员函数返回Builder对象自身的另一个作用就是让它支持链式调用，使代码可读性大大增强。

于是我们就可以这样创建Person类。

```java
Person.Builder builder=new Person.Builder();
Person person=builder
		.name("张三")
		.age(18)
		.height(178.5)
		.weight(67.4)
		.build();
```

有没有觉得创建过程一下子就变得那么清晰了。对应的值是什么属性一目了然，可读性大大增强。

其实在Android中， Builder模式也是被大量的运用。比如常见的对话框的创建

```java
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
```
最后对Builder模式做一下总结：

- 定义一个静态内部类Builder，内部的成员变量和外部类一样
- Builder类通过一系列的方法用于成员变量的赋值，并返回当前对象本身（this）
- Builder类提供一个build方法或者create方法用于创建对应的外部类，该方法内部调用了外部类的一个私有构造函数，该构     造函数的参数就是内部类Builder
- 外部类提供一个私有构造函数供内部类调用，在该构造函数中完成成员变量的赋值，取值为Builder对象中对应的值

## 观察者模式

>  定义对象间的一种一对多的依赖关系，当一个对象的状态发送改变时，所有依赖于它的对象都能得到通知并被自动更新

定义太抽象了，我们还是通过举例子来讲解。

- 情景1
> 有一种短信服务，比如天气预报服务，一旦你订阅该服务，你只需按月付费，付完费后，每天一旦有天气信息更新，它就会及时 向你发送最新的天气信息。

- 情景2
> 杂志的订阅，你只需向邮局订阅杂志，缴纳一定的费用，当有新的杂志时，邮局会自动将杂志送至你预留的地址。

观察上面两个情景，有一个共同点，就是我们无需每时每刻关注我们感兴趣的东西，我们只需做的就是订阅感兴趣的事物，比如天气预报服务，杂志等，一旦我们订阅的事物发生变化，比如有新的天气预报信息，新的杂志等，被订阅的事物就会即时通知到订阅者，即我们。而这些被订阅的事物可以拥有多个订阅者，也就是一对多的关系。当然，严格意义上讲，这个一对多可以包含一对一，因为一对一是一对多的特例，没有特殊说明，本文的一对多包含了一对一。

现在再来理解观察者模式的定义，是不是有种豁然开朗的感觉。

观察者模式的几个重要组成：

- 观察者，我们称它为Observer，有时候我们也称它为订阅者，即Subscriber
- 被观察者，我们称它为Observable，即可以被观察的东西，有时候还会称之为主题，即Subject

至于观察者模式的具体实现，这里带带大家实现一下场景一，其实java中提供了Observable类和Observer接口供我们快速的实现该模式，但是为了加深印象，我们不使用这两个类。

场景1中我们感兴趣的事情是天气预报，于是，我们应该定义一个Weather实体类。

```java
public class Weather {
    private String description;

    public Weather(String description) {
        this.description = description;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    @Override
    public String toString() {
        return "Weather{" +
                "description='" + description + '\'' +
                '}';
    }
}
```

然后定义我们的被观察者，我们想要这个被观察者能够通用，将其定义成泛型。内部应该暴露register和unregister方法供观察者订阅和取消订阅，至于观察者的保存，直接用ArrayList即可，此外，当有主题内容发送改变时，会即时通知观察者做出反应，因此应该暴露一个notifyObservers方法，以上方法的具体实现见如下代码。

```java
public class Observable<T> {
    List<Observer<T>> mObservers = new ArrayList<Observer<T>>();

    public void register(Observer<T> observer) {
        if (observer == null) {
            throw new NullPointerException("observer == null");
        }
        synchronized (this) {
            if (!mObservers.contains(observer))
                mObservers.add(observer);
        }
    }

    public synchronized void unregister(Observer<T> observer) {
        mObservers.remove(observer);
    }

    public void notifyObservers(T data) {
        for (Observer<T> observer : mObservers) {
            observer.onUpdate(this, data);
        }
    }

}
```
而我们的观察者，只需要实现一个观察者的接口Observer，该接口也是泛型的。其定义如下。

```java
public interface Observer<T> {
    void onUpdate(Observable<T> observable,T data);
}
```

一旦订阅的主题发送变换就会回调该接口。

我们来使用一下，我们定义了一个天气变换的主题，也就是被观察者，还有两个观察者观察天气变换，一旦变换了，就打印出天气信息，注意一定要调用被观察者的register进行注册，否则会收不到变换信息。而一旦不敢兴趣了，直接调用unregister方法进行取消注册即可。

```java
public class Main {
    public static void main(String [] args){
        Observable<Weather> observable=new Observable<Weather>();
        Observer<Weather> observer1=new Observer<Weather>() {
            @Override
            public void onUpdate(Observable<Weather> observable, Weather data) {
                System.out.println("观察者1："+data.toString());
            }
        };
        Observer<Weather> observer2=new Observer<Weather>() {
            @Override
            public void onUpdate(Observable<Weather> observable, Weather data) {
                System.out.println("观察者2："+data.toString());
            }
        };

        observable.register(observer1);
        observable.register(observer2);


        Weather weather=new Weather("晴转多云");
        observable.notifyObservers(weather);

        Weather weather1=new Weather("多云转阴");
        observable.notifyObservers(weather1);

        observable.unregister(observer1);

        Weather weather2=new Weather("台风");
        observable.notifyObservers(weather2);

    }
}
```

最后的输出结果也是没有什么问题的，如下

> 观察者1：Weather{description=’晴转多云’}

> 观察者2：Weather{description=’晴转多云’}

> 观察者1：Weather{description=’多云转阴’} 

> 观察者2：Weather{description=’多云转阴’} 

> 观察者2：Weather{description=’台风’} 

接下来我们看看观察者模式在android中的应用。我们从最简单的开始。还记得我们为一个Button设置点击事件的代码吗。

```java
Button btn=new Button(this);
btn.setOnClickListener(new View.OnClickListener() {
	@Override
	public void onClick(View v) {
		Log.e("TAG","click");
	}
});
```

其实严格意义上讲，这个最多算是回调，但是我们可以将其看成是一对一的观察者模式，即只有一个观察者。

其实只要是set系列的设置监听器的方法最多都只能算回调，但是有一些监听器式add进去的，这种就是观察者模式了，比如RecyclerView中的addOnScrollListener方法

```java
private List<OnScrollListener> mScrollListeners;
public void addOnScrollListener(OnScrollListener listener) {
	if (mScrollListeners == null) {
		mScrollListeners = new ArrayList<OnScrollListener>();
	}
	mScrollListeners.add(listener);
}
public void removeOnScrollListener(OnScrollListener listener) {
	if (mScrollListeners != null) {
		mScrollListeners.remove(listener);
	}
}
public void clearOnScrollListeners() {
	if (mScrollListeners != null) {
		mScrollListeners.clear();
	}
}
```

然后有滚动事件时便会触发观察者进行方法回调

```java
public abstract static class OnScrollListener {
	public void onScrollStateChanged(RecyclerView recyclerView, int newState){}
	public void onScrolled(RecyclerView recyclerView, int dx, int dy){}
}

void dispatchOnScrolled(int hresult, int vresult) {
	//...
	if (mScrollListeners != null) {
		for (int i = mScrollListeners.size() - 1; i >= 0; i--) {
			mScrollListeners.get(i).onScrolled(this, hresult, vresult);
		}
	}
}
void dispatchOnScrollStateChanged(int state) {
	//...
	if (mScrollListeners != null) {
		for (int i = mScrollListeners.size() - 1; i >= 0; i--) {
			mScrollListeners.get(i).onScrollStateChanged(this, state);
		}
	}
}
```
类似的方法很多很多，都是add监听器系列的方法，这里也不再举例。

还有一个地方就是Android的广播机制，其本质也是观察者模式，这里为了简单方便，直接拿本地广播的代码说明，即LocalBroadcastManager。

我们平时使用本地广播主要就是下面四个方法

```java
LocalBroadcastManager localBroadcastManager=LocalBroadcastManager.getInstance(this);
localBroadcastManager.registerReceiver(BroadcastReceiver receiver, IntentFilter filter);
localBroadcastManager.unregisterReceiver(BroadcastReceiver receiver);
localBroadcastManager.sendBroadcast(Intent intent)
```

调用registerReceiver方法注册广播，调用unregisterReceiver方法取消注册，之后直接使用sendBroadcast发送广播，发送广播之后，注册的广播会收到对应的广播信息，这就是典型的观察者模式。具体的源代码这里也不贴。

android系统中的观察者模式还有很多很多，有兴趣的自己去挖掘，接下来我们看一下一些开源框架中的观察者模式。一说到开源框架，你首先想到的应该是EventBus。没错，EventBus也是基于观察者模式的。

观察者模式的三个典型方法它都具有，即注册，取消注册，发送事件

```java
EventBus.getDefault().register(Object subscriber);
EventBus.getDefault().unregister(Object subscriber);

EventBus.getDefault().post(Object event)
```
内部源码也不展开了。接下来看一下重量级的库，它就是RxJava，由于学习曲线的陡峭，这个库让很多人望而止步。

创建一个被观察者

```java
Observable<String> myObservable = Observable.create(  
    new Observable.OnSubscribe<String>() {  
        @Override  
        public void call(Subscriber<? super String> sub) {  
            sub.onNext("Hello, world!");  
            sub.onCompleted();  
        }  
    }  
);
```
创建一个观察者，也就是订阅者

```java
Subscriber<String> mySubscriber = new Subscriber<String>() {  
    @Override  
    public void onNext(String s) { System.out.println(s); }  
  
    @Override  
    public void onCompleted() { }  
  
    @Override  
    public void onError(Throwable e) { }  
};
```

观察者进行事件的订阅

```java
myObservable.subscribe(mySubscriber);
```

具体源码也不展开，不过RxJava这个开源库的源码个人还是建议很值得去看一看的。推荐看一下[给 Android 开发者的 RxJava 详解](http://gank.io/post/560e15be2dca930e00da1083)

总之，在Android中观察者模式还是被用得很频繁的。





