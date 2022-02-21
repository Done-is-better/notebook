**单一职责原则**

> 对类来说，即一个类只负责一项职责。

**接口隔离原则**

> 客户端不应该依赖它不需要的接口，即一个类对另一个类的依赖应该建立在最小的接口上

**依赖倒置原则**

>高层模块不应该依赖低层模块，二者都应该依赖抽象
>
>抽象不应该依赖细节，细节应该依赖抽象

***中心思想是面向接口编程***

***依赖关系的三种传递方式：接口、构造器、setter***

**里式替换原则**

> 子类型必须能够替换掉他们的父类型

​		使用继承时，尽量不要重写父类中的方法

**开闭原则**

> 一个软件实体，如类、模块、函数应该对扩展开放，对修改关闭
>
> 用抽象构建框架，用实现扩展细节

**迪米特法则**

> 如果两个类不必彼此直接通信，那么这两个类就不应当发生直接的相互作用。如果其中的一个类需要调用另一个类的某一个方法，可以通过第三者转发这个调用。

**合成复用原则**

> 尽量使用合成、聚合的方式，而不是使用继承

*******

> 类之间的关系：依赖、泛化、实现、关联、聚合、组合

### 设计模式分类

> **创建型模式:**单例模式、抽象工厂模式、原型模式、创建者模式、工厂模式
>
> **结构型模式**：适配器模式、桥接模式、装饰模式、组合模式、外观模式、享元模式、代理模式
>
> **行为型模式**：模板方法模式、命令模式、访问者模式、迭代器模式、观察者模式、中介者模式、						备忘录模式、解释器模式、状态模式、策略模式、责任链模式

#### 单例模式

> 类的单例模式，就是采取一定的方法保证在整个软件系统中，对整个类只能存在一个对象实例，并且该类只提供一个取得其对象实例的方法。

单例模式的八种方式：饿汉式(静态常量)、饿汉式(静态代码块)、懒汉式(线程不安全)、懒汉式(线程安全，同步方法)、懒汉式(线程安全，同步代码块)、双重检查、静态内部类、枚举

```java
/*饿汉式--静态变量*/
class Singleton{
    //构造器私有话，外部不能new
    private Singleton(){}
    //类内部创建对象实例
    private final static Singleton instance = new Singleton();
    //提供一个公有静态方法，返回实例对象
    public static Singleton getInstance(){
        return instance;
    }
}
 //java.lang.Runtime采用饿汉式   
```

```java
/*另一种写法*/
class Singleton{
    private Singleton(){}
    private static Singleton instance;
    static{
        instance = new Singleton();
    }
    public static Singleton getInstance(){
        return instance;
    }
}
```



**优点：**在类装载的时候就完成了实例化，避免了线程同步问题

**缺点：**在类装载的时候就完成实例化，没有达到Lazy Loading的效果。如果从始至终从未使用这个实例，就会造成内存浪费。

*****

```java
/*懒汉式--线程不安全*/
class Singleton{
    private static Singleton instance;
    private Singleton(){}
    //当使用时才创建instance
    public static Singleton getInstance(){
        if(instance == null){
            instance = new Singleton();
        }
        return instance;
    }
}
```

**优点：**起到了Lazy Loading的效果，但是只能在单线程下使用（多线程下破坏单例模式）

```java
/*懒汉式--线程安全*/
class Singleton{
    private static Singleton singleton;
    private Singleton(){}
    public static synchronized Singleton getInstance(){
        if(singleton == null){
            singleton = new Singleton();
        }
        return singleton;
    }
}
```

**优缺点：**解决了线程不安全问题。但是效率太低了

```java
/*双重检查*/
class Singleton{
    private static volatile Singleton singleton;
    private Singleton(){}
    public static Singleton getInstance(){
        if(singleton == null){
            synchronized (Singleton.class){
                if(singleton == null){
                    singleton = new Singleton();
                }
            }
        }
        return singleton;
    }
}
```

**优点：** 线程安全、延迟加载、效率较高

```java
/*静态内部类*/
class Singleton{
    private Singleton(){}
    private static class SingletonInstance{
        private static final Singleton INSTANCE = new Singleton();
    }
    public static Singleton getInstance(){
        return SingletonInstance.INSTANCE;
    }
}
```

**优点：**既能达到Lazy Loading 效果，也能实现线程安全

​		外部类被装载的时候，内部类不会立即被装载。调用getInstance()的时候会导致内部类的装载，且只装在一次，线程是安全的（JVM装载类是线程安全的）。

```java
/*用枚举实现单例模式*/
enum Singleton{
    INSTANCE;
    public void entity(){
        ....
    }
}
```

**优点：**借助JDK1.5的枚举来实现单例模式，不仅能避免多线程同步问题，而且能防止反序列化重新创建对象

> 1、单例模式保证了系统内存中该类只存在一个对象，节省了系统资源，对于一些需要频繁创建销毁的对象，使用单例模式可以提高系统性能
>
> 使用场景：需要频繁创建和销毁的对象、创建对象时耗时过多或耗费资源过多但又经常用到的对象、工具类的对象、频繁访问数据库或文件的对象。

+++

#### 工厂模式

##### 简单工厂模式（静态工厂）

> 定义了一个创建对象的类，用这个类来封装实例化对象的行为

##### 工厂方法模式

##### 抽象工厂模式

> 定义了一个interface用于创建相关或有依赖关系的对象簇，无需指明具体的类
>
> 抽象工厂可以将简单工厂模式和工厂方法模式进行整合
>
> 将工厂抽象成两层，抽象工厂和具体实现的工厂子类

> **工厂模式的实质：**
>
> 将实例化对象的代码提取出来，放到一个类中统一管理和维护，达到和主项目的依赖关系的解耦。从而提高项目的扩展和维护性。

+++

#### 原型模式

> 用原型实例指定创建对象的种类，并且通过拷贝这些原型，创建新的对象
>
> **原理：**通过将一个原型对象传给那个要发动创建的对象，这个要发动创建的对象通过请求原型对象拷贝自己来创建，即 obj.clone()

```java

public class DeepProtoType implements Serializable,Cloneable{
	public String name;
    public DeepCloneableTarget deepCloneableTarget;
    public DeepProtoType(){
        super();
    }
    //深拷贝--克隆方法
    @Override
    protected Object clone() throws CloneNotSupportedException{
        Object deep = null;
        //完成对基本数据类型和String的克隆
        deep = super.clone();
        //对引用类型的属性单独处理
        DeepProtoType deepPtotoType = (DeepProtoType) deep;
        deepProtoType.deepCloneableTarget =(DeepCloneableTarget) deepCloneableTarget.clone();
        return deepProtoType;
    }
}
```



+++

#### 建造者模式

> 建造者模式又叫生成器模式，是一种对象构建模式。可以将复杂对象的构建过程抽象出来，使这个抽象过程的不同实现方法可以构造出不同表现的对象。

> **创建者模式的四个角色**
>
> **Product**:一个具体的产品对象
>
> **Builder**:创建一个Product对象的各个部件指定的接口/抽象类
>
> **ConcreteBuilder**：实现接口，构建和装配各个部件
>
> **Director**:构建一个使用Builder接口的对象。主要用于创建一个复杂的对象。它主要有两个作用，隔离用户与对象的生产过程，和负责控制产品对象的生产过程。

jdk 源码 java.lang.StringBuilder使用了建造者模式：

Appendable接口定义了append方法，为抽象建造者

AbstractStringBuilder实现了Appendable接口方法，为建造者，只是不能实例化

StringBuilder即充当指挥者角色，同时充当了具体的建造者，建造方法的实现是由AbstractStringBuilder完成，StringBuilder继承了AbstractStringBuilder;

> **细节**
>
> 使用程序不必知道产品内部的组成细节，*将产品本身与产品的创建过程解耦*，使相同的创建过程可以创建不同的产品对象
>
> 可以更加精细的控制产品的创建过程。将复杂产品的创建步骤分解在不同的方法中，使得创建过程更加清晰。
>
> 增加新的具体创建者无需修改原有类库的代码，指挥者类针对抽象编程，符合ocp.
>
> 建造者模式创建的产品一般具有较多共同点，如果产品之间差异较大，则不适合使用建造者模式

![image-20211011201635659](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211011201635659.png)

> **抽象工厂和建造者模式的比较**
>
> 抽象工厂模式实现对产品家族的创建，一个产品家族是这样的一系列产品：具有不同分类维度的产品组合，采用抽象工厂模式不需要关心构建过程，只关心什么产品由什么工厂生产。
>
> 创建者模式则要求按照指定的蓝图建造产品，它的主要目的是通过组装零配件而生产一个新产品。





***

#### ***结构型***

***

#### 适配器模式(Adapter Pattern)

> 适配器模式将某个类的接口转换成客户端期望的另一个接口表示，目的是兼容性，让原本接口不匹配，不能一起工作的两个类可以协同工作。

适配器模式主要分三类：类适配器模式、对象适配器模式、接口适配器模式

