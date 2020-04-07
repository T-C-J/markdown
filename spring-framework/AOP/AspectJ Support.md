#### AspectJ support

---

##### 开启AspectJ Support

~~~java
@Configuration
@EnableAspectJAutoProxy
public class AppConfig {

}
~~~

~~~xml
<aop:aspectj-autoproxy/>
~~~



##### 定义一个 Aspect

---

~~~java
package org.xyz;
import org.aspectj.lang.annotation.Aspect;

@Aspect
public class NotVeryUsefulAspect {

}
~~~

~~~xml
<bean id="myAspect" class="org.xyz.NotVeryUsefulAspect">
    <!-- configure properties of the aspect here -->
</bean>
~~~



##### 定义一个切点

---

~~~JAVA
@Pointcut("execution(* transfer(..))") // the pointcut expression
private void anyOldTransfer() {} // the pointcut signature
~~~

* execution：表达式含义

  ```
   execution(modifiers-pattern? ret-type-pattern declaring-type-pattern?name-pattern(param-pattern)
                  throws-pattern?)
  ```

* within：表达式种所有类的所有的方法都会执行(不包含子包) aop方法
* this：实现了该接口的类、继承该类、该类本身的类---的所有方法（包括不是接口定义的方法，但不包含父类的方法）都会执行aop方法
* target：实现了该接口的类、继承该类、该类本身的类---的所有方法（包括不是接口定义的方法，包含父类的方法）
* args：当执行的方法的参数是指定类型时生效。
* @target：当代理的目标对象上拥有指定的注解时生效。
* @args：当执行的方法参数类型上拥有指定的注解时生效。
* @within：注解**标注在的类上**；该类的所有方法（不包含子类方法）执行aop方法
* @annotation：在注解标注的方法上执行aop方法



合并多个切点

~~~java
@Pointcut("execution(public * *(..))")
private fun anyPublicOperation() {} 

@Pointcut("within(com.xyz.someapp.trading..*)")
private fun inTrading() {} 

@Pointcut("anyPublicOperation() && inTrading()")
private fun tradingOperation() {} 
~~~



---


> Examples

执行所有的publish方法

~~~java
 execution(public * *(..))
~~~

执行所有方法名为`set`开头的方法

~~~java
 execution(* set*(..))
~~~

执行所有定义在`AccountService` 的方法

~~~java
 execution(* com.xyz.service.AccountService.*(..))
~~~

执行所有`service`包下的方法

~~~java
 execution(* com.xyz.service.*.*(..))
~~~

执行所有`service`包下或它的子包下的所有方法

~~~java
 execution(* com.xyz.service..*.*(..))
~~~

执行所有在`service`包内的方法

~~~java
 within(com.xyz.service.*)
~~~

执行所有`service`包下或它的子包下的所有方法

~~~java
 within(com.xyz.service..*)
~~~

执行所有实现了`AccountService` 接口的方法 （包括不是接口定义的方法，但不包含父类的方法）

~~~java
 this(com.xyz.service.AccountService)
~~~

执行所有实现了`AccountService` 接口的方法 （包括不是接口定义的方法，包含父类的方法）

```
 target(com.xyz.service.AccountService)
```

执行所有一个参数而且参数实现了`Serializable`的方法

~~~java
 args(java.io.Serializable)
~~~

执行所有有`@Transactional` 注解的方法（标注在类上）

```java
 @target(org.springframework.transaction.annotation.Transactional)
```

执行含有注解`@Transactional`标注在的类上,该类的所有方法（不包含子类方法）执行aop方法

```java
 @within(org.springframework.transaction.annotation.Transactional)
```

执行所有含有`@Transactional`注解的方法

```java
 @annotation(org.springframework.transaction.annotation.Transactional)
```

执行所有含有一个参数而且参数含有`@Classified`注解的方法

```java
 @args(com.xyz.security.Classified)
```

执行一个spring bean 的 name 为`tradeService`的所有方法

```java
 bean(tradeService)
```

执行spring bean 的 name 为所有以`Service`结尾类的所有方法

```java
 bean(*Service)
```





#### 定义策略

---

##### Before Advice

```java
@Aspect
class BeforeExample {
    @Before("com.xyz.myapp.SystemArchitecture.dataAccessOperation()")
    fun doAccessCheck() {
        // ...
    }
}

@Aspect
class BeforeExample {
    @Before("execution(* com.xyz.myapp.dao.*.*(..))")
    fun doAccessCheck() {
        // ...
    }
}
```



##### After Returning Advice

```java
@Aspect
public class AfterReturningExample {
    @AfterReturning("com.xyz.myapp.SystemArchitecture.dataAccessOperation()")
    public void doAccessCheck() {
        // ...
    }
}

// 含有返回值的 returning="retVal"
@Aspect
public class AfterReturningExample {
    @AfterReturning(
        pointcut="com.xyz.myapp.SystemArchitecture.dataAccessOperation()",
        returning="retVal")
    public void doAccessCheck(Object retVal) {
        // ...
    }
}
```



##### After Throwing Advice

```java
@Aspect
public class AfterThrowingExample {
    @AfterThrowing("com.xyz.myapp.SystemArchitecture.dataAccessOperation()")
    public void doRecoveryActions() {
        // ...
    }
}

// 获取 Exception 信息
@Aspect
public class AfterThrowingExample {
    @AfterThrowing(
        pointcut="com.xyz.myapp.SystemArchitecture.dataAccessOperation()",
        throwing="ex")
    public void doRecoveryActions(Exception ex) {
        // ...
    }
}
```

* 可以指定匹配的异常类型，如果异常不匹配不执行

```java
@Aspect
public class AfterThrowingExample {
    @AfterThrowing(
        pointcut="com.xyz.myapp.SystemArchitecture.dataAccessOperation()",
        throwing="ex")
    public void doRecoveryActions(DataAccessException ex) {
        // 只有当抛出 DataAccessException 异常时才执行该方法
        // ...
    }
}
```



##### After (Finally) Advice

```java
@Aspect
public class AfterFinallyExample {
    @After("com.xyz.myapp.SystemArchitecture.dataAccessOperation()")
    public void doReleaseLock() {
        // ...
    }
}
```



##### Around Advice

```java
@Aspect
public class AroundExample {
    @Around("com.xyz.myapp.SystemArchitecture.businessService()")
    public Object doBasicProfiling(ProceedingJoinPoint pjp) throws Throwable {
        // start stopwatch
        Object retVal = pjp.proceed(); // 调用真实方法
        // stop stopwatch
        return retVal;
    }
}
```



#### 获取参数

---

##### 从 JoinPoint 获取

* 所有的策略都可以有一个类型为`org.aspectj.lang.JoinPoint`的参数
* Around Advice 的参数为`org.aspectj.lang.JoinPoint`的子类 `ProceedingJoinPoint`
  * `getArgs()`: Returns the method arguments.
  * `getThis()`: Returns the proxy object.
  * `getTarget()`: Returns the target object.
  * `getSignature()`: Returns a description of the method that is being advised.
  * `toString()`: Prints a useful description of the method being advised.



##### 将参数传递给 advice

```java
@Before("com.xyz.myapp.SystemArchitecture.dataAccessOperation() && args(account,..)")
public void validateAccount(Account account) {
    // ...
}
```

```java
@Pointcut("com.xyz.myapp.SystemArchitecture.dataAccessOperation() && args(account,..)")
private void accountDataAccessOperation(Account account) {}

@Before("accountDataAccessOperation(account)")
public void validateAccount(Account account) {
    // ...
}
```



##### 参数和泛型

```java
public interface Sample<T> {
    void sampleGenericMethod(T param);
    void sampleGenericCollectionMethod(Collection<T> param);
}
```

```java
@Before("execution(* ..Sample+.sampleGenericMethod(*)) && args(param)")
public void beforeSampleMethod(MyType param) {
    // Advice implementation
}
```

```java
@Before("execution(* ..Sample+.sampleGenericCollectionMethod(*)) && args(param)")
public void beforeSampleMethod(Collection<MyType> param) {
    // Advice implementation
}
```

