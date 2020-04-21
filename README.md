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
<bean id="id" class="com.***.Myclass">
```

**Application.java**
```java
public static void main(String[] args) {
  ClassPathXmlApplicationContext ctx = new ClassPathXmlApplicationContext("config.xml");
  MyClass myClass = ctx.getBean(Myclass.class);
}
```

# Annotations
