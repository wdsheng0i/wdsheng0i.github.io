---
layout: post
title: 集合 泛型 反射 网络编程
category: java
tags: [java]
---

集合 泛型 反射 网络编程

## 参考资料

## 一、泛型
 
### 1.1 泛型概念
即“参数化类型”。那么参数化类型怎么理解呢？顾名思义，就是将类型由原来的具体的类型参数化，类似于方法中的变量参数，此时类型也定义成参数形式（可以称之为类型形参），然后在使用/调用时传入具体的类型（类型实参）。  
Java语言引入泛型的好处是安全简单  https://www.cnblogs.com/lwbqqyumidi/p/3837629.html

### 1.2 泛型特性
1.常见的如T、E、K、V等形式的参数常用于表示泛型形参 
  
2.泛型只在编译阶段有效，泛型类型在逻辑上可以看成是多个不同的类型，实际上都是相同的基本类型
```
List<String> stringArrayList = new ArrayList<String>();
List<Integer> integerArrayList = new ArrayList<Integer>();
Class classStringArrayList = stringArrayList.getClass();
Class classIntegerArrayList = integerArrayList.getClass();
classStringArrayList.equals(classIntegerArrayList)
```

3.类型通配符，类型通配符一般是使用 ? 代替具体的类型实参  

4.泛型边界：  
```
<? extends T>   //是指 “上界通配符：元素需要是 T 的子类
<? super T>     //是指 “下界通配符：元素需要是 T 的父类
```

### 1.3 泛型形式
#### 1.3.1.泛型类：  
泛型类型用于类的定义中，被称为泛型类；如各种容器类：List、Set、Map。
```
public interface Iterable<T> {   Iterator<T> iterator(); }
public interface Collection<E> extends Iterable<E> {...}
public interface List<E> extends Collection<E> {...}
public abstract class AbstractCollection<E> implements Collection<E> {...}
public abstract class AbstractList<E> extends AbstractCollection<E> implements List<E> {
public class ArrayList<E> extends AbstractList<E> 
    implements List<E>, RandomAccess, Cloneable, java.io.Serializable {...}
List<String> list = new ArrayList<String>();
public interface Map<K,V> {...}
//  此处T可以随便写为任意标识，常见的如T、E、K、V等形式的参数常用于表示泛型
//  在实例化泛型类时，必须指定T的具体类型
public class Generic<T>{ 
    //key这个成员变量的类型为T,T的类型由外部指定  
    private T key; 
    public Generic(T key) {   //泛型构造方法  形参key的类型也为T，T的类型由外部指定
        this.key = key;
    } 
    public T getKey(){   //泛型方法 getKey的返回值类型为T，T的类型由外部指定
        return key;
    }
}
```

#### 1.3.2.泛型方法:
```
public class ArrayList<E> extends AbstractList<E> implements List<E>, RandomAccess, Cloneable, java.io.Serializable
{ 
    .......

    public <T> T[] toArray(T[] a) {
        if (a.length < size) 
            return (T[]) Arrays.copyOf(elementData, size, a.getClass());
        System.arraycopy(elementData, 0, a, 0, size);
        if (a.length > size)
            a[size] = null;
        return a;
    }
}
 
```

#### 1.3.3.泛型接口:
```
public interface Collection<E> extends Iterable<E> {...}
public interface List<E> extends Collection<E> {...}
```

## 二、反射

### 2.1.基础知识
- 编译时   
编译时顾名思义就是正在编译的时候。就是编译器帮你把源代码翻译成机器能识别的代码.  
(当然只是一般意义上这么说,实际上可能只是翻译成某个中间状态的语言.比如Java只有JVM识别的字节码,.另外还有啥链接器.汇编器.为了了便于理解我们可以统称为编译器)   
那编译时就是简单的作一些翻译工作,比如检查有没有写错啥关键字.有啥词法分析,语法分析之类的过程. 如果发现啥错误编译器就告诉你. 

- 运行时   
所谓运行时就是代码跑起来了.被装载到内存中去了。  
而运行时类型检查就与前面讲的编译时类型检查(或者静态类型检查)不一样.不是简单的扫描代码.而是在内存中做些操作,做些判断.(这样很多编译时无法发现的错误,在运行就可以发现报错了) 

- 反射机制  
是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取的信息以及动态调用对象的方法的功能称为java语言的反射机制  
反射提供了一种动态的功能，非常强大，体现在通过反射相关的api就可以知道一个陌生java类的所有信息，包括属性方法和构造器，而且元素可以在运行时态动态的进行创建和调用，不必在jvm运行时进行确定

### 2.2.反射的原理：
反射是为了能够动态加载一个类，动态的调用方法和访问属性，出发点在于JVM会为每个类创建一个java.lang.Class类的实例，通过该对象可以获取这个类的信息，然后通过使用java.lang.reflect包下的的api达到各种动态需求

### 2.3.Class类的含义和作用
在new()对象，使用类的静态方法或者成员时，类就会被加载至JVM中，然后jvm就会为它创建一个class类的实例对象，获取Class对象的方法：
```
1）.  Class<?> class1 = Class.forName("com.wds.test.Dog"); //通过包路径获取类信息
2）.  Class<?> class2 = jinmaoDog.getClass(); //通过实例对象获取类信息
3）.  Class<?> class3 = Dog.class;  或者 Class <Dog> cls = Dog.class;  //通过类名获取类信息
```

### 2.4.操作类的属性和方法
```
//1.取类信息
Class<Student> clazz = Student.class  //取Class类信息 
Class<?> clazz = Class.forName("com.test.Student"); //获取Class信息
Class<?> cls = student1.getClass(); // 通过对象实例取得类信息

Class<?> parentClass = clazz.getSuperclass(); // 取得父类
Class<?> intes[] = clazz.getInterfaces(); // 获取所有的接口
String className = cls.getName()
Object obj = clazz.newInstance();  //实例化对象

//2.通过Field对象，可以改变对应属性的可见性，也可以通过set()方法进行属性的赋值：
Field[] filed1 = clazz.getFields();// 获取类的public属性
Field[] fields = clazz.getDeclaredFields();// 取得本类的全部属性
int mo = field[i].getModifiers();// 获取权限修饰符
Class<?> type = field[i].getType();// 获取属性类型
fileds[i].getName() //获取属性名

Field field = clazz.getDeclaredField("name"); //获取name属性
field.setAccessible(true); //可以改变对应属性的可见性
field.set(obj, "张三"); //obj对象 的 name 的属性赋值
field.get(obj);  //获取obj对象的当前属性值

//3.通过反射，获取方法签名信息，存储在Method对象中：通过Method对象的invoke()方法进行方法调用：
public class ReflectionTest {
    public void reflect() {
        System.out.println("Java 反射机制 - 调用无参方法");
    }
    public void reflect(int age, String name) {
        System.out.println("Java 反射机制 - 调用有参数方法");
    }

//4.使用示例
public static void main(String[] args) throws Exception {
    Class<?> clazz = Class.forName("com.tianmaying.ReflectionTest");    // 获取类信息
    Method method[] = clazz.getMethods();   // 获取类的所有方法
    for (int i = 0; i < method.length; ++i) {
    method[i].getName()；  // 获取方法的签名
    Class<?> returnType = method[i].getReturnType();      // 获取方法返回类型
    Class<?> para[] = method[i].getParameterTypes();       // 获取方法参数列表
    int temp = method[i].getModifiers();    // 获取方法修饰符
    Class<?> exce[] = method[i].getExceptionTypes();    // 获取方法抛出的异常列表
    Method method = clazz.getMethod("reflect");   // 通过反射获取方法并调用方法
    method.invoke(clazz.newInstance());   //调用方法
    method = clazz.getMethod("reflect", int.class, String.class);   //通过反射获取方法，并进行传参调用方法
    method.invoke(clazz.newInstance(), 20, "张三");  }   //调用方法
}
```

### 2.5.反射应用
具体来说，反射机制主要提供了以下功能：
- 在运行时判断任意一个对象所属的类；  
- 在运行时构造任意一个类的对象；  
- 在运行时判断任意一个类所具有的成员变量和方法；  
- 在运行时调用任意一个对象的方法；  
- 生成动态代理；  
 

## 三、集合
### 3.1 容器类介绍
- java.util.Collection   集合
    - java.util.List     //有序、可重复
        - java.util.ArrayList    //基于数组，内存连续，查询块，增删慢
        - java.util.LinkedList     //基于链表，内存可不连续，查询慢，增删块
        - java.util.Vector    //线程安全（方法有synchronized修饰）
    - java.util.Set      //无序、不可重复
        - java.util.SortedSet     //有序
              - java.util.TreeSet      //有序
        - java.util.HashSet    //无序 
                 - java.util.LinkedHashSet     //维持元素的插入顺序 
- java.util.Map   键值对  
    - java.util.SortedMap      //有序
        - java.util.TreeMap      //有序
    - java.util.HashMap   //基于 数组 + 链表(1.8增加红黑树)，可以存储null键和null值
            - java.util.LinkedHashMap 
    - java.util.HashTable  //线程安全（方法有synchronized修饰），key还是value都不能为null
- java.util.Iterator      //迭代器 -- 用于遍历集合
- java.util.Comparator    // （外）比较器 -- 用于比较 -- 实现排序
- java.util.Collections      //容器工具类    sort（）方法
- java.lang.Comparable   // （内）比较器 -- 用于比较 -- 实现排序  java Lang包中

![](https://wdsheng0i.github.io/assets/images/2021/java/c1.png)   
![](https://wdsheng0i.github.io/assets/images/2021/java/c2.png) 
 
集合（Collection、List、Set、Map、Comparator、Iterator ）  
集合类只能存放对象，基本数据类型 需要自动装箱为其包装类（对象）使用  

### 3.2 List
1.List的遍历：
``` 
1).  for(int i = 0;i < list.size(); i ++){
    System.out.println(list.get(i));  
}

2）.List<AttrValueDto> attrValueDtoList = dpService.getAttrValueDtoList();
Iterator<AttrValueDto> it = attrValueDtoList.iterator();
while(it.hasNext()){
    AttrValueDto dto = it.next();
}

3).for (BalDto bal : balResp.getQryBalDtoList()) {
AcctBalResp.Balance  balance = new AcctBalResp.Balance();
       BeanUtils.copyProperties(bal, balance);
}
```

2.List判空： 
``` 
if(acctItemDtoList!=null && !acctItemDtoList.isEmpty())
Collections.isEmpty();
```

### 3.2.Map: 
http://www.cnblogs.com/fczjuever/archive/2013/04/07/3005997.html  
https://www.cnblogs.com/bukudekong/p/3889740.html

遍历key和value ： 
``` 
Map<String, Object> map = new HashMap<String, Object>(); 
         map.put(“key”, ObjectValue);
}

遍历1：Iterator<String> iter = map.keySet().iterator();
while (iter.hasNext()) {
    key = iter.next();  //取键
    value = map.get(key);  //取值
}

遍历2：for (String key : map.keySet()) {
    System.out.println(key );  
             value = map.get(key);
}

遍历3： for (Map.Entry<String, String> entry : map.entrySet()) {
 key = entry.getKey() 
    value =  entry.getValue());
} //推荐，尤其是容量大时
```

### 3.3.迭代器（Iterator）
迭代器是一种设计模式，它是一个对象，它可以遍历并选择序列中的对象，而开发人员不需要了解该序列的底层结构。迭代器通常被称为“轻量级”对象，因为创建它的代价小。  
Java中的Iterator功能比较简单，并且只能单向移动：  
- (1) 使用方法iterator()要求容器返回一个Iterator。第一次调用Iterator的next()方法时，它返回序列的第一个元素。注意：iterator()方法是java.lang.Iterable接口,被Collection继承。
- (2) 使用next()获得序列中的下一个元素。
- (3) 使用hasNext()检查序列中是否还有元素。
- (4) 使用remove()将迭代器新返回的元素删除。
- Iterator是Java迭代器最简单的实现，为List设计的ListIterator具有更多的功能，它可以从两个方向遍历List，也可以从List中插入和删除元素。

### 3.4.比较器（Comparable、Comparator）
Comparable可以认为是一个内比较器，  

- 1.很多类都会实现这个接口以提供对该类对象之间比较的默认实现；如String，Integer,Float,Double类
- 2.实现Comparable接口的类提供了自己对象之间比较大小默认实现，可以通过Collections的sort方法自动进行排序
- 3.compareTo方法的返回值是int，有三种情况：
    - 1)、比较者(调用compareTo方法者)大于被比较者（也就是compareTo方法接受对象），那么返回 1
    - 2)、比较者等于被比较者，那么返回0
    - 3)、比较者小于被比较者，那么返回 -1

源码： 
``` 
public interface Comparable<T> {
    public int compareTo(T o); //只有一个方法
}
```

示例：
``` 
public class Domain implements Comparable<Domain> { //实现接口
    ...
    public int compareTo(Domain o) {...}  //实现接口中唯一默认抽象方法
}
```

Comparator可以认为是是一个外比较器

- 1.一个对象实现了Comparable接口，但是开发者认为compareTo方法中的比较方式并不是自己想要的那种比较方式。Comparator接口更多是对特定比较需求提供支持。
- 2.方法返回值和Comparable接口一样是int，有三种情况：
    - 1)、o1大于o2，返回1
    - 2)、o1等于o2，返回0
    - 3)、o1小于o3，返回-1 

源码： 
``` 
public interface Comparator<T> {
    int compare(T o1, T o2);
    boolean equals(Object obj);
}
```

示例：
``` 
public class AbsComparator implements Comparator {
    public int compare(Object o1, Object o2) {
        int v1 = Math.abs(((Integer) o1).intValue());
        int v2 = Math.abs(((Integer) o2).intValue());
        return v1 > v2 ? 1 : (v1 == v2 ? 0 : -1);
    }
}
```

### 3.5.Vector和ArrayList区别
- 1）  Vector的方法都是同步的(Synchronized),是线程安全的(thread-safe)，而ArrayList的方法不是，由于线程的同步必然要影响性能，因此,ArrayList的性能好于Vector。 
- 2） 当Vector或ArrayList中的元素超过它的初始大小时,Vector会将它的容量翻倍,而ArrayList只增加50%的大小，这样,ArrayList就有利于节约内存空间。

### 3.6.HashMap和HashTable
Hashtable和HashMap它们的性能方面的比较类似 Vector和ArrayList，Hashtable的方法是同步的,线程安全  
而HashMap性能好于Hashtable。

### 3.7. ArrayList & LinkedList。
ArrayList：通过下标随机访问元素快，但是插入、删除元素较慢  
LinkedList：插入、删除和移动元素快，但是通过下标随机访问元素性能较低  

ArrayList是基于数组实现的，而LinkedList是基于链表实现的。两种数据结构特点决定了这两个容器的不同之处。

### 3.8.可以使用foreach循环的集合 
``` 
for(元素类型t 元素变量x : 遍历对象obj){     
    引用了x的java语句;
} 
```

- 1.首先增强for循环和iterator遍历的效果是一样的，也就说增强for循环的内部也就是调用iteratoer实现的，
    但是增强for循环有些缺点，例如不能在增强循环里动态的删除集合内容。不能获取下标等。
- 2.ArrayList由于使用数组实现，因此下标明确，最好使用普通 for 循环。
- 3.而对于LinkedList 由于获取一个元素，要从头开始向后找，因此建议使用增强for循环，也就是iterator

### 3.9.Collection和Collections的区别  
- Collection是集合类的上级接口，继承与他有关的接口主要有List和Set
- Collections是针对集合类的一个工具类，他提供一系列静态方法实现对各种集合的搜索、排序、线程安全等操作

### 3.10.早期线程安全的集合 
- 1.Vector（弃用）： Vector和ArrayList类似，是长度可变的数组，与ArrayList不同的是，Vector是线程安全的，它给几乎所有的public方法都加上了synchronized关键字。由于加锁导致性能降低，在不需要并发访问同一对象时，这种强制性的同步机制就显得多余，所以现在Vector已被弃用
- 2.HashTable（弃用）：HashTable和HashMap类似，不同点是HashTable是线程安全的，它给几乎所有public方法都加上了synchronized关键字，还有一个不同点是HashTable的K，V都不能是null，但HashMap可以，它现在也因为性能原因被弃用了
- 3.Stack：栈，也是线程安全的，继承于Vector。

### 3.11.Collections包装方法
Vector和HashTable被弃用后，它们被ArrayList和HashMap代替，但它们不是线程安全的，所以Collections工具类中提供了相应的包装方法把它们包装成线程安全的集合

``` 
List<E> synArrayList = Collections.synchronizedList(new ArrayList<E>());
Set<E> synHashSet = Collections.synchronizedSet(new HashSet<E>());
Map<K,V> synHashMap = Collections.synchronizedMap(new HashMap<K,V>());
```

Collections针对每种集合都声明了一个线程安全的包装类，在原集合的基础上添加了锁对象，集合中的每个方法都通过这个锁对象实现同步

### 3.12.java.util.concurrent包中的集合
- 1.ConcurrentHashMap   
    ConcurrentHashMap和HashTable都是线程安全的集合，它们的不同主要是加锁粒度上的不同。  
    HashTable的加锁方法是给每个方法加上synchronized关键字，这样锁住的是整个Table对象。  
    而ConcurrentHashMap是更细粒度的加锁   
    在JDK1.8之前，ConcurrentHashMap加的是分段锁，也就是Segment锁，每个Segment含有整个table的一部分，这样不同分段之间的并发操作就互不影响 
JDK1.8对此做了进一步的改进，它取消了Segment字段，直接在table元素上加锁，实现对每一行进行加锁，进一步减小了并发冲突的概率
- 2.CopyOnWriteArrayList和CopyOnWriteArraySet    
    它们是加了写锁的ArrayList和ArraySet，锁住的是整个对象，但读操作可以并发执行
- 3.除此之外还有ConcurrentSkipListMap、ConcurrentSkipListSet、ConcurrentLinkedQueue、ConcurrentLinkedDeque等，  
    至于为什么没有ConcurrentArrayList，原因是无法设计一个通用的而且可以规避ArrayList的并发瓶颈的线程安全的集合类，只能锁住整个list，这用Collections里的包装类就能办到

### 3.13.集合使用泛型的好处
对于集合类来说，它们可以存放各种类型的元素。如果在存放之前，就能确定元素的类型，那么就可以更加直观的明确容器保存的是什么类型的数据
集合使用泛型之后，在获取元素的时候，类型会自动转换，避免了手动类型转换的过程，这也是集合使用泛型的最直接的好处。

### 3.14.集合对象里的元素排序
- java.util.Collections 
- java.util.Comparator  常用
- java.lang.Comparable

源码：
``` 
public static <T extends Comparable<? super T>> void sort(List<T> list) {
        Object[] a = list.toArray();
        Arrays.sort(a);
        ListIterator<T> i = list.listIterator();
        for (int j=0; j<a.length; j++) {
            i.next();
            i.set((T)a[j]);
        }
}
public static <T> void sort(List<T> list, Comparator<? super T> c) {
        Object[] a = list.toArray();
        Arrays.sort(a, (Comparator)c);
        ListIterator i = list.listIterator();
        for (int j=0; j<a.length; j++) {
            i.next();
            i.set(a[j]);
}
```

示例：
``` 
List<Person> plist = new ArrayList<Person>();
List<String> slist = new ArrayList<String>();
Person p1 = new Person("0001","zhangsan",32);  
Person p2 = new Person("0002","lisi",20);
plist.add(p1);
plist.add(p2);
slist.add("1");
slist.add("2");
Collections.sort(slist);    //String有默认比较器实现
Collections.sort(plist, new Comparator<Person>(){  //Person无默认比较器实现
    public int compare(Person p1, Person p2) {  //按照Person的年龄进行升序排列
        if(p1.getAge() > p2.getAge()){
            return 1;
        }
        if(p1.getAge() == p2.getAge()){
            return 0;
        }
        return -1;
    }
});
```

四、网络编程

