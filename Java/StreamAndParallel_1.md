
# 1. 스트림 소개

## 스트림(Stream) 이란?

스트림(Stream)은 Java 8 버전 이후부터 추가되었으며, Collection과 배열의 저장요소를 하나씩 참조해서 람다식으로 처리할 수 있도록 도와주는 반복자

## Stream vs Iterator

Java 7 이전에는 Collection의 저장 요소를 참조하기 위해서 Iterator를 사용하였다. 

Java 8 이후에는 Stream을 사용해서 저장 요소를 하나씩 참조가 가능하며, forEach() 메소드와 같은 Consumer 함수 인터페이스를 이용해서 람다식으로 사용이 가능하다.

```java
import java.util.Arrays;
import java.util.Iterator;
import java.util.List;
import java.util.stream.Stream;

public class IteratorAndStreamExample {

    public static void main(String[] args) {

        List<String> list = Arrays.asList("사과", "바나나", "포도");

        // Java 7 이전, Iterator 사용
        Iterator<String> iterator = list.iterator();
        while (iterator.hasNext()){
            String fruit = iterator.next();
            System.out.println("fruit-iterator = " + fruit);
        }

        // Java8 이후, Stream 사용
        Stream<String> stream = list.stream();
        stream.forEach(fruit -> System.out.println("fruit-stream = " + fruit));
    }
}
```

## 스트림의 특징

### 람다식으로 요소 처리 코드를 제공

Stream이 제공하는 대부분의 메소드들은 함수적 인터페이스를 매개 타입으로 가진다. 따라서 람다식 또는 메소드 참조를 이용해서 컬렉션의 요처들을 매개값으로 전달이 가능하다.

```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Stream;

public class LamdaExpressionExample {

    public static void main(String[] args) {
        List<Person> list = Arrays.asList(
                new Person("홍길동", 20),
                new Person("JHJ", 20));

        Stream<Person> stream = list.stream();
        stream.forEach(p -> {
            System.out.println("이름 :  " + p.getName() + ", 나이 : " + p.getAge());
        });
    }
}

public class Person{
    String name;
    int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }
}
```

### 내부 반복자를 사용하므로 병렬처리가 쉽다.

- 외부 반복자 : 개발자가 코드로 직접 컬렉션의 요소를 가져오는 패턴 (index를 활용한 for문, iterator, while문 등)
- 내부 반복자 : 컬렉션 내부에서 요소들을 반복시키고 개발자는 요소당 처리해야할 코드만 제공하는 패턴

![](https://images.velog.io/images/dev_jhjhj/post/f186379d-a597-41e9-945b-7d71b96d5930/%E1%84%82%E1%85%A2%E1%84%87%E1%85%AE%20%E1%84%87%E1%85%A1%E1%86%AB%E1%84%87%E1%85%A9%E1%86%A8%E1%84%8C%E1%85%A1.png)

- 내부 반복자를 사용하면 컬렉션 내부에서의 요소를 어떻게 반복 시킬지는 컬렉션에 맡겨 둔다. 개발자는 요소 처리에만 집중할 수 있다.
- 멀티코어 CPU를 최대한 활용하기 위해 요소들을 분배시켜 병렬작업을 도와주기 때문에 하나씩 처리하는 순차적인 외부 반복자보다는 내부 반복자를 사용하는 것이 좀 더 효율적이다.
- 병렬처리란 ? 
한가지 작업을 서브 작업으로 나누고, 서브 작업들을 분리된 스레드에서 병렬적으로 처리하는 것
런타임시 하나의 작업을 여러개의 작업으로 나눠서 처리 후, 결과를 자동으로 결합

```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Stream;

public class ParallelExample {

    public static void main(String[] args) {
        List<String> list = Arrays.asList("apple", "banana", "coconut", "grape");

        // 순차처리
        Stream<String> stream = list.stream();
        stream.forEach(ParallelExample::print);

        // 병렬처리 : 병렬 스트림으로 스트림 선언
        Stream<String> parallelStream = list.parallelStream();
        parallelStream.forEach(ParallelExample::print);
    }

    private static void print(String s) {
        System.out.println(s + " : " + Thread.currentThread().getName());
    }
}

// 결과
apple : main
banana : main
coconut : main
grape : main
coconut : main
grape : main
banana : ForkJoinPool.commonPool-worker-5
apple : ForkJoinPool.commonPool-worker-19
```

### 스트림은 중간처리와 최종 처리를 할 수 있다.

컬렉션 요소에 대해서 중간처리 (매핑, 필터링, 정렬)를 한 후, 최종 처리 (반복, 카운팅, 평균, 총합 등)의 집계처리가 가능하다.

```java
import java.util.Arrays;
import java.util.List;

public class MapAndReduceExample {
    public static void main(String[] args) {
        List<Person> personList = Arrays.asList(new Person("홍길동", 20),
                new Person("김OO", 21),
                new Person("JHJ", 21));
        
        double ageAvg = personList.stream()
                .mapToInt(Person::getAge)
                .average()
                .getAsDouble();

        System.out.println("평균 나이 = " + ageAvg);
    }
}
```

# 2. 스트림의 종류

Java 8 부터 [java.util.stream](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html) 패키지에 스트림(Stream) API 들이 포함되어 있다.

부모 인터페이스 : [BaseStream](https://docs.oracle.com/javase/8/docs/api/java/util/stream/BaseStream.html)

자식 인터페이스 : [Stream](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html), [IntStream](https://docs.oracle.com/javase/8/docs/api/java/util/stream/IntStream.html), [LongStream](https://docs.oracle.com/javase/8/docs/api/java/util/stream/LongStream.html), [DoubleStream](https://docs.oracle.com/javase/8/docs/api/java/util/stream/DoubleStream.html)

# 3. 스트림 파이프 라인

## 리덕션(Reduction)이란?

대량의 데이터를 가공해서 축소하는 것 (데이터의 합계, 평균값, 카운팅, 최대값, 최소값 등)

**하지만, 컬렉션의 요소를 바로 리덕션화해서 집계할 수 없을 경우, 집계하기 좋도록 필터링, 매핍, 정렬, 그룹피이 등 중간처리가 필요**

## 중간처리와 최종 처리

스트림은 데이터의 필터링, 매핑, 정렬, 그룹핑 등의 중간처리와 합계, 평균, 카운팅, 최대값, 최소값 등의 최종 집계 처리를 파이프라인으로 해결한다.

파이프라인은 여러개의 스트림이 연결되어 있는 구조를 말하는 데, 최종 처리를 제외하고 모두 중간처리 스트림이다.

![](https://images.velog.io/images/dev_jhjhj/post/59e591d1-5e30-4bf9-a7f8-da5fe98aaaa2/%E1%84%8C%E1%85%AE%E1%86%BC%E1%84%80%E1%85%A1%E1%86%AB%E1%84%8E%E1%85%A5%E1%84%85%E1%85%B5%E1%84%8B%E1%85%AA%20%E1%84%8E%E1%85%AC%E1%84%8C%E1%85%A9%E1%86%BC%E1%84%8E%E1%85%A5%E1%84%85%E1%85%B5.png)

중간 스트림이 생성될 때, 바로 중간처리를 진행하는 것이 아니라, `최종처리가 시작되기 전까지 중간처리는 Lazy 된다. 최종 처리가 시작되면 그때 컬렉션 요소들이 중간 스트림에서 처리 → 최종 처리`

```java
import java.util.Arrays;
import java.util.List;
import java.util.OptionalDouble;
import java.util.stream.IntStream;
import java.util.stream.Stream;

public class StreamPipelineExample {

    public static void main(String[] args) {
        List<Person> list = Arrays.asList(new Person("홍길동", 20, Person.MALE),
                new Person("김미미", 20, Person.FEMALE),
                new Person("김호호", 25, Person.MALE));

//    중간처리
//    Stream<Person> maleFemaleStream = list.stream();
//    Stream<Person> maleStream = maleFemaleStream.filter(m-> m.getGender() == Person.MALE);
//    IntStream ageStream = maleStream.mapToInt(Person::getAge);
//    최종 처리
//    OptionalDouble optionalDouble = ageStream.average();
//    double ageAge = optionalDouble.getAsDouble();

        double ageAge = list.stream()
                .filter(m -> m.getGender() == Person.MALE)
                .mapToInt(Person::getAge)
                .average()   // 최종처리
                .getAsDouble();

        System.out.println("ageAge = " + ageAge);

    }
}
```

# 4. 필터링 (distinct(), filter())

필터링은 중간 처리 기능으로 요소를 걸러내는 역할

모든 스트림이 공통으로 가지고 있다.

![](https://images.velog.io/images/dev_jhjhj/post/0c1366ef-b906-4622-9b62-20c3d9c59468/%E1%84%91%E1%85%B5%E1%86%AF%E1%84%90%E1%85%A5%E1%84%85%E1%85%B5%E1%86%BC.png)

### distinct()

Stream의 경우, Object.equals()가 true이면 동일한 객체로 판단해서 중복 제거

IntStream, LongStream, DoubleStream은 동일값이면 중복 제거

### filter()

매개값으로 주어진 Predicate가 true를 리턴하는 요소만 필터링


```java
import java.util.Arrays;
import java.util.List;

public class FilteringExample {

    public static void main(String[] args) {
        List<String> fruits = Arrays.asList("사과", "바나나", "포도", "귤", "사과", "포도", "포도송이", "포도알");

        // 중복제거
        fruits.stream()
                .distinct()
                .forEach(f-> System.out.println(f));

        // 필터링
        fruits.stream()
                .filter(f-> f.startsWith("포도"))
                .forEach(f-> System.out.println(f));

        // 중복 제거 후, 필터링
        fruits.stream()
                .distinct()
                .filter(f-> f.startsWith("포도"))
                .forEach(f-> System.out.println(f));
    }
}
```


# 5. 매핑 (flatMapXXX(), mapXXX(), asXXXStream(), boxed())

mapping은 중간 처리 기능으로 스트림의 요소를 다른 요소로 대체하는 것을 말한다.

## flatMapXXX()

flatMap은 스트림의 각 값을 다른 스트림으로 만든 다음에 모든 스트림을 하나의 스트림으로 연결하여 새로운 스트림을 리턴한다.

![](https://images.velog.io/images/dev_jhjhj/post/0711fb85-97ea-434b-8264-2e2bae83baa9/flatMap.png)

### flatMap 메소드 종류

![](https://images.velog.io/images/dev_jhjhj/post/d41a368a-8353-405b-9721-27cf3f7a6df6/flatMap_method.png)

```java
import java.util.Arrays;
import java.util.List;

public class FlatMapExample {

    public static void main(String[] args) {
        List<String> list = Arrays.asList("hello world", "good-bye world");
        list.stream()
                .flatMap(data -> Arrays.stream(data.split(" ")))
                .forEach(word -> System.out.println(word));

        List<String> list2 = Arrays.asList("10, 20, 30","40, 50, 60");
        list2.stream()
                .flatMapToInt(data -> {
                    String[] strArr = data.split(",");
                    int[] intArr = new int[strArr.length];

                    for (int i = 0; i < strArr.length; i++) {
                        intArr[i] = Integer.parseInt(strArr[i].trim());
                    }
                    return Arrays.stream(intArr);
                })
                .forEach(number -> System.out.println("number = " + number));

    }
}
```

## mapXXX() 메소드

map 메소드는 요소를 대체하는 요소로 구성된 새로운 스트림을 반환한다.

![](https://images.velog.io/images/dev_jhjhj/post/accccac2-d58a-4a80-891e-afebc96619e0/map.png)
### map의 메소드 종류

![](https://images.velog.io/images/dev_jhjhj/post/a1faba62-e702-45bc-9a6a-54a42bd01418/map_method.png)

```java
import java.util.Arrays;
import java.util.List;

public class MapExample {
    public static void main(String[] args) {
        List<Person> personList = Arrays.asList(
                new Person("홍길동", 20, Person.MALE),
                new Person("김미미", 25, Person.FEMALE),
                new Person("JHJ", 23, Person.FEMALE)
        );

        personList.stream()
                .mapToInt(Person::getAge)
                .forEach(age -> System.out.println("age = " + age));
    }
}
```

## asDoubleStream(), asLongStream(), boxed() 메소드

asDoubleStream() 메소드는 IntStream의 int 요소 또는 LongStream의 long 요소를 double 타입으로 반환해서 DoubleStream을 생성한다.

asLongStream() 메소드는 IntStream의 int 요소를 long 타입으로 변환해서 LongStream을 생성한다.

boxed() 메소드는 int, long, double 요소를 Integer, Long, Double 요소로 박싱해서 Stream을 생성한다.

```java
import java.util.Arrays;
import java.util.stream.IntStream;

public class AsDoubleBoxedExample {
    public static void main(String[] args) {

        int[] intArr = {1, 2, 3, 4, 5};

        IntStream intStream = Arrays.stream(intArr);
        intStream
                .asDoubleStream()
                .forEach(d -> System.out.println(d));

        intStream = Arrays.stream(intArr);
        intStream
                .boxed()
                .forEach(obj -> System.out.println(obj.intValue()));
    }
}
```

# 6. 정렬 (sorted)

중간단계에서 스트림 요소를 정렬하여 최종 처리 순서를 변경할 수 있다. 

### 요소 정렬 메소드

![](https://images.velog.io/images/dev_jhjhj/post/ee11ee62-2d97-48f9-b43e-2c5963f8f0f8/%E1%84%8C%E1%85%A5%E1%86%BC%E1%84%85%E1%85%A7%E1%86%AF.png)

### 객체 요소를 정렬할 때

객체 요소일 경우, 클래스에 `Comparable` 를 구현하지 않으면, `sorted()` 메소드 호출했을 때 `ClassCastException` 발생한다. 그렇기 때문에 Comparable 인터페이스를 상속받으면, `Comparable`를 구현한 요소에서만 `sorted()` 메소드를 사용해야 한다. 

```java
public class Person implements Comparable<Person>{
    public static int MALE = 0;
    public static int FEMALE = 1;

    String name;
    int age;
    int gender;

    public Person(String name, int age, int gender) {
        this.name = name;
        this.age = age;
        this.gender = gender;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public int getGender() {
        return gender;
    }

    @Override
    public int compareTo(Person o) {
        // age < o.age 음수 리턴
        // age == o.age 0 리턴
        // age > o.age 양수 리턴
        return Integer.compare(age, o.age); 
    }
}
```

### Comparable을 구현한 객체의 기본 비교 (Comparable) 와 반대 비교

```java
// 기본비교 ex) 오름차순
sorted();
sorted((a, b)-> a.compareTo(b));
sorted(Comparator.naturalOrder());

// 반대비교 ex) 내림차순
sorted((a, b)-> b.compareTo(a));
sorted(Comparator.reverseOrder());
```

만약 객체 요소가 Comparable를 구현하지 않았다면, Comparator를 매개값으로 갖는 sorted() 메소드를 사요하면 된다.

```java
// a가 작으면 음수, 같으면 0, a가 크면 양수
sorted((a, b) -> {....})
```

### 정렬 예제

```java
import java.util.Arrays;
import java.util.Comparator;
import java.util.List;
import java.util.stream.IntStream;

public class SortingExample {
    public static void main(String[] args) {

        IntStream intStream = Arrays.stream(new int[] {5, 2, 1, 3, 4, 6});

        // 숫자 오름차순으로 정렬
        intStream
                .sorted()
                .forEach(n -> System.out.println(n));

        // 객체 요소일 경우
        List<Person> personList = Arrays.asList(
                new Person("홍길동", 20, Person.MALE),
                new Person("김미미", 25, Person.FEMALE),
                new Person("JHJ", 23, Person.FEMALE)
        );

        // 나이 내림차순으로 정렬
        personList
                .stream()
                .sorted(Comparator.reverseOrder())
                .forEach(p -> System.out.println(p.getAge()));

        // 나이 오름차순
        personList
                .stream()
                .sorted((a, b) -> a.compareTo(b))
                .forEach(p -> System.out.println(p.getAge()));
    }
}
```


출처 : [이것이 자바다 (저자 : 신용권, 출판사 : 한빛미디어)](https://www.google.com/search?q=%EC%9D%B4%EA%B2%83%EC%9D%B4+%EC%9E%90%EB%B0%94%EB%8B%A4&oq=%EC%9D%B4%EA%B2%83%EC%9D%B4+%EC%9E%90%EB%B0%94%EB%8B%A4&aqs=chrome..69i57j0i20i263i512j0i512l3j69i60j69i61l2.2118j0j7&sourceid=chrome&ie=UTF-8)
