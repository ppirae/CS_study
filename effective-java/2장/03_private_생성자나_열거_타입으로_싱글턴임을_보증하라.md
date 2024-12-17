### 싱글턴이란 ?
[싱글턴 참고](https://refactoring.guru/ko/design-patterns/singleton)

클래스를 싱글턴으로 만들면 이를 사용하는 클라이언트를 테스트하기가 어려워질 수 있다.

## 싱글턴을 만드는 방식
### 1. public static final 필드 방식
```JAVA
public class Elvis {
	public static final Elvis INSTANCE = new Elvis();
	private Elvis() { ... }
}
```
* public이나 protected 생성자가 없으므로 초기화될때 만들어진 인스턴스가 전체 시스템에서 하나임을 보장한다.
* 예외는 065_리플렉션 API AccessibleObject.setAccessible 을 이용해 private 생성자 호출이 가능하다.
* 방어는 생성자를 수정하여 두 번째 객체가 생성되려 할때 예외를 throw 하면 된다.

##### 장점
1) 싱글턴이 명백하다.
2) 코드가 간결하다
---
### 2. 정적 팩터리 방식
```JAVA
public class Elvis {
	private static final Elvis INSTANCE = new Elvis();
	private Elvis() { ... }
	public static Yaho getInstance() { return INSTANCE; }
}
```
##### 장점
1. API를 바꾸지 않고도 싱글턴이 아니게 변경 가능하다.
2. 정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있다. (030)
3. 정적 팩터리의 메서드 참조를 공급자로 사용할 수 있다. (043, 044)   
      
위 장점들이 굳이 필요없다면 1번을 쓰자.

---
### 3. 열거 타입 방식
```JAVA
public enum Elvis {
	INSTANCE;
}
```
##### 장점
1. public 방식과 비슷하지만 더 간결하고 직렬화 가능하다.
2. 리플렉션 공격도 막는다.
3. 대부분 상황에서 원소가 하나뿐이 열거 타입이 싱글턴을 만드는 가장 좋은 방법이다.

단, 만들려는 싱글턴이 Enum 외의 클래스를 상속해야 한다면 이 방법은 사용할 수 없다.