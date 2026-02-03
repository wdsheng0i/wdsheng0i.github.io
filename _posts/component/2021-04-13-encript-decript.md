---
layout: post
title: 加解密算法
category: component
tags: [ component ]
---

加解密算法

## 参考资料

- [常用的加密算法](https://blog.csdn.net/liaomin416100569/article/details/75646029)
- [夏冰加密软件技术博客](http://www.jiamisoft.com/blog/category/jiamisuanfa)

数据加密的基本过程就是对原来为明文的文件或数据按某种算法进行处理，使其成为不可读的一段代码，通常称为“密文”，使其只能在输入相应的密钥之后才能显示出本来内容，通过这样的途径来达到保护数据不被非法人窃取、阅读的目的。

该过程的逆过程为解密，即将该编码信息转化为其原来数据的过程。

## 一、摘要算法：不可逆的算法，如：MD5、SHA

是一种不可逆的算法，如：MD5、SHA，一般进行散列时最好提供一个salt（盐）

- MD5算法 :（Message Digest Algorithm 5） 可以保证数据传输完整性和一致性 摘要后长度为16字节 摘要信息中不包含原文信息  
  所有加密结果不可逆（无法解密） 一般在传送文件时 对源文件进行md5 hash 传送到对方后 检测hash值是否相等 如果相等文件传输正确
  如果不相等 说明文件被篡改（加入木马）或者未传送完成

```
MessageDigest md=MessageDigest.getInstance("MD5") ;
String code="hello";
byte[] bt=md.digest(code.getBytes());

//或者
String str  = "hello";
String salt = "1234";
String md5 = new Md5Hash(str,salt).toString();
//还可以指定散列次数  
new Md5Hash(str,salt,2).toString();   //相当于进行了两次MD5加密
```

- SHA算法: Secure Hash Algorithm（安全hash算法） 安全散列算法（hash函数 将原始信息压缩
  返回散列值）可以是SHA-1，SHA1是目前最安全  
  还有**SHA512、SHA256、SHA384**

```
MessageDigest md=MessageDigest.getInstance("SHA") ;//或者SHA-1 SHA1
String code="hello";
byte[] bt=md.digest(code.getBytes());
//或者		
String str = "hello";  
String salt = "123";  
String sha1 = new Sha256Hash(str, salt).toString();
```

- 通用的散列SHA算法

```
String str = "hello";  
String salt = "123";  
//内部使用MessageDigest  
String simpleHash = new SimpleHash("SHA-1", str, salt).toString(); 
```

## 二、编码和解码：Base64、16进制编码Hex

- Base64编码: 用于将字节数组和字符串互相转换

```
String str = "hello";
//编码
String base64Encode = Base64.encodeToString(str.getBytes());
//解码
String str2 = Base64.decodeToString(base64Encode);
```

- 16进制 编码: 计算机系统使用 2进制 为了编写存储方便一般将2进制 转换为16进制字符串  
  其中base64也是其中类似转换一种 16进制编码和base64都是

```
String str = "hello";
//编码
String base64Encode = Hex.encodeToString(str.getBytes());
//解码
String str2 = new String(Hex.decode(base64Encode.getBytes()));
```

- byte/String 互相转换

```
String str="hello";
byte[] b = CodecSupport.toBytes(str);
String result = CodecSupport.toString(b);
```

## 三、对称加密：DES、AES

- DES算法:（Data Encryptin Standard） 对称加密算法, 使用秘钥加解密 秘钥必须是56字节
- AES算法:（Advanced Encryptin Standard 高级加密标准） 是对称加密算法一种升级 因为 56位秘钥 在计算机系统性能越来越高的前提下
  56位很容易被破解
- PBE算法 :（Password Base Encryption） 基于自定义口令的加解密算法 定义口令 同时还必须定义 盐和 使用盐混淆的次数

## 四、非对称加密：RSA、EC

- RSA算法: 目前影响力最大的非对称加密算法   
  一般公钥对外公开 加密后传送给服务器 服务器使用独有的私钥解密     
  加密的数据在传输过程是无法破解的 秘钥对初始化大小必须是64的倍数 只能在512-1024中

- DH算法: 是一种对称加密到非对称加密的过度算法 使用DH算法生成密钥对 使用对称加密算法获取秘钥后 进行加解密 双方必须都存在公钥和私钥
- EC算法：椭圆曲线密码学(Elliptic curve cryptography),简称ECC,是一种建立公开密钥加密的算法,也就是非对称加密。
- ElGamal加密算法是一个基于迪菲-赫尔曼密钥交换的非对称加密算法

## 五、数据签名：DSA

- 签名是非对称加密技术和摘要技术的综合运用用户A将明文和使用私钥加密的明文摘要一起发送给用户B 用户B使用公钥解密出摘要
  然后使用相同的摘要进行签名比对

## 六、国密算法  SM1-SM4

- [国密算法介绍](https://zhuanlan.zhihu.com/p/132352160)

国密算法是我国自主研发创新的一套数据加密处理系列算法。从SM1-SM4分别实现了对称、非对称、摘要等算法功能。

国密即国家密码局认定的国产密码算法，即商用密码。

- SM1 为对称加密；加密强度为128位，采用硬件实现；
- SM2为非对称加密，基于ECC；，其加密强度为256位
- SM3 消息摘要；密码杂凑算法，杂凑值长度为32字节 ；
- SM4 对称加密算法，随WAPI标准一起公布，可使用软件实现，加密强度为128位;







