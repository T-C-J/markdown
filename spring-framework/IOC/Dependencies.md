### Dependencies

----

##### Dependency Injection

* [Constructor-based dependency injection](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-constructor-injection)
* [Setter-based dependency injection](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-setter-injection)



#### 依赖解析过程

---

* ApplicationContext 通过各种方式创建所有bean的配置元数据
* 当要实例化一个bean时，需要提供实例它所需要的数据，比如构造器参数等
* 每个属性或构造函数的参数值是从其指定的格式转换为实际的属性或构造函数参数的类型。默认情况下，Spring可以提供的值转换为字符串格式的所有内置类型，如int、long、字符串、布尔值，等等。



#### 循环依赖

> 两个类都只能通过构造器注入所依赖的彼此，则会抛出 BeanCurrentlyInCreationException

解决方式

* 通过setter注入







### 方法注入

---

##### 查询方法注入

* 可以注入到抽象方法上，spring会动态生成该类的子类实现对该方法的注入

~~~xml
<bean id="myCommand" class="tcj.test.lookup.Command" scope="prototype">
    <!-- inject dependencies here as required -->
</bean>
<!-- commandProcessor uses statefulCommandHelper -->
<bean id="commandManager" class="tcj.test.lookup.CommandManager">
    <lookup-method name="createCommand" bean="myCommand"/>
</bean>
~~~

~~~java
public class Command {
	private Object status;
	public Object getStatus() {
		return status;
	}
	public void setStatus(Object status) {
		this.status = status;
	}
	public Object execute(){
		return "execute";
	};
}
~~~

~~~java
public abstract class CommandManager {
	public Object process(Object commandState) {
		// grab a new instance of the appropriate Command interface
		Command command = createCommand();
		// set the state on the (hopefully brand new) Command instance
		command.setStatus(commandState);
		return command.execute();
	}
	// okay... but where is the implementation of this method?
	protected abstract Command createCommand();
}
~~~

~~~java
public static void main(String[] args) {
		ApplicationContext applicationContext = new ClassPathXmlApplicationContext("lookup.xml");
		CommandManager bean = applicationContext.getBean(CommandManager.class);
		Object string = bean.process("string");
	}
~~~



##### 任意方法替换
> 需要实现 org.springframework.beans.factory.support.MethodReplacer 接口
* 可以替换一个任意已实现的方法，不用修改方法所在的类
~~~xml
<bean id="myValueCalculator" class="tcj.test.replace.MyValueCalculator">
		<!-- arbitrary method replacement -->
		<replaced-method name="computeValue" replacer="replacementComputeValue">
			<arg-type>String</arg-type>
		</replaced-method>
</bean>
<bean id="replacementComputeValue" class="tcj.test.replace.ReplacementComputeValue"/>
~~~

~~~java
public class ReplacementComputeValue implements MethodReplacer {
	@Override
	public Object reimplement(Object obj, Method method, Object[] args) throws Throwable {		 // get the input value, work with it, and return a computed result
		String input = (String) args[0];
		return “replace method return”;
	}
}
~~~

~~~java
public class MyValueCalculator {
	public String computeValue(String input) {
		// some real code...
		return "real method";
	}
}
~~~

