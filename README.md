# SpringGuide

# Core

## History
1996 -- Java Beans
1998 -- EJB
2003 -- Spring

**POJO** -- plain old java object -- single responsability + getter, setter
**SOLID**

Dependencies required for DI (dependency injection):
- core
- beans
- context

Why do we need DI?

- Less hard relations between classes
- Easily write tests

Why do we need tests?

Imagine we don't have it. One programmer push to master, second push, the deployed it and nothing works. If tests had existed the would have seen errors rapidly.

## Ways of initialising context:
# XML
**config.xml**
```xml
<bean id="id" class="com.***.MyClass">
```

**Application.java**
```java
public static void main(String[] args) {

  ClassPathXmlApplicationContext ctx = new ClassPathXmlApplicationContext("config.xml");
  MyClass myClass = ctx.getBean(Myclass.class);
}
```

# Annotations
**Config.class**

```java
@Configuration (
  componentScan = "com.core.components.*"
)
public class Config {

  @Bean 
  MyClass myClass() {
    return new MyClass();
}
```

**MyComponent.class** in package ```com.core.components``` 

```java
@Component
public class MyComponent {
  ***
}
```

**Application.java**
```java
public static void main(String[] args) {

  AnnotationApplicationContext ctx = new AnnotationApplicationContext(Config.class);
  MyClass myClass = ctx.getBean(Myclass.class);
  MyComponent myComponent = ctx.getBean(MyComponent.class);
}
```

## Initialising beans with some dependencies:
Imagine now *MyClass* is an iterface we have classes:

**MyClass.class** in package ```com.core.components``` 

```java
public @interface MyClass {

  void doSomeJob();
}
```


**MyClassImpl.class** in package ```com.core.components``` 

```java
public class MyClassImpl implements MyClass {

  @Override
  public void doSomeJob() {
    System.out.println("Job done").
  }
  
}
```

**MyComponent.class** in package ```com.core.components``` 

```java
public class MyComponent {

  MyClass myClass;
  
  MyComponent (MyClass myClass) {
    this.myClass = myClass;
  }
}
```

To inject MyClassImpl bean in MyComponent bean we should do: 

# XML
**config.xml**
```xml
<bean id="my_class" class="com.***.MyClassImpl">
  
<bean id="my_component" class="com.***.MyComponent">
  <constructor-arg name="myclass" ref="my_class" /> 
  
```
//If we use setter injection, the key is ```property``` instead of ```constructor-arg```

**Application.java**
```java
public static void main(String[] args) {

  ClassPathXmlApplicationContext ctx = new ClassPathXmlApplicationContext("config.xml");
  MyClass myClass = ctx.getBean(MyClass.class);
}
```

# Annotations
**Config.class**

```java
@Configuration (
  componentScan = "com.core.components.*"
)
public class Config {

  @Bean 
  MyClass myClass() {
    return new MyClass();
}
```

**MyComponent.class** in package ```com.core.components``` 

```java
@Component
public class MyComponent {
  MyClass myClass
  
  @Autowired
  MyComponent (MyClass myClass) {
    this.myClass = myClass;
  }
}
```

**Application.java**
```java
public static void main(String[] args) {

  AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(Config.class);
  MyClass myClass = ctx.getBean(Myclass.class);
  MyComponent myComponent = ctx.getBean(MyComponent.class);
}
```

### What is bean definition? It is information, based on that bean of object is created.

## Types of injection
- by field
  **bad** because 
  - if Spring don't find any bean to inject inito our field it put null instead and we will get NPE;
  - it is hard to debug, hard to see dependencies;
  - bad for testing, because hard to mock.
- by setter 
  for optional
- by constructor
  **best** 
  - can find cyclic dependencies.
  
  ##BeanFactory vs ApplicationContext
  BeanFactory
  - Can instantiate and link beans
  - Do not have postprocessors, internatializers and work with events
  
  ##Questions
  1. Is it allowed to use both setter and constructor injections
  2. Servlet conatainer. What is the responsability?
  
  
  ## Spring AOP
  
  Required:
  - `@EnableAspectJProxy`-- spring.context
  - `@Aspect` class -- spring-aspects
  
  So that all the classes who sutisfies the Pointcut will be proxied by Dynamic Proxy or CGlib.
  ### Dynamic proxy -- powered by JDK (reflection package). It is used in case when proxied class implements one or more interfaces. So the reference to that class will be changed to reference on proxy because it will implement all the interfaces too. BUT you won't be able to autowire your class by default.
  ### CGlib proxy -- powered by CGlib. It is used when proxied class implements nothing. So that proxy class will extend it.
  
  ### Aspect class
  - We need @PointCut -- the condition to methods be covered by proxy.
  - Then there are some annotations: @Before, @After, @AfterReturn, @AfterThrowing.
