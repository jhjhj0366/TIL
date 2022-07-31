### 자바는 두 가지 객체 소멸자를 제공한다.

- [finalizer](https://docs.oracle.com/javase/9/docs/api/java/lang/Object.html#finalize)
    - **finalizer는 예측할 수 없고 오동작, 낮은 성능, 이식성 문제의 원인이 된다.** finalizer는 나름의 쓰임새가 있긴하지만 기본적으로는 사용하지 말아야 한다.(자바 9에서는 finalizer를 deprecated API로 지정하고 cleaner를 대안으로 소개했다.)
- [cleaner](https://docs.oracle.com/javase/9/docs/api/java/lang/ref/Cleaner.html)
    - finalizer보다는 덜 위험하지만, 여전히 예측할 수 없고 느리고 일반적으로 불필요하다.
    굳이 사용하자면, 안전망의 역할로 사용 (아래 정리)
    
    <aside>
    👉  자바의 finalizer와 cleaner는 C++의 파괴자 (destructor)는 다른 개념, C++에서 특정객체의 자원을 회수하는 보편적인 방법, 자바에서는 접근할 수 없게 된 객체의 자원을 회수하는 역할을 가비지 컬렉터가 담당
    
    C++의 파괴자는 비메모리 자원을 회수, 자바에서는 try-with-resource 와 try-finally를 사용해서 비메모리 자원 회수해결 (아이템9)
    
    </aside>
    

### **finalizer와 cleaner는 즉시 수행된다는 보장이 없다.**

- finalizer와 cleaner 는 즉시 수행된다는 보장이 없고, 수행 속도는 가비지 컬렉터 알고리즘에 달렸기 때문에 즉시 수행된다고 보장할 수 없다.
- finalizer 스레드는 다른 애플리케이션 스레드보다 우선 순위가 낮아서 제때 실행될 기회를 얻지 못한다.
- Java Language Specification 에서 finalizer나 cleaner의 수행 시점 뿐만 아니라 수행 여부조차 보장하지 않는다. (즉, 접근할 수 없는 일부 객체에 딸린 종료 작업을 전혀 수행하지 못한 채 프로그램이 종료될 수 있다는 얘기)
- 프로그램 생애주기와 상관없이 **상태를 영구적으로 수정하는 작업에서는 절대 finalizer와 cleaner에 의존해서는 안된다.**

### **finalizer 동작 중에 예외가 발생하면 정리 작업이 처리 되지 않을 수도 있다.**

- finalizer 동작 중 발생한 예외는 무시되며 처리할 작업이 남았더라도 그 순간 종료된다. 경고조차 출력하지 않는다.
→ 잡지 못한 예외 때문에 해당 객체가 미처리된 상태로 남아있을 수 있다.
- 그나마 cleaner는 자신의 스레드를 통제하기 때문에 이런 이슈는 생기지 않는다.

### **finalizer 와 cleaner는 심각한 성능 문제가 있다.**

- AutoClosable 객체를 생성하고 가비지 컬렉터가 수거하기까지 12ns
- finalizer를 사용하면 550ns가 걸렸다. 즉, ****finalizer를 사용한 객체를 생성하고 파괴하기까지 50배나 느림
- finalizer가 가비지 컬렉터의 효율을 떨어뜨리기 때문이다. (cleaner도 클래스의 모든 인스턴스를 수거하는 형태로 사용하면 finalizer와 성능이 비슷하다.)

### f**inalizer 는 보안 문제가 있다.**

- finalizer를 사용한 클래스는 finalizer 공격에 노출되어 심각한 보안 문제를 일으킬 수 있다.
- 생성자나 직렬화 과정에서 예외가 발생하면 생성되다만 객체에서 악의적인 하위 클래스의 finalizer가 수행될 수 있게 된다.
- 이를 방어하려면 상위클래스를 final 클래스로 만들거나, finalizer()메소드를 오버라이딩한 다음, final을 붙여서 하위 클래스에서 재정의할 수 없게 막는다.

```java
public class Account {

    private String accountId;

    public Account(String accountId) {
        this.accountId = accountId;

        if (accountId.equals("푸틴")) {
            throw new IllegalArgumentException("푸틴은 계정을 막습니다.");
        }
    }

    public void transfer(BigDecimal amount, String to) {
        System.out.printf("transfer %f from %s to %s\n", amount, accountId, to);
    }

```

```java
public class BrokenAccount extends Account {

    public BrokenAccount(String accountId) {
        super(accountId);
    }

    @Override
    protected void finalize() throws Throwable {
        this.transfer(BigDecimal.valueOf(100), "jhj");
    }
}
```

```java
@Test
    void 푸틴_계정() throws InterruptedException {
        Account account = null;
        try {
            account = new BrokenAccount("푸틴");
        } catch (Exception exception) {
            System.out.println("과연???");
        }

        System.gc();
        Thread.sleep(3000L);
    }

// 결과
//과연???
//transfer 100.000000 from 푸틴 to jhj
```

### **객체 소멸자 finalizer, cleaner를 대체해줄 묘안**

- 파일이나 스레드 등을 종료하려면  `AutoCloseable`을 구현하는 것을 추천
- 클라이언트에서는 인스턴스를 다 쓰고 나면 `close` 메서드를 호출하는 것을 추천
👉 일반적으로 예외가 발생해도 제대로 종료되도록 `try-with-resources`를 사용 (아이템 9)

### **finalizer와 Cleaner 사용처**

1. 자원의 소유자가 close 메서드를 호출하지 않을 것을 대비하는 안전망 역할
    - finalize와 cleaner가 호출되리라는 보장은 없지만, 클라이언트가 하지 않은 자원회수를 늦게라도 해줄 일말의 가능성이 있음, 그치만 꼭 사용할 값어치가 있는지 심사숙고 필요
    - 자바 라이브러리 일부 클래스는 안정망 역할의 finalizer를 제공
    ( `FileInputStream`, `FileOutputStream`, `ThreadPoolExcutor`)
2. 네이티브 피어(Native Peer)와 연결된 객체에서 사용
    
    > 네이티브 피어 : 일반 자바 객체가 네이티브 메서드를 통해 기능을 위임한 네이티브 객체 (ex. Java Swing)
    > 
    - 네이티브 객체는 자바 객체가 아님, 가비지 컬렉터가 네이티브 객체까지 회수를 못함
    - finalizer와 cleaner가 처리할 수 있지만 성능 저하에 대해 고려 필요, 네이티브 피어가 `close` 메서드 사용 권장

### Cleaner 사용법

👉 cleaner를 사용할지 말지는 순전히 내부 구현 방식에 관한 문제

```java
/**
* Room 객체를 소멸하기 전에 반드시 청소 작업을 해야한다고 가정
**/

public class Room implements AutoCloseable {

		// Cleaner 생성
    private static final Cleaner cleaner = Cleaner.create();

    // 방의 상태. cleanable과 공유한다.
    private final State state;

    // cleanable 객체. 수거 대상이 되면 방을 청소한다.
    private final Cleaner.Cleanable cleanable;

    public Room(int numJunkPiles) {
        state = new State(numJunkPiles);
        cleanable = cleaner.register(this, state);
    }

    @Override public void close() {
        cleanable.clean();
    }

     // 청소가 필요한 자원. 절대 Room을 참조해서는 안 된다! (순환참조 발생)
		// 정적인 inner class로 구현되어야, 바깥 객체인 Room 참조 못함
    private static class State implements Runnable {
        int numJunkPiles; // 방 안의 쓰레기 수, 수거할 자원에 해당

        State(int numJunkPiles) {
            this.numJunkPiles = numJunkPiles;
        }

        // close 메서드나 cleaner가 호출한다.
        @Override public void run() {
            System.out.println("Cleaning room");
            numJunkPiles = 0;   // 값을 0으로 만드는 것을 청소 작업
        }
    }

}
```

**run 메서드가 호출되는 상황 (2가지)**

- Room의 close 메서드를 호출할 때 `close` 메서드에서 `Cleanable`의 `clean`을 호출하면 이 메서드 안에서 `run`을 호출한다.
- 가비지 컬렉터가 Room을 회수할 때까지 `close`를 호출하지 않는다면 `cleaner`가 State 의 `run` 메서드를 호출해줄 것이다.

<aside>
👉 **중첩 클래스**

- State를 인스턴스는 절대로 Room 인스턴스를 참조해서는 안된다. 
→ 순환참조가 생겨서 가비지 컬렉터가 Room 인스턴스를 회수할 기회가 오지 않는다.
- State를 꼭 중첩클래스로 사용할 경우, 정적 (static) 중첩 클래스로 구현된 이유
→ 중첩 클래스는 자동으로 바깥 객체의 참조를 갖게 되기 때문, 따라서 정적으로 선언하여 바깥 객체 Room을 참조하는 것을 막는다.

</aside>

**잘 짜여진 자동 청소**

```java
public class Adult {
    public static void main(String[] args) {
        try (Room myRoom = new Room(7)) {
            System.out.println("안녕~");
        }
    }
}

// 안녕~
// Cleaning room
```

- `AutoCloseable`을 상속받고 있기 때문에 `try-with-resource` 구문 사용 시, `close` 가 호출되어 객체 반납

**예측할 수 없는 cleaner**

```java
public class Teenager {

    public static void main(String[] args) {
        new Room(99);
        System.out.println("Peace out");

        // 다음 줄의 주석을 해제한 후 동작을 다시 확인해보자.
        // 단, 가비지 컬렉러를 강제로 호출하는 이런 방식에 의존해서는 절대 안 된다!
        // System.gc();
    }
}

// 결과
// "Peace out" 만 출력됨 -> 예측할 수 없음
```

- 위 코드는 clean을 한번도 출력하지 않음, 예측할 수 없는 상황 (Room의 cleaner는 단순지 자원 소멸을 위한 안전망으로 사용)

**cleaner에 명세서**

<aside>
👉 System.exit을 호출할 때의 cleaner 동작은 구현하기 나름이다. **청소가 이뤄질지는 보장하지 않는다.**

</aside>

**정리**

- finalizer, cleaner 사용은 피하라
- cleaner는 안전망 역할이나, 네이티브 자원 회수용으로만 사용하라
→ 불확실성과 성능 저하에 주의 필요



#### 출처
- 도서 : Effective Java - 저자, 조슈아 블로크
- 인프런 강의 : [이펙티브 자바 완벽 공략 1부 - 백기선](https://www.inflearn.com/course/%EC%9D%B4%ED%8E%99%ED%8B%B0%EB%B8%8C-%EC%9E%90%EB%B0%94-1)
