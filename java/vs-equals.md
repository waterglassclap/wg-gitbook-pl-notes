# == vs equals

자바에서 == 는

1. 비교 대상에 하나라도 primitive type 이 있으면 값으로 비교한다
2. 비교 대상에 primitive type이 없으면 주소 값으로 비교한다

따라서 아래와 같은 결과가 나온다

```java
int a = 6;
int b = 6;

Integer c = new Integer(6);
Integer d = new Integer(6);

System.out.println(a == b); // true
System.out.println(c == d); // false
```

반면에 equals는 java.lang.Object.equals로, 케이스에 따라 override 해서 사용할 수 있다.  
예를 들어 lombok 에서 class @EqualsAndHashCode annotation 을 달면

```java
import lombok.EqualsAndHashCode;

@EqualsAndHashCode
public class EqualsAndHashCodeExample {
  private transient int transientVar = 10;
  private String name;
  private double score;
  private String[] tags;
  @EqualsAndHashCode.Exclude private int id;
  
  public String getName() {
    return this.name;
  }
}
```

위 코드가 아래와 같이 변환된다

```java
import java.util.Arrays;

public class EqualsAndHashCodeExample {
  private transient int transientVar = 10;
  private String name;
  private double score;
  private String[] tags;
  private int id;
  
  public String getName() {
    return this.name;
  }
  
  @Override public boolean equals(Object o) {
    if (o == this) return true;
    if (!(o instanceof EqualsAndHashCodeExample)) return false;
    EqualsAndHashCodeExample other = (EqualsAndHashCodeExample) o;
    if (!other.canEqual((Object)this)) return false;
    if (this.getName() == null ? other.getName() != null : !this.getName().equals(other.getName())) return false;
    if (Double.compare(this.score, other.score) != 0) return false;
    if (!Arrays.deepEquals(this.tags, other.tags)) return false;
    return true;
  }
  
  @Override public int hashCode() {
    final int PRIME = 59;
    int result = 1;
    final long temp1 = Double.doubleToLongBits(this.score);
    result = (result*PRIME) + (this.name == null ? 43 : this.name.hashCode());
    result = (result*PRIME) + (int)(temp1 ^ (temp1 >>> 32));
    result = (result*PRIME) + Arrays.deepHashCode(this.tags);
    return result;
  }
  
  protected boolean canEqual(Object other) {
    return other instanceof EqualsAndHashCodeExample;
  }
} 
```

## References

{% embed url="https://marobiana.tistory.com/130" %}

{% embed url="https://stackoverflow.com/questions/7520432/what-is-the-difference-between-and-equals-in-java" %}

{% embed url="https://projectlombok.org/features/EqualsAndHashCode" %}



