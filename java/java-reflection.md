# Java Reflection

re·flec·tion : the throwing back by a body or surface of light, heat, or sound without absorbing it.  
Java Reflection : **to get Class information with Object**

### Example

```java
Class c = Data.class;

Method[] m = c.getMethods();                     
Field[] f = c.getFields();
Constructor[] cs = c.getConstructors();
Class[] inter = c.getInterfaces();
Class superClass = c.getSuperclass();
```

### Usages

#### **1. Invoke method with method name**

```java
Class class1 = Class.forName("name");
Method[] methods = class1.getMethods();
...
methods[0].invoke(methodObj, args);
```

#### **2. Create new object**

```java
Class class1 = Class.forName("name");
Constructor[] constructors = class1.getConstructors();
...
Object newObj = constructors[0].newInstance(arglist);
```

#### 3. Deserialization in ObjectInputStream.readObject\(\)

**4. Apache Commons BeanUtils library**

**5. Spring Dependency Injection**

**Annotation is for adding metadata in code**.With this, programmers can write some metadata at some point\(**Type, Method, Field,...**\) with certain retention \(**Source, Class, Runtime**\).  
**spring writes metadata of class on xml file and read it, and use reflection to utilize the class.**



## References

{% embed url="https://gyrfalcon.tistory.com/entry/Java-Reflection" %}

{% embed url="https://loco-motive.tistory.com/31" %}

\*\*\*\*

