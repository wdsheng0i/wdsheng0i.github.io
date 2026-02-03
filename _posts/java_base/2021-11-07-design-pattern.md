---
layout: post
title: 设计模式介绍
category: design-patterns
tags: [ design-patterns ]
---

设计模式介绍

## 参考资料

- [Java开发中的23种设计模式详解(转)](http://www.cnblogs.com/maowang1991/archive/2013/04/15/3023236.html)
- https://www.cnblogs.com/MJyc/p/10410327.html
- ["乔"话设计模式——设计模式（前篇）-咚咚锵嘿哈](https://mp.weixin.qq.com/s/RmpASQlsOHcuGGD2MODjtg)

## 一、对象间关系

接触设计模式之前一定先清楚类之间的关系和设计，也就是我们常说的UML类图和类设计。    
想想自己在日常开发中有为了应对变化而对类做了什么处理吗？你的类是不是一个高内聚单一职责的呢？类之间的有没有强耦合关系？有没有面向接口，面向日志编程呢？

既然是面向对象，那么对象之间有什么关系呢？也就引申出我们的UML类图，为了更清晰的表达类与类之间的复杂的关系（能用图解决的事情尽量避免文字），说是复杂也是复杂在类之间的组合上，单纯的类之间的关系，无非就以下几种  
依赖, 关联, 聚合, 组合, 继承, 实现。其中关联关系由强到弱为：组合 > 聚合 > 关联 > 依赖。

再复杂的类图也无非这几种关系去表达，常见的类图结构中也就三种关系：继承，实现，关联（组合>聚合>关联>依赖）

### 1.1 UML图示例

分析下方这个图：  
![](https://wdsheng0i.github.io/assets/images/2021/design-pattern/pr.png)

- 大黑是一只【实现】了大雁接口的大雁，具有大雁的一切特征，
- 它呼吸的时候【依赖】于空气，
- 大黑由两个翅膀和其他的器官【组合】而成。
- 小黑是大黑的孩子，【继承】了大黑的全部特性。
- 冬天来了，包括大黑小黑在内的一群大雁【聚合】在一起向南飞去

对象间关系UML图示：

- 依赖(use a)【局部变量、方法的形参】  
  对象间最弱的一种关系，临时性的关联。代码中一般指由局部变量、函数参数、返回值建立的对于其他对象的调用关系  
  ![](https://wdsheng0i.github.io/assets/images/2021/java/d1.jpeg)

- 关联(1..1 1..N n ..1)(has a)  【成员变量】   
  对象间的一种引用关系，代码中指一个对象中长期引用另一个类的对象  
  关联关系中的两个类完全相互独立。可以是一对一、一对多、多对一。  
  比如：厨师和刀的关系，厨师切菜其实是刀切菜，厨师可以使用多把刀，每把刀也可以给多个厨师使用。  
  ![](https://wdsheng0i.github.io/assets/images/2021/java/d2.jpeg)

- 聚合（owns a） 【成员变量，不同生命周期】  
  聚合是关联关系的一种。聚合表述的是整体和个之间的关系。个体可以单独存在  
  比如：厨师组与厨师，厨师组由多个厨师组成，厨师组、每一个厨师也可以单独存在。  
  ![](https://wdsheng0i.github.io/assets/images/2021/java/d3.jpeg)

- 组合（is a part of） 【成员变量，同生命周期】  
  组合也是关联关系的一种。它是比聚合关系更强的关系，表述个体和局部之间的关系。局部不能单独存在；  
  组合关系代表一种“is-a”的关系。不仅强调整体与部分之间的关系，而且代表整体的对象也要对部分的生命周期负责，组合关系不能共享，如果代表整体的对象被销毁，那么部分也会被销毁。
  举例如：人体和器官  
  ![](https://wdsheng0i.github.io/assets/images/2021/java/d4.jpeg)

- 实现（implement）    
  ![](https://wdsheng0i.github.io/assets/images/2021/java/d5.jpeg)

- 继承(泛化)    
  ![](https://wdsheng0i.github.io/assets/images/2021/java/d6.jpeg)

### 1.2 代码示例

``` 
1.泛化关系：【继承extends】
1）.文字描述
　　一个类或者接口继承了另一个类或者接口，增加了在原有的方法和属性上增加了新的方法和属性。
2）.代码表示
　　public class A{}
　　public class B extends A{}//B继承了A中的所有的方法和属性，对于私有的方法和属性，子类只能是拥有，但无法直接使用。

2.实现关系 【实现implements】
1）.文字描述
　　一个class类实现了interface接口
2）.代码表示
　　public interface A{}
　　public class B implements A{}//类B实现了接口A，使用关键自implements，接口和接口的继承实用的是extends

3.依赖关系：【局部变量、方法的形参】
1）.文字描述
　　依赖关系表示一个类依赖于另一个类的定义，一般依赖关系在java中提现为局部变量、方法的形参，或者对静态方法的调用，这是一种弱关系。
2）.代码描述
　　public class A{}
　　public class B{
　　　　public void abc(A a){//在B类中的没有A的属性，而是以形参的方式引入
         A a1 = new A(); //在B类中的没有A的属性，而是以局部变量的方式引入
      }
　　}

4.关联关系：【成员变量】
1）.文字描述
　　一般以类的属性形式出现在关联类中，在java中关联关系一般使用成员变量来实现。又分为聚合关系和组合关系。
2）.代码描述
　　public class A{

    }

　　public class B{
　　　　A a;//成员变量引入了A类
　　}

5.聚合关系：【成员变量，不同生命周期】
1）.文字描述
　　聚合关系 has a,关联关系较弱,后期使用不会因为对象的删除而删除
2）.代码描述
　　public class A{
  
   }

　　public class B{
　　　　A a;//成员变量引入了A类
　　　　B(A a){
　　　　　　this.a = a;
　　　　}
　　}

6.组合关系：【成员变量，同生命周期】
1）文字描述
　　组合关系 contains a，关联关系较强，整体和部分是不可分割的，他们又共同的生命周期。
2）代码描述
　　public class A{
    
    }

　　public class B{
　　　　A a;//成员变量引入了A类
　　　　B(){
　　　　　　this.a = new A();
　　　　}
　　}
```

## 二、设计模式介绍

### 2.1 设计模式的分类 （总体来说设计模式分为三大类）

- 创建型模式：对象实例化的模式，创建型模式用于解耦对象的实例化过程。
    - 单例模式：某个类只能有一个实例，提供一个全局的访问点。
    - （简单工厂）：一个工厂类根据传入的参量决定创建出那一种产品类的实例。
    - 工厂方法：定义一个创建对象的接口，让子类决定实例化那个类。
    - 抽象工厂：创建相关或依赖对象的家族，而无需明确指定具体类。
    - 建造者模式：封装一个复杂对象的构建过程，并可以按步骤构造。
    - 原型模式：通过复制现有的实例来创建新的实例。
- 结构型模式：把类或对象结合在一起形成一个更大的结构。关注类和对象的组合。继承的概念被用来组合接口和定义组合对象获得新功能的方式
    - 适配器模式：将一个类的方法接口转换成客户希望的另外一个接口。
    - 装饰模式：动态的给对象添加新的功能。
    - 代理模式：为其他对象提供一个代理以便控制这个对象的访问。
    - 组合模式：将对象组合成树形结构以表示“”部分-整体“”的层次结构。
    - 亨元（蝇量）模式：通过共享技术来有效的支持大量细粒度的对象。
    - 外观模式：对外提供一个统一的方法，来访问子系统中的一群接口。
    - 桥接模式：将抽象部分和它的实现部分分离，使它们都可以独立的变化。
- 行为型模式：关注对象之间的通信，类和对象如何交互，及划分责任和算法。
    - 策略模式：定义一系列算法，把他们封装起来，并且使它们可以相互替换。
    - 模板模式：定义一个算法结构，而将一些步骤延迟到子类实现。
    - 观察者模式：对象间的一对多的依赖关系。
    - 迭代器模式：一种遍历访问聚合对象中各个元素的方法，不暴露该对象的内部结构
    - 责任链模式：将请求的发送者和接收者解耦，使的多个对象都有处理这个请求的机会。
    - 中介者模式：用一个中介对象来封装一系列的对象交互。
    - 解释器模式：给定一个语言，定义它的文法的一种表示，并定义一个解释器。
    - 状态模式：允许一个对象在其对象内部状态改变时改变它的行为。
    - 备忘录模式：在不破坏封装的前提下，保持对象的内部状态。
    - 命令模式：将命令请求封装为一个对象，使得可以用不同的请求来进行参数化。
    - 访问者模式：在不改变数据结构的前提下，增加作用于一组对象元素的新功能。

![](https://wdsheng0i.github.io/assets/images/2021/java/d-fl.png)

![](https://wdsheng0i.github.io/assets/images/2021/java/d-fl4.png)

### 2.2 设计模式之禅-总结

![](https://wdsheng0i.github.io/assets/images/2021/java/dp-c.png)

![](https://wdsheng0i.github.io/assets/images/2021/java/dp-s.png)

## 三、设计模式学习（spring经典9种设计模式）

### 3.1 简单工厂模式

要求：产品A/B继承相同的基类，可统一返回

实现方式1：统一的创建方法，根据传参进行创建不同的产品

实现方式2：工厂内不同产品对应不同创建方法

缺点：扩展产品C，需要修改工厂类

### 3.2 工厂方法(Factory Method)

实现方式：产品A、B继承同一基类，工厂A、B继承同一基类，分别负责AB的创建

优点：新增产品C，只需要对应创建c产品和c工厂，符合对扩展开放，对修改关闭 原则

### 3.3 单例模式(Singleton)

单例模式定义：

- 单例模式确保某个类只有一个实例，而且自行实例化并向整个系统提供这个实例， 单例模式就是为了避免不一致状态。

单例模式UML类图：  
![](https://wdsheng0i.github.io/assets/images/2021/design-pattern/singleton.png)

单例模式使用场景：

- 在计算机系统中，线程池、缓存、日志对象、对话框、打印机、显卡的驱动程序对象常被设计成单例.
- ES、redis、ftp连接 都可设计成单例模式
- springMVC的bean注入单例对象

单例模式有以下特点：

- 1、单例类只能有一个实例。
- 2、单例类必须自己创建自己的唯一实例。
- 3、单例类必须给所有其他对象提供这一实例：提供 static public 的 get实例方法

#### 3.3.1 懒汉式单例 （在第一次调用的时候实例化自己 ）

在同一个虚拟机范围内，Singleton的唯一实例只能通过getInstance()方法访问

懒汉式1 在getInstance方法上加同步    
在方法调用上加了同步，虽然线程安全了，但是每次都要同步，会影响性能，毕竟99%的情况下是不需要同步的

```
public class Singleton {
    private Singleton() {} //Singleton通过将构造方法限定为private避免了类在外部被实例化
    private static Singleton single=null;
    //静态工厂方法 
    public static synchronized  Singleton getInstance() {  //考虑线程安全,getInstance方法加同步
         if (single == null) {  
             single = new Singleton(); //懒汉式单例类.在第一次调用的时候实例化自己
         }  
        return single;
    }
}
```

懒汉式2 静态内部类    
利用了classloader的机制来保证初始化instance时只有一个线程，所以也是线程安全的，同时没有性能损耗， 所以一般我倾向于使用这一种。

```
public class Singleton {  
    private Singleton (){}  
    //静态内部类
    private static class LazyHolder { 
    //懒汉式单例类.在第一次调用的时候实例化自己 
       private static final Singleton INSTANCE = new Singleton();  
    }  
    public static final Singleton getInstance() {  
       return LazyHolder.INSTANCE;  
    }  
}
```

懒汉式3 双重检查锁定

```
public class Singleton {  
    private static volatile Singleton singleton = null;

    /**
     * 构造函数修饰为private防止在其他地方创建该实例
     */
    private Singleton() {
    }

    public static Singleton getInstance() {
        if (singleton == null) {  
            synchronized (Singleton.class) {  
               if (singleton == null) {  
                  singleton = new Singleton(); 
               }  
            }  
        }  
        return singleton; 
    }
}
```

#### 3.3.2 饿汉式单例

饿汉式单例类.在类初始化时，已经自行实例化     
饿汉式在类创建的同时就已经创建好一个静态的对象供系统使用，以后不再改变，所以天生是线程安全的。

```
public class Singleton1 {
    private Singleton1() {}
    private static final Singleton1 single = new Singleton1(); //在类初始化时，已经自行实例化 
    //静态工厂方法 
    public static Singleton1 getInstance() {
        return single;
    }
}
```

#### 3.3.3 单例模式总结

1、线程安全：

- 懒汉式本身是非线程安全的，为了实现线程安全有几种写法，分别是上面的1、2、3，这三种实现在资源加载和性能方面有些区别。
- 饿汉式天生就是线程安全的，可以直接用于多线程而不会出现问题

2、资源加载和性能：

- 懒汉式顾名思义，会延迟加载，在第一次使用该单例的时候才会实例化对象出来，第一次调用时要做初始化，如果要做的工作比较多，性能上会有些延迟，之后就和饿汉式一样了。
- 饿汉式在类创建的同时就实例化一个静态对象出来，不管之后会不会使用这个单例，都会占据一定的内存，但是相应的，在第一次调用时速度也会更快，因为其资源已经初始化完成，

### 3.4 代理模式(Proxy)

代理模式的定义：

- 代理模式提供了对目标对象另外的一种访问方式， 即通过【代理对象】访问【目标对象】；并由代理对象控制对原对象的引用。通俗的来讲代理模式就是我们生活中常见的中介。

代理模式UML类图：  
![](https://wdsheng0i.github.io/assets/images/2021/design-pattern/proxy.png)

代理模式优点：

- 中介隔离作用：一个客户对象不想或者不能直接引用一个委托对象，而代理类对象可以在客户对象和委托对象之间起到中介的作用，其特征是代理类和委托类实现相同的接口。
-
开闭原则，扩展功能：代理类除了是客户类和委托类的中介之外，还可以通过给代理类增加额外的功能来扩展委托类的功能，只需修改代理类而不需再修改委托类，符合代码设计的开闭原则。  
代理类主要负责为委托类**预处理消息、过滤消息、把消息转发给委托类，以及事后对返回结果的处理**
等。代理类本身并不真正实现服务，而是同过调用委托类的相关方法，来提供特定的服务。真正的业务功能还是由委托类来实现，但可以在业务功能执行的前后加入一些公共的服务。例如我们想给项目加入缓存、日志这些功能，我们就可以使用代理类来完成，而不需修改已经封装好的委托类。

代理模式分类：  
按照代理创建的时期来进行分类的话， 可以分为两种：静态代理、动态代理。

- 静态代理是由程序员创建或特定工具自动生成源代码，再对其编译。在程序运行之前，代理类.class文件就已经被创建了。
- 动态代理是在程序运行时通过反射机制动态创建的。

#### 3.4.4 静态代理

静态代理在使用时, 需要定义接口或者父类, 被代理对象与代理对象一起实现相同的接口或者是继承相同父类.

``` 
/**
 * 目标对象和代理对象的共同：接口类
 */
public interface IBuyHouse {
    void buyHosue();
}

/**
 * 目标对象实现类
 */
public class BuyHouseImpl implements IBuyHouse {
    @Override
    public void buyHosue() {
        System.out.println("我要买房");
    }
}

/**
 * 代理类
 */
public class BuyHouseProxy implements IBuyHouse {
    private IBuyHouse buyHouse;

    //构造方法：传入目标对象
    public BuyHouseProxy(final IBuyHouse buyHouse) {
        this.buyHouse = buyHouse;
    }

    @Override
    public void buyHosue() {
        System.out.println("买房前准备"); // 前置扩展
        buyHouse.buyHosue();  // 执行目标对象的真正方法
        System.out.println("买房后装修");  // 后置结果扩展
    }
}

/**
 * 测试
 */
public class ProxyTest {
    public static void main(String[] args) {
        IBuyHouse buyHouse = new BuyHouseImpl(); // 目标对象
        // buyHouse.buyHosue(); // 创建目标对象直接调用目标方法
        BuyHouseProxy buyHouseProxy = new BuyHouseProxy(buyHouse); // 创建代理对象,把目标对象传给代理对象,建立代理关系
        buyHouseProxy.buyHosue(); // 执行的是代理的方法，代理对象内部调用目标对象方法，并执行代理类内添加的扩展功能
    }
}
```

静态代理总结：

- 优点：可以做到在符合开闭原则的情况下对目标对象进行功能扩展。
- 缺点：我们得为每一个服务都得创建代理类，工作量太大，不易管理。同时接口一旦发生改变，接口增加方法,
  目标类、代理类也得相应修改。(jdk1.8支持接口内提供方法默认实现)

#### 3.4.5 动态代理

如何解决静态代理中的缺点呢? 答案是可以使用动态代理方式    
在动态代理中不再需要手动的创建代理类，只需要编写一个动态处理器就可以了。真正的代理对象由JDK再运行时动态的来创建。

动态代理有以下特点:

- 1.代理对象,不需要实现接口
- 2.代理对象的生成, 是利用JDK的API, 动态的在内存中构建代理对象(需要我们指定创建代理对象/目标对象实现的接口的类型)
- 3.动态代理也叫做:JDK代理,接口代理

JDK中生成代理对象的API:   
代理类所在包:java.lang.reflect.Proxy  
JDK实现代理只需要使用newProxyInstance方法,但是该方法需要接收三个参数,完整的写法是:  
``` static Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces,InvocationHandler h ) ```   
注意该方法是在Proxy类中是静态方法,且接收的三个参数依次为:

- ClassLoader loader,:指定当前目标对象使用类加载器,获取加载器的方法是固定的
- Class<?>[] interfaces,:目标对象实现的接口的类型,使用泛型方式确认类型
- InvocationHandler h:事件处理,执行目标对象的方法时,会触发事件处理器的方法,把当前执行目标对象的方法作为参数传入

``` 
/**
 * 实现方式1：代理工厂类
 * 创建动态代理对象
 * 动态代理不需要实现接口,但是需要指定接口类型
 */
public class ProxyFactory{

    //维护一个目标对象
    private Object target;

    public ProxyFactory(Object target){
        this.target=target;
    }

    //给目标对象生成代理对象
    public Object getProxyInstance(){
        return Proxy.newProxyInstance(
                target.getClass().getClassLoader(),
                target.getClass().getInterfaces(),
                new InvocationHandler() {
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        System.out.println("买房前准备");
                        //执行目标对象方法
                        Object returnValue = method.invoke(target, args);
                        System.out.println("买房后装修");
                        return returnValue;
                    }
                }
        );
    }
}

/**
 * 测试类
 */
public class App {
    public static void main(String[] args) {
        // 目标对象
        IBuyHouse buyHouse = new BuyHouseImpl();  
        // 创建代理对象, 传入目标对象，内存中动态生成的代理对象 
        IUserDao proxy = (IUserDao) new ProxyFactory(buyHouse).getProxyInstance();
        // 执行代理对象方法时, 会触发事件处理器的方法,会把当前执行目标对象的方法作为参数传入
        proxy.buyHosue();
    }
}


/**
 * 实现方式2：
 */
public class DynamicProxyHandler implements InvocationHandler {
    //维护一个目标对象
    private Object object;

    public DynamicProxyHandler(final Object object) {
        this.object = object;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("买房前准备");
        Object result = method.invoke(object, args);
        System.out.println("买房后装修");
        return result;
    }
}

/**
 * 测试类2
 */
public class DynamicProxyTest {
    public static void main(String[] args) {
        IBuyHouse buyHouse = new BuyHouseImpl();
        BuyHouse proxyBuyHouse = (BuyHouse) Proxy.newProxyInstance(IBuyHouse.class.getClassLoader(),
            new Class[]{IBuyHouse.class},
            new DynamicProxyHandler(buyHouse));
        proxyBuyHouse.buyHosue();  // 执行代理对象方法时, 会触发事件处理器的方法,会把当前执行目标对象的方法作为参数传入
    }
}

```

静态代理总结：     
动态代理大大减少了开发任务，同时减少了对业务接口的依赖，降低了耦合度。但还是有一点遗憾之处，就是它始终无法摆脱仅支持interface代理的桎梏    
目标对象一定要实现接口（Proxy.newProxyInstance三个参数之一即要求传入目标对象接口）,否则不能用动态代理

#### 3.4.6 CGlib代理（子类代理）

上面的静态代理和动态代理模式都是要求目标对象是实现一个接口的目标对象,  
但是有时候目标对象只是一个【普通的类对象】,并没有实现任何的接口,这个时候就可以使用以【目标对象子类】的方式类实现代理,从而实现对目标对象功能的扩展，这种方法就叫做:
Cglib代理

- JDK的动态代理有一个限制,就是目标对象必须实现一个或多个接口,如果想代理没有实现接口的类,就可以使用Cglib实现.
- Cglib是一个强大的高性能的代码生成包,可以在运行期扩展java类与实现java接口.被许多AOP的框架广泛使用,例如Spring
  AOP和synaop,为他们提供方法的interception(拦截)
- Cglib包的底层是通过使用一个小而块的字节码处理框架ASM来转换字节码并生成新的类.不鼓励直接使用ASM,因为它要求你必须对JVM内部结构包括class文件的格式和指令集都很熟悉.
- CGLib通过字节码技术为一个类创建子类，并在子类中采用方法拦截的技术拦截所有父类方法的调用，顺势织入横切逻辑。但因为采用的是继承，所以不能对final修饰的类进行代理

``` 
Cglib子类代理实现方法:
1.需要引入cglib的jar文件,但是Spring的核心包中已经包括了Cglib功能,所以直接引入pring-core-3.2.5.jar即可.
2.引入功能包后,就可以在内存中动态构建子类
3.代理的类不能为final,无法被继承
4.目标对象的方法如果为final/static,那么就不会被拦截,即不会执行目标对象额外的业务方法.

/**
 * Cglib动态代理类
 */
public class CglibProxy implements MethodInterceptor {
    //维护目标对象
    private Object target;

    public CglibProxy(Object target) {
        this.target = target;
    }

    public Object getInstance() {
        //1.工具类 
        Enhancer enhancer = new Enhancer();
        //2.设置父类
        enhancer.setSuperclass(this.target.getClass());
        //3.设置回调函数
        enhancer.setCallback(this);
        //4.创建子类(代理对象)
        return enhancer.create();
    }

    @Override
    public Object intercept(Object object, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
        System.out.println("买房前准备");
        //执行目标对象方法
        Object result = methodProxy.invoke(object, args);
        System.out.println("买房后装修");
        return result;
    }
}

/**
 * 测试类
 */
public class CglibProxyTest {
    public static void main(String[] args){
        IBuyHouse buyHouse = new BuyHouseImpl();
        //传入目标对象，创建代理对象
        BuyHouseImpl buyHouseCglibProxy = (BuyHouseImpl) new CglibProxy(buyHouse).getInstance();
        buyHouseCglibProxy.buyHosue(); //执行代理对象的方法
    }
}
```

CGLIB代理总结：  
CGLIB创建的动态代理对象比JDK创建的动态代理对象的性能更高，但是CGLIB创建代理对象时所花费的时间却比JDK多得多。所以对于单例的对象，因为无需频繁创建对象，用CGLIB合适，反之使用JDK方式要更为合适一些

### 3.5 模板方法(Template Method)

### 3.6 观察者模式(Observer)

### 3.7 适配器模式(Adapter)

### 3.8 装饰者模式(Decorator)

### 3.9 策略模式(Strategy)

策略模式定义：

- 策略模式定义了一系列的算法（一般会用一个抽象的策略类来定义），并将每一个算法实现封装起来（每种算法实现则对应于一个具体策略类），使每个算法可以相互替代，使算法本身和使用算法的客户端分割开来，相互独立。

#### 3.9.1 策略模式结构

- 策略接口角色 IStrategy: 用来约束一系列具体的策略算法，策略上下文角色StrategyContext使用此策略接口来调用具体的策略所实现的算法。
- 策略实现角色 ConcreteStrategy: 具体的策略实现，即具体的算法实现。
- 策略上下文角色 StrategyContext: 策略上下文，负责和具体的策略实现交互，通常策略上下文对象会持有一个真正的策略实现对象，策略上下文还可以让具体的策略实现从其中获取相关数据，回调策略上下文对象的方法。

策略模式UML类图：
![](https://wdsheng0i.github.io/assets/images/2021/design-pattern/strategy.png)

#### 3.9.2 策略模式的优、缺点

策略模式的优点：

- 1.策略模式的功能就是通过抽象、封装来定义一系列的算法，使得这些算法可以相互替换，所以为这些算法定义一个公共的接口，以约束这些算法的功能实现。如果这些算法具有公共的功能，可以将接口变为抽象类，将公共功能放到抽象父类里面。
- 2.策略模式的一系列算法是可以相互替换的、是平等的，写在一起就是if-else组织结构，如果算法实现里又有条件语句，就构成了多重条件语句，可以用策略模式，避免这样的多重条件语句。
- 3.扩展性更好：对“开闭原则”的完美支持，在策略模式中扩展策略实现非常的容易，只要新增一个策略实现类，然后在使用策略实现的地方，使用这个新的策略实现就好了。
- 4.策略模式的关注点不是如何实现算法，而是如何组织、调用这些算法，从而让程序结构更灵活，具有更好的维护性和扩展性

策略模式的缺点：

- 1.客户端必须了解所有的策略，清楚它们的不同：
    - 如果由客户端来决定使用何种算法，那客户端必须知道所有的策略，清楚各个策略的功能和不同，这样才能做出正确的选择，但是这暴露了策略的具体实现。
- 2.增加了对象的数量：
    - 由于策略模式将每个具体的算法都单独封装为一个策略类，如果可选的策略有很多的话，那对象的数量也会很多。
- 3.只适合偏平的算法结构：
    - 由于策略模式的各个策略实现是平等的关系（可相互替换），实际上就构成了一个扁平的算法结构。即一个策略接口下面有多个平等的策略实现（多个策略实现是兄弟关系）
    - 运行时可以动态地在不同的策略实现中切换，但是同时只能使用一个，这就限制了算法的使用层级，且【不能被嵌套】。

#### 3.9.3 策略模式示例

``` 
/**
 * 策略角色(抽象/接口)类： Strategy
 * 用来约束一系列具体的策略算法
 * 也可以是abstract抽象父类，策略实现子类对其继承
 */
public interface IStrategy {
    /**
     * 必要方法：抽象算法接口，策略实现类中进行多态实现
     */
    void algorithm();
}

/**
 * @Description 具体策略实现类A： ConcreteStrategy
 **/
public class ConcreteStrategyA implements IStrategy {
    @Override
    public void algorithm() {
        System.out.println("Strategy A");
    }
}

/**
 * @Description 具体策略实现类B： ConcreteStrategy
 **/
public class ConcreteStrategyB implements IStrategy {
    @Override
    public void algorithm() {
        System.out.println("Strategy B");
    }
}

/**
 * @Description 策略上下文
 * 负责和具体的策略实现交互，通常策略上下文对象会持有一个真正的策略实现对象，策略上下文还可以让具体的策略实现从其中获取相关数据，回调策略上下文对象的方法
 **/
public class StrategyContext {
    private IStrategy mStrategy;

    public StrategyContext(IStrategy strategy) {
        this.mStrategy = strategy;
    }

    public void algorithm() {
        this.mStrategy.algorithm();
    }
}

/**
 * 测试StrategyContext 
 **/
public class StrategyTest {
    public static void main2(String[] args) {
        //选择一个具体策略
        IStrategy strategyA = new ConcreteStrategyA();
        //来一个上下文环境
        StrategyContext context = new StrategyContext(strategyA);
        //客户端直接让上下文环境执行算法
        context.algorithm();

        IStrategy strategyB = new ConcreteStrategyB();
        context = new StrategyContext(strategyB);
        context.algorithm();
    }
}
```

### 原型模式（Prototype）

是用于创建重复的对象，同时又能保证性能。这种类型的设计模式属于创建型模式，它提供了一种**创建对象的最佳方式**。

**原型模式的本质就是clone**，可以解决构建复杂对象的资源消耗问题，能再某些场景中提升构建对象的效率；还有一个重要的用途就是保护性拷贝，可以通过返回一个拷贝对象的形式，实现只读限制
这种模式是实现了一个原型接口Cloneable，该接口用于创建当前对象的克隆。

1）、意图：  
用原型实例指定创建对象的种类，并且通过拷贝这些原型创建新的对象。

2）、主要解决：  
在运行期建立和删除原型。

3）、何时使用：

- 1、当一个系统应该独立于它的产品创建，构成和表示时。
- 2、当要实例化的类是在运行时刻指定时，例如，通过动态装载。
- 3、为了避免创建一个与产品类层次平行的工厂类层次时。
- 4、当一个类的实例只能有几个不同状态组合中的一种时。建立相应数目的原型并克隆它们可能比每次用合适的状态手工实例化该类更方便一些。

4）、如何解决：  
利用已有的一个原型对象，快速地生成和原型对象一样的实例。

5）、关键代码：

- 1、实现克隆操作，在 JAVA 继承 Cloneable，重写 clone()。
- 2、原型模式同样用于隔离类对象的使用者和具体类型（易变类）之间的耦合关系，它同样要求这些"易变类"拥有稳定的接口

6）、优点：

- 1、性能提高。
- 2、逃避构造函数的约束。

7）、缺点：

- 1、配备克隆方法需要对类的功能进行通盘考虑，这对于全新的类不是很难，但对于已有的类不一定很容易，特别当一个类引用不支持串行化的间接对象，或者引用含有循环结构的时候。
- 2、必须实现 Cloneable 接口。

8）、使用场景：

- 1、资源优化场景。
- 2、类初始化需要消化非常多的资源，这个资源包括数据、硬件资源等。
- 3、性能和安全要求的场景。
- 4、通过 new 产生一个对象需要非常繁琐的数据准备或访问权限，则可以使用原型模式。
- 5、一个对象多个修改者的场景。
- 6、一个对象需要提供给其他对象访问，而且各个调用者可能都需要修改其值时，可以考虑使用原型模式拷贝多个对象供调用者使用。
- 7、在实际项目中，原型模式很少单独出现，一般是和工厂方法模式一起出现，通过 clone 的方法创建一个对象，然后由工厂方法提供给调用者。原型模式已经与
  Java 融为浑然一体，大家可以随手拿来使用

## 四、设计模式学习心得

- 1、刚接触设计模式学习的话一阶段一定先理解设计模式的概念以及应用场景、优缺点、和UML、实现简单Demo入手。
- 2、学习过程中可以结合着spring和java的设计理念一起学习，既掌握了一些框架中的典型应用场景又加深了设计模式的理解，一举两得
- 3、二阶段的话在日常开发过程中和实际业务结合尝试加入你的设计，哪怕只是开发一个很小的模块也可以有你设计的思想，只有真正和实际项目结合才能让你对设计的理解更加深入
- 4、设计是一个迭代的过程，版本迭代也是考验设计良好性的过程。有的设计模式在项目启动的时候就要考虑，有的是在系统升级的时候考虑，勿要在系统启动之初过度设计
- 5、设计模式在一定程度上是增加了系统的复杂度，考虑到系统的扩展性，必然也有所牺牲，在真正使用的时候一定衡量你能给项目带来的是否利大于弊

