---
layout: post
title: 接口鉴权、参数校验、签名验证、防刷、幂等
category: component
tags: [ component ]
---

接口鉴权

## 参考资料

## 一、token验证 + session超期验证

拦截器统一拦截请求

```
@Slf4j
public class AuthInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o) throws Exception {
        return this.authentic(o, httpServletRequest, httpServletResponse);
    }
    
    //是否有权限
    private boolean authentic(Object handler, HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) {
        if (handler instanceof HandlerMethod) {
            String token = httpServletRequest.getHeader("token");
            if (StringUtils.isBlank(token)) {
                httpServletResponse.setStatus(ErrorCodeEnum.INVALID_SESSION.getCode());
                return false;
            }
            String userString = (String) httpServletRequest.getSession().getAttribute("user");
            if (userString == null) {
                log.info("没有获取到session信息");
                httpServletResponse.setStatus(HttpStatus.UNAUTHORIZED.value());
                return false;
            }
        }
```

## 二、Permissions接口权限字验证

用户-角色-权限字: 接口需要的权限字

```
1.@Permissions自定义注解
/**
 * 自定义权限注解
 */
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
public @interface Permissions {
    //api接口所需权限字集合    
    String[] value();
    // 逻辑关系
    Logical logical() default Logical.NONE; //AND, OR, NONE
}

2.拦截器方式实现接口鉴权
@Slf4j
public class AuthInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o) throws Exception {
        return this.authentic(o, httpServletRequest, httpServletResponse);
    }
    
    /**
     * 是否有权限
     * @param handler
     * @return 是否有权限
     */
    private boolean authentic(Object handler, HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse) {
        if (handler instanceof HandlerMethod) {
            // 2.1.获取api接口方法上的权限注解
            Permissions requirePermissions = handlerMethod.getMethod().getAnnotation(Permissions.class);
            if(requirePermissions != null){
                // TODO 2.2.获取当前请求, 用户的角色-权限
                String[] permissions = requirePermissions.value();
                Logical logical = requirePermissions.logical();
                // TODO 2.3.判断是否有接口要求的权限字    
                if (有接口要求的权限字) {
                    return true;
                }
                httpServletResponse.setStatus(HttpStatus.UNAUTHORIZED.value());
                return false;
            }
        }
    }

3.应用
@Permissions(value = {"userAdd", "userDel"}, logical = Logical.OR)
@PostMapping(value = "/user/list")
public Map userList(@RequestBody UserRequest requestParam){
    ...
}
```

## 三、validation请求参数校验

javax.validation + @validated实现方式

```
1.定义请求参数
@Data
public class UserRequest {
    private Integer userId;

    @NotNull(message = "名字不能为空")
    private String name;

    @NotNull(message = "年龄不能为空")
    private Integer age;

}

2.参数校验应用
@Permissions(value = {"userAdd"})
@PostMapping(value = "/user/add")
public Map userAdd(@Validated @RequestBody UserRequest addParam){
    ...
}
```

javax.validation + @validated + groups分组校验

```
@Data
public class UserRequest {

    @NotNull(groups = {update.class},message = "id不能为空")
    private Integer userId;
    
    @NotNull(groups = {add.class, update.class},message = "名字不能为空")
    @Pattern(regexp = "[\\u4e00-\\u9fa5_a-zA-Z0-9_]{1,20}", groups = {add.class, update.class},message = "名字不能为空")
    private String name;

    @NotNull(groups = {add.class, update.class},message = "年龄不能为空")
    private Integer age;

    public interface add{}

    public interface update{}
}

@Permissions(value = {"userAdd"})
@PostMapping(value = "/user/add")
public Map userAdd(@Validated({UserRequest.add.class}) @RequestBody UserRequest addParam){
    ...
}
```

## 四、signture无状态接口签名验证

| 参数          | 值                       | 说明                         |
|-------------|-------------------------|----------------------------|
| appId	      | wdsswds12wds2dsads	     | 渠道参数，每个接入渠道分配唯一            |
| appSecert	  | wds1sewdssd1wds	        | 渠道接入秘钥                     |
| RSAPublic	  | MIGwds0GCSqGWDS3DQEB…	  | RSA公钥，用于调用接口前，加密报文         |
| RSAPrivate	 | MIIwdsIBAWDSBgkqhkiGF…	 | RSA私钥，用于调用接口后，接收到返回的报文进行解密 |

4.1 输入格式规范：

```
{
    "head": {
        "appId": "分配的appId",
        "timeStamp": "yyyymmddhhmmss",
        "sequenceId": "请求的唯一序列uuid",
        "signture": "签名值"
    },
    "body": {
        "reqType": "方法名",
        "reqBody": "Base64(RSA加密(reqParam))"  //reqParam 是json格式参数，每个具体接口定义
    }
}


```

4.2 输出格式规范：

```
{
    "head": {
        "appId": "分配的appId",
        "timeStamp": "yyyymmddhhmmss",
        "sequenceId": "请求的唯一序列uuid",
        "signture": "签名值"
    },
    "body": {
        "respType": "方法名",
        "resultCode": "200",
        "resultMsg": "success",
        "respBody": "Base64解析(RSA解密(respData))"  //respData 是json格式返回值，每个具体接口返回
    }
}
```

4.3 signture签名过程

- 对body参数排序，拼接密钥，摘要算法加密，转16进制 ==> 生成的值作签名值，为防止信息传输过程中篡改 校验使用
- 请求者拿到响应后签名与原签名值比对

## 五、接口防刷

[一个注解搞定接口防刷！还有谁不会？](https://mp.weixin.qq.com/s/KeWZmdMtUNLXd5Gb9GrITw)

## 六、SpringBoot[接口幂等性的实现](http://www.mydlq.club/article/94/)

幂等（idempotent、idempotence）是一个数学与计算机学概念，常见于抽象代数中。   
在编程中一个幂等操作的特点是其任意多次执行所产生的影响均与一次执行的影响相同。

简单的说幂等性就是验证数据的一致性和事务的完整性。

### 6.1 什么情况下会出现幂等性的场景：

- 用户重复提交
- 网络重发
- 消息重发
- 系统间重试

### 6.2 [数据库操作幂等性分析：](http://www.kokojia.com/article/49852.html)

- insert操作不满足幂等性，那么唯一索引可以是幂等问题的解法。数据库会帮助我们执行唯一性检查，相同数据不会重复落库。
- update操作部分不满足幂等性，计算式update不具备幂等性，update goods set number=nember-1 where id=1
- select操作满足幂等性
- delete操作满足幂等性

### 6.3 幂等性解决方案

- 唯一索引：数据库建立合适的字段唯一索引，重复insert时会校验
- 悲观锁for update：数据锁定较长场景使用，select * from t_order where order_id = trade_no for update;
    - 当线程A执行for update，数据库会对当前记录加锁，其他线程执行到此行代码的时候，会等待线程A释放锁之后，才可以获取锁，继续后续操作。
    - 事务提交时，for update获取的锁会自动释放。
- 乐观锁：更新时 使用版本号操作，update xxx set name ='xxx' ,version= version+1 where id=xxx and version='xxx'
- 分布式锁：插入或更新时获取锁，操作完毕再释放，防止重复更新

## 七、API面试四连杀：[接口如何设计？安全如何保证？签名如何实现？防重如何实现？](https://mp.weixin.qq.com/s/HnrjbeMlV-FvW1mDT_r9ww)



