#### **@DeclareParents**

----

> 为已有的类追加接口与接口的默认实现

~~~java
//value表示要需要添加接口的类
//defaultImpl 表示添加接口的默认实现
@DeclareParents(value="com.xzy.myapp.Service+", defaultImpl=DefaultUsageTracked.class)
public UsageTracked mixin;
~~~



#### Example

---

`假设我们的目标类是一个女人，她的核心方法为喜欢帅哥，但是我们又要为该方法添加一个新的功能，建立一个新的雌性类，该类中的方法为非常喜欢吃，把此功能添加到原目标类中。`

```java
package com.lzj.spring.annotation;

public interface Person {
    void likePerson();
}
```

```java
package com.lzj.spring.annotation;
import org.springframework.stereotype.Component;

@Component("women")
public class Women implements Person {

    @Override
    public void likePerson() {
        System.out.println("我是女生，我喜欢帅哥");
    }
}
```

```java
package com.lzj.spring.annotation;
import org.springframework.stereotype.Component;

public interface Animal {
    void eat(); 
}
```

```java
package com.lzj.spring.annotation;
import org.springframework.stereotype.Component;

@Component
public class FemaleAnimal implements Animal {

    @Override
    public void eat() {
        System.out.println("我是雌性，我比雄性更喜欢吃零食");
    }
}
```

```java
package com.lzj.spring.annotation;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.DeclareParents;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class AspectConfig {
    //"+"表示person的所有子类；defaultImpl 表示默认需要添加的新的类
    @DeclareParents(value = "com.lzj.spring.annotation.Person+", defaultImpl = FemaleAnimal.class)
    public Animal animal;
}
```

```java
package com.lzj.spring.annotation;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class MainTest {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(AnnotationConfig.class);
        Person person = (Person) ctx.getBean("women");
        person.likePerson();
        Animal animal = (Animal)person;
        animal.eat();
    }
}
```

```
我是女生，我喜欢帅哥
我是雌性，我比雄性更喜欢吃零食
```

