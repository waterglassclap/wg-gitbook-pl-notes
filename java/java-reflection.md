# Java Reflection

re·flec·tion : the throwing back by a body or surface of light, heat, or sound without absorbing it.  
Java Reflection : **to get Class information with Object.**

Java class file is compiled into bytecode, placed in static area. That is, if you know the class name you can access this area and get information of the class.

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

### **Performance Issues**

Btw, there are some reasons using reflection carefully, especially for the performance reasons:

1. The compiler can do no optimization whatsoever as it can have no real idea about what you are doing. This probably goes for the **JIT** as well
2. Everything being invoked/created has to be _discovered_ \(i.e. classes looked up by name, methods looked at for matches etc\)
3. Arguments need to be dressed up via boxing/unboxing, packing into arrays, Exceptions wrapped in InvocationTargetExceptions and re-thrown etc.
4. All the processing that [_Jon Skeet_ mentions here](https://stackoverflow.com/questions/1392351/java-reflection-why-is-it-so-bad/1392379#1392379) :
   1. Check that there's a parameterless constructor
   2. Check the accessibility of the parameterless constructor
   3. Check that the caller has access to use reflection at all
   4. Work out \(at execution time\) how much space needs to be allocated
   5. Call into the constructor code \(because it won't know beforehand that the constructor is empty\)

\(from : [https://stackoverflow.com/questions/1392351/java-reflection-why-is-it-so-slow](https://stackoverflow.com/questions/1392351/java-reflection-why-is-it-so-slow)\)

## References

{% embed url="https://gyrfalcon.tistory.com/entry/Java-Reflection" %}

{% embed url="https://loco-motive.tistory.com/31" %}

{% embed url="https://woovictory.github.io/2019/01/04/Java-What-is-Reflection/" %}

{% embed url="https://stackoverflow.com/questions/1392351/java-reflection-why-is-it-so-slow" %}

