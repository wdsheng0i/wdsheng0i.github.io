---
layout: post
title: 异常 IO 序列化
category: java
tags: [java]
---

异常 IO 序列化

## 参考资料

## 一、异常继承关系 
![](https://wdsheng0i.github.io/assets/images/2021/java/exc.png)

### 1.RuntimeException示例
```
1，ClassCastException
    Object x = new Integer(0);
        System.out.println((String)x);
    当试图将对象强制转换为不是实例的子类时，抛出该异常（ClassCastException)

2，ArithmeticException
    int a=5/0;
    一个整数“除以零”时，抛出ArithmeticException异常。

3, NullPointerException
    String s=null;
    int size=s.size();
    当应用程序试图在需要对象的地方使用 null 时，抛出NullPointerException异常

4, StringIndexOutOfBoundsException
    "hello".indexOf(-1);
指示索引或者为负，或者超出字符串的大小，抛出StringIndexOutOfBoundsException异常

5，NegativeArraySizeException
    String[] ss=new String[-1];
    如果应用程序试图创建大小为负的数组，则抛出NegativeArraySizeException异常
```

### 2.Exception 和 error 区别
error：是不可捕捉到的，无法采取任何恢复的操作，顶多只能显示错误信息。不可控制的
表示系统级的错误和程序不必处理的异常

Exception ：表示可恢复的例外，这是可捕捉到的，是可被控制(checked) 或不可控制的
表示需要捕捉或者需要程序进行处理的异常，

### 3.checked exception 和 runtime exception
Java提供了两类主要的异常:运行时异常runtime exception和一般异常checked exception。checked 异常。对于后者这种异常，JAVA要求程序员对其进行catch。所以，面对这种异常不管我们是否愿意，只能自己去写一大堆catch块去处理可能的异常。  

运行时异常我们可以不处理。这样的异常由虚拟机接管。出现运行时异常后，系统会把异常一直往上层抛，一直遇到处理代码。如果不对运行时异常进行处理，那么出现运行时异常之后，要么是线程中止，要么是主程序终止。 

### 4. throws和throw区别
- 1、throws出现在方法函数头；而throw出现在函数体。
- 2、throws表示出现异常的一种可能性，并不一定会发生这些异常；throw则是抛出了异常，执行throw则一定抛出了某种异常。
- 3、两者都是消极处理异常的方式（这里的消极并不是说这种方式不好），只是抛出或者可能抛出异常，但是不会由函数去处理异常，真正的处理异常由函数的上层调用try...catch...处理

### 5.常见问题
> finally是否一定执行?     try执行finally就一定执行？    return与finally先后？

1、不管有木有出现异常，finally块中代码都会执行； （除非程序没有走到try就已经返回   或者  程序终止）

2、当try和catch中有return时，finally仍然会执行；

3、finally是在return语句执行之后，返回之前执行的（此时并没有返回运算后的值，而是先把要返回的值保存起来，不管finally中的代码怎么样，返回的值都不会改变，仍然是之前保存的值），所以函数返回值是在finally执行前就已经确定了；

4、finally中如果包含return，那么程序将在这里返回，而不是try或catch中的return返回，返回值就不是try或catch中保存的返回值了。

### 6.自定义异常实现统一异常处理
- @ExceptionHandler + 自定义异常 实现统一异常处理 

```
//1.自定义异常
@Getter
public class ServiceException extends RuntimeException {
    private final int code;
    private final String message;
    private JSONObject data;

    public ServiceException() {
        this(ErrorCodeEnum.ERROR.getCode(), ErrorCodeEnum.ERROR.getMessage());
    }

    public ServiceException(ErrorCodeEnum errorCodeEnum) {
        this(errorCodeEnum.getCode(), errorCodeEnum.getMessage());
    }
    
    @Override
    public String toString() {
        JSONObject result = new JSONObject();
        result.put("resultCode", code);
        result.put("resultMessage", message);
        return result.toString();
    }
}

//2.统一异常处理
@Slf4j
@RestControllerAdvice
public class ServiceExceptionHandler {
    @ExceptionHandler(ServiceException.class)
    public ResultBean processException(HttpServletRequest request, ServiceException ex, HandlerMethod handler) {
        log.error(toLogMsg(request, ex, handler), ex);
        return new ResultBean(ex.getCode(), ex.getMessage(), ex.getData());
    }
}

//3.业务显示抛异常
public List<TreeBeen<Area>> getAeraTree(){
    if(null == areaId){
        throw new ServiceException(ErrorCodeEnum.NOT_EXIST);
    }
}
```

## 二、IO  
![](https://wdsheng0i.github.io/assets/images/2021/java/io.png)   

字节流读取的时候，读到一个字节就返回一个字节；  
  
字符流使用了字节流读到一个或多个字节（中文对应的字节数是两个，在UTF-8码表中是3个字节）时。先去查指定的编码表，将查到的字符返回

一个英文字符 A占一个字节，一个汉字字符 “你” 占2个字节，  1字节(byte)=8位(bit)  

- 字符流: 处理的单元为 2 个字节的 Unicode 字符，分别操作 字符、字符数组、字符串   Reader、Writer 
- 字节流: 处理单元为 1 个字节，操作 字节、字节数组。  InputStream、OutputStream
- 字节流: 可以处理所有类型数据，如：图片，MP3，AVI视频文件，
- 字符流: 只能处理字符数据。只要是处理纯文本数据，就要优先考虑使用字符流，除此之外都用字节流



### 2.1 BIO：同步并阻塞

### 2.2 AIO：异步非阻塞

### 2.3 NIO：同步非阻塞

### IO操作为什么必须关闭流
java在对象的引用被消除之后,正常情况下，内存资源是会被垃圾回收。

使用完IO流，必须手动回收。
一般来说，需要自己close的东西，都是用了虚拟机之外的资源，例如端口，显存，文件等，虚拟机无法通过垃圾回收释放这些资源，只能显式调用close方法来释放。比如释放占用的端口,文件句柄,网络操作数据库应用等

### ByteArrayOutputStream或ByteArrayInputStream不需要关闭流原因分析
ByteArrayOutputStream或ByteArrayInputStream是内存读写流，不同于指向硬盘的流，它内部是使用字节数组读内存的，这个字节数组是它的成员变量，当这个数组不再使用变成垃圾的时候，Java的垃圾回收机制会将它回收。所以不需要关流

## 三、序列化