### Container

---

* *org.springframework.context.ApplicationContext*  是容器的接口
* 容器是对bean的集合，保存对象的实例，配置元数据等信息的BeanDefinition  
* 配置元数据可以从XML文件，注解以及JAVA代码中获取
* 管理至少一个 bean



#### Configuration Metadata

---

* 表示指定spring去怎样创建类的实例
* 保存了类的信息，例如 生成实例时机，是否单例，依赖关系等
* 由用户自定义
* 表示形式
  * XML-based configuration
  * [Annotation-based configuration](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-annotation-config)
  * [Java-based configuration](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-java)

##### XML-based configuration

---

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="..." class="...">  
        <!-- collaborators and configuration for this bean go here -->
    </bean>
    <bean id="..." class="...">
        <!-- collaborators and configuration for this bean go here -->
    </bean>
    <!-- more bean definitions go here -->
</beans>
~~~







#### 创建容器实例

---

~~~java
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");
~~~





#### 使用容器

---

~~~java
// create and configure beans
ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");
// retrieve configured instance
PetStoreService service = context.getBean("petStore", PetStoreService.class);
// use configured instance
List<String> userList = service.getUsernameList();
~~~

