# Type erasure and Generics

Java generics 는 Type erasure 를 통해 구현된다.  
Type erasure 는 컴파일 타임에 Type 정보를 제거 \(또는 변환\) 하고, 런타임에는 타입을 강제하지 않는 방법이다.  
예를 들어

```java
public static <E> boolean containsElement(E [] elements, E element){
    for (E e : elements){
        if(e.equals(element)){
            return true;
        }
    }
    return false;
}
```

위 코드는 컴파일 시 아래와 같은 코드로 변환된다

```java
public static boolean containsElement(Object [] elements, Object element){
    for (Object e : elements){
        if(e.equals(element)){
            return true;
        }
    }
    return false;
}
```

Type erasure는 클래스, 메서드, 변수에 적용될 수 있다

### Class Type Erasure

```java
public class Stack<E> {
    private E[] stackContent;
 
    public Stack(int capacity) {
        this.stackContent = (E[]) new Object[capacity];
    }
 
    public void push(E data) {
        // ..
    }
 
    public E pop() {
        // ..
    }
}
```

위 코드는 컴파일시 아래와 같이 변환된다

```java
public class Stack {
    private Object[] stackContent;
 
    public Stack(int capacity) {
        this.stackContent = (Object[]) new Object[capacity];
    }
 
    public void push(Object data) {
        // ..
    }
 
    public Object pop() {
        // ..
    }
}
```

 만약에 Generic E가 bounded 라면

```java
public class BoundStack<E extends Comparable<E>> {
    private E[] stackContent;
 
    public BoundStack(int capacity) {
        this.stackContent = (E[]) new Object[capacity];
    }
 
    public void push(E data) {
        // ..
    }
 
    public E pop() {
        // ..
    }
}
```

위 코드는 컴파일시 아래와 같이 변환된다

```java
public class BoundStack {
    private Comparable [] stackContent;
 
    public BoundStack(int capacity) {
        this.stackContent = (Comparable[]) new Object[capacity];
    }
 
    public void push(Comparable data) {
        // ..
    }
 
    public Comparable pop() {
        // ..
    }
}
```

E가 first bound class 인 Comparable 로 변환된 것을 볼 수 있다

### Method Type Erasure

```java
public static <E extends Comparable<E>> void printArray(E[] array) {
    for (E element : array) {
        System.out.printf("%s ", element);
    }
}
```

컴파일 시

```java
public static void printArray(Comparable[] array) {
    for (Comparable element : array) {
        System.out.printf("%s ", element);
    }
}
```

위와 같이 변환된다.

## References

{% embed url="https://www.baeldung.com/java-type-erasure" %}

{% embed url="https://stackoverflow.com/questions/27606449/how-exactly-do-generics-work" %}







