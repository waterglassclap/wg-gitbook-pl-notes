# Java Singleton

자바의 멀티쓰레드 환경에서 싱글톤을 구현하기 위해서 여러 방식이 제안되었는데, 그 방법들 몇 가지를 정리해본다.

## **1. Eager Initialization**

```java
public class Foo {
    private static Foo instance = new Foo(); 
    private Foo() {}
    
    public static Foo getInstance() {
        return instance;
    }
}

// 또는
public class Foo {
    private static Foo instance;
    private Foo() {}
    
    static {
        try {
            instance = new Foo();
        } catch (Exception e) {}
    }
    
    public static Foo getInstance() {
        return instance;
    }
}
```

이러한 방식에서는

1. ****constructor와 멤버 변수를 private 으로 선언해 외부에서 인스턴스를 선언하지 못하게 한다.

2. static 멤버 변수로서 선언 \( 또는 static block 내부에서 선언\)함으로서, classLoader에 의해 클래스가 최초 로딩될 때 생성될 수 있도록 함으로 thread safe를 보장한다.

```java
// Classloader.java 1.8

protected Class<?> loadClass(String name, boolean resolve)
    throws ClassNotFoundException
{
    synchronized (getClassLoadingLock(name)) {
        // First, check if the class has already been loaded
        Class<?> c = findLoadedClass(name);
        if (c == null) {
            long t0 = System.nanoTime();
        }
            
        ...
            
    protected Object getClassLoadingLock(String className) {
        Object lock = this;
        if (parallelLockMap != null) {
            Object newLock = new Object();
            lock = parallelLockMap.putIfAbsent(className, newLock);
        
        if (lock == null) {
            lock = newLock;
        }
    }
    return lock;
}
```

위와 같이 자바 클래스로더가 클래스 로딩시에 해당 클래스 이름으로 락을 걸고, static field는 로딩 시에 초기화되기 때문에 thread safety가 보장된다.

위 방식의 단점은 로드되는 시점에 인스턴스를 생성하기 때문에, 싱글톤으로 선언된 클래스가 많아질수록 사용하기도 전에 생성되는 인스턴스가 많아져 부담이 될 수 있다는 것이다.

**2. Lazy Initialization**

```java
public class Foo {
	
    private static Foo instance;
    private Foo () {}
	
    public static synchronized Foo getInstance() {
        if (instance == null) {
            instance = new Foo();
        }
        return instance;
    }
}
// 또는

public class Foo {
	
    private static Foo instance;
    private Foo () {}
	
    public static Foo getInstance() {
        synchronized(Foo.class) {
            if (instance == null) {
                instance = new Foo();
            }
            return instance;
        }
    }
}
```

위 방식에서는

1. constructor와 멤버 변수를 private 으로 선언해 외부에서 인스턴스를 선언하지 못하게 한다.

2. 클래스가 로드되는 시점이 아닌 getInstance\(\)를 호출한 시점에 인스턴스 생성이 이루어지기 때문에, Eager initialization 방식의 문제점이 해결 가능하다.

3. synchronized block을 사용하여 getInstance\(\)에서 instance 선언하는 부분의 thread safety를 보장한다.

```java
// 1.8 Object.java

public class Object {
    ...
    public final native void notify();
    public final native void notifyAll();
    public final void wait() throws InterruptedException {
        wait(0);
    }
}
```

Java의 모든 객체는 monitor lock 을 가지고 있다. static synchronized로 선언된 메서드\(또는 블록\)에 접근할 때는 해당 클래스 객체\(Foo.class\)에 대해 lock을 획득해야 하므로 thread safety가 보장된다.

위 방식의 단점은 매번 getInstance\(\)를 할 때마다 synchronized 로 실행되므로 효율이 떨어진다는 것이다. 인스턴스가 생성되는 시점에는 하나의 쓰레드만 접근이 가능해야 하지만, 그 이후 매 getInstance\(\) 요청 시에도 synchronized로 실행되는 것이 문제이다.

**3. Doubly Checked Locking**

```java
public class Foo {
	
    private volatile static Foo instance;
    private Foo () {}
	
    public static Foo getInstance() {
        if (instance == null) {
            synchronized(Singleton.class) {
                if (instance == null) {
                    instance == new Singleton();
                }
            }
        }
        return instance;
    }
}
```

Lazy initialization 방법에서 getInstance\(\)시에 매번 synchronized를 해야한다는 문제를 개선하고자 한 방법이다. 최초 instance 할당 시에는 synchronized block을 실행하되, 한번 instance가 할당된 이후로는 synchronized block을 타지 않도록 하려고 하였다.

또 volatile 키워드로 consistency 를 보장하고 코드가 재배치되는 걸 막는다 \(java 5 이상\).  
링크 참고 : [https://stackoverflow.com/questions/7855700/why-is-volatile-used-in-double-checked-locking](https://stackoverflow.com/questions/7855700/why-is-volatile-used-in-double-checked-locking)

**4. Enum**

```java
public enum Foo {
    INSTANCE;
}
```

enum은 열거형으로 초기화가 컴파일 타임에 결정되고, 단 하나의 인스턴스가 생기도록 보장된다. \(하지만 enum 내부에 선언되는 메서드는 구현 방식에 따라 thread safe 하지 않을 수도 있다\)  
방식은 깔끔하지만 enum은 컴파일 타임에 초기화가 진행되기 때문에 특정 상황에서는 활용하기 어려울 수 있다.

**5. LazyHolder**

```java
public class Foo {
    private Foo() {}
    private static class FooHolder {
        private static final Foo INSTANCE = new Foo();
    }
    public static Foo getInstance() {
        return FooHolder.INSTANCE;
    }
}
```

1. constructor와 멤버 변수를 private 으로 선언해 외부에서 인스턴스를 선언하지 못하게 한다.

2. Foo에는 FooHolder의 인스턴스가 없기 때문에, Foo 클래스의 로드 시점에는 FooHolder 가 초기화되지 않는다 . getInstance\(\) 로 요청을 하면 그때 FooHolder를 로드하는데, 1.2 의 이유와 마찬가지로 thread safety를 보장한다.

정리하면, FooHolder를 사용함으로서 1번 방식의 문제를 해결하고 thread safety를 보장하는 방법이다.

**References**

[http://happinessoncode.com/2017/10/04/java-intrinsic-lock/](http://happinessoncode.com/2017/10/04/java-intrinsic-lock/)  
[https://www.jpstory.net/2015/03/02/mutex-semaphore-monitor/](https://www.jpstory.net/2015/03/02/mutex-semaphore-monitor/)  
[https://kiwi99.tistory.com/19](https://kiwi99.tistory.com/19)  
[https://blog.seotory.com/post/2016/03/java-singleton-pattern](https://blog.seotory.com/post/2016/03/java-singleton-pattern)  
[https://medium.com/@joongwon/multi-thread-%ED%99%98%EA%B2%BD%EC%97%90%EC%84%9C%EC%9D%98-%EC%98%AC%EB%B0%94%EB%A5%B8-singleton-578d9511fd42](https://medium.com/@joongwon/multi-thread-%ED%99%98%EA%B2%BD%EC%97%90%EC%84%9C%EC%9D%98-%EC%98%AC%EB%B0%94%EB%A5%B8-singleton-578d9511fd42)  
[https://limkydev.tistory.com/67](https://limkydev.tistory.com/67)  
[https://homoefficio.github.io/2018/10/13/Java-%ED%81%B4%EB%9E%98%EC%8A%A4%EB%A1%9C%EB%8D%94-%ED%9B%91%EC%96%B4%EB%B3%B4%EA%B8%B0/](https://homoefficio.github.io/2018/10/13/Java-%ED%81%B4%EB%9E%98%EC%8A%A4%EB%A1%9C%EB%8D%94-%ED%9B%91%EC%96%B4%EB%B3%B4%EA%B8%B0/)  
[https://m.blog.naver.com/jjoommnn/130036635345](https://m.blog.naver.com/jjoommnn/130036635345)  
[https://m.blog.naver.com/jjoommnn/130036640859](https://m.blog.naver.com/jjoommnn/130036640859)  
[https://stackoverflow.com/questions/2531873/how-thread-safe-is-enum-in-java](https://stackoverflow.com/questions/2531873/how-thread-safe-is-enum-in-java)

