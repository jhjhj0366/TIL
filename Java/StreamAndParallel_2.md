# 7. Looping 메소드 (peek(), forEach())

## peek()

중간처리 looping 메소드, 중간처리 단계에서 전체 요소를 루핑을 돌면서 추가적인 작업을 위해 사용

마지막에 최종 처리 메소드가 실행되지 않으면 동작하지 않는다.

## forEach()

최종처리 looping 메소드

파이프라인 마지막에 루핑하면서 요소를 하나씩 처리

forEach가 최종처리 메소드이기 때문에 이후에 또다른 최종처리 메소드를 호출하면 안된다.

```java
import java.util.Arrays;

public class LoopingExample {
    public static void main(String[] args) {

        int[] intArr = {1, 2, 3,4,5,6};

        System.out.println("peek은 중간처리 메소드 - peek을 마지막에 호출한 경우, 동작하지 않음");
        Arrays.stream(intArr)
                .filter(a -> a%2 ==0)
                .peek(n-> System.out.println(n));

        // 최종 처리 메소드 sum()을 마지막에 호출하면 peek 동작함
        int total = Arrays.stream(intArr)
                .filter(a -> a%2 ==0)
                .peek(n-> System.out.println(n))
                .sum();
        System.out.println("total = " + total);

        System.out.println("forEach()는 최종처리 루핑 메소드 - 마지막에 호출한 경우, 동작함");
        Arrays.stream(intArr)
                .filter(a -> a%2 ==0)
                .forEach(n-> System.out.println(n));
    }
}
```

# 8. Matching (allMatch(), anyMatch(), noneMatch())

최종 처리 단계에서 요소들이 특정 조건에 맞는 지 확인할 수 있도록 matching 메소드를 지원한다.

## allMatch()

모든 요소들이 매개값으로 주어진 predicate 값이 조건을 만족하는 지 조사한다.

## anyMatch()

최소한 한개 이상의 요소가 매개값으로 주어진 predicate 값이 조건을 만족하는 지 조사한다.

## noneMatch()

모든 요소가 매개값으로 주어진 predicate 값이 조건을 만족하지 않는 지 조사한다.

![](https://images.velog.io/images/dev_jhjhj/post/8dd58f13-dab5-4f4a-8833-57b5e18a738a/allMatch.png)

```java
import java.util.Arrays;

public class MatchExample {
    public static void main(String[] args) {
        int[] intArray = {1, 2, 3, 4, 5};

        boolean result = Arrays.stream(intArray)
                .allMatch(a -> a % 2 == 0);
        System.out.println("모두 2의 배수인가 ? = " + result);

        result = Arrays.stream(intArray)
                .anyMatch(a -> a % 3 == 0);
        System.out.println("3의 배수인 요소가 하나라도 있나 ? = " + result);

        result = Arrays.stream(intArray)
                .noneMatch(a -> a % 7 == 0);
        System.out.println("모든 요소가 7의 배수가 아닌가? = " + result);

    }
}
```

# 9. 기본 집계 (sum(), count(), average(), max(), min())

집계는 최종 처리 기능으로 요소들을 처리한다.

카운팅, 합계, 평균값, 최댓값, 최솟값 등 하나의 값을 산출한다.

집계는 대량의 데이터를 가공해서 축소하는 리덕션(Reduction) 이라고 볼 수 있다.

## 스트림이 제공하는 기본 집계 함수

![](https://images.velog.io/images/dev_jhjhj/post/f2b24c3f-d65a-456e-93bd-a4012ab7372b/%E1%84%89%E1%85%B3%E1%84%90%E1%85%B3%E1%84%85%E1%85%B5%E1%86%B7%E1%84%8B%E1%85%B5%E1%84%8C%E1%85%A6%E1%84%80%E1%85%A9%E1%86%BC%E1%84%92%E1%85%A1%E1%84%82%E1%85%B3%E1%86%AB%20%E1%84%80%E1%85%B5%E1%84%87%E1%85%A9%E1%86%AB%E1%84%8C%E1%85%B5%E1%86%B8%E1%84%80%E1%85%A8.png)
```java
import java.util.Arrays;

public class AggregateExample {

    public static void main(String[] args) {
        int[] intArr = {1, 2, 3, 4, 5, 6, 7};

        long count = Arrays.stream(intArr)
                .filter(a -> a % 2 == 0)
                .count();
        System.out.println("2의 배수 개수 : " + count);

        long sum = Arrays.stream(intArr)
                .filter(n -> n % 2 == 0)
                .sum();
        System.out.println("2의 배수인 요소들의 합 : " + sum);

        double avg = Arrays.stream(intArr)
                .filter(n -> n % 2 == 0)
                .average()
                .getAsDouble();
        System.out.println("2의 배수인 요소들의 평균, double 타입 : " + avg);

        int max = Arrays.stream(intArr)
                .filter(n -> n % 2 == 0)
                .max()
                .getAsInt();
        System.out.println("2의 배수인 요소들의 최댓값, double 타입 : " + max);

        int first = Arrays.stream(intArr)
                .filter(n -> n % 3 == 0)
                .findFirst()
                .getAsInt();
        System.out.println("3의 배수인 요소들 중에서 가장 첫 요소 : " + first);
    }
}
```

## Optional 클래스

Optional 클래스는 단순히 집계 값만 저장하는 것이 아니라, 집계 값이 존재하지 않을 경우(null 일 경우), 디폴트 값을 설정할 수 있고 집계 값을 처리하는 Cunsumer도 등록할 수 있다.

![](https://images.velog.io/images/dev_jhjhj/post/e25be9b9-e232-4bf1-b8d6-28f53ed118d1/Optional.png)

```
importjava.util.ArrayList;
importjava.util.List;
importjava.util.OptionalDouble;

public classOptionalExample {

public static voidmain(String[] args) {
        List<Integer> list =newArrayList<>();

//  java.util.NoSuchElementException발생한다.
        //요소 값이 null이기 떄문 !
//        double avg = list.stream()
//                .mapToInt(Integer::intValue)
//                .average()
//                .getAsDouble();

        // 1. isPresent()사용
OptionalDouble optionalAvg = list.stream()
                .mapToInt(Integer::intValue)
                .average();

if(optionalAvg.isPresent()){
            System.out.println("isPresent() 사용 : " + optionalAvg);
        }else{
            System.out.println(0.0);
        }

// 2. orElse
doubleavg = list.stream()
                .mapToInt(Integer::intValue)
                .average()
                .orElse(0.0);

        System.out.println("orElse() 사용 = " + avg);

// 3. ifPresent람다식
list.stream()
                .mapToInt(Integer::intValue)
                .average()
                .ifPresent(a -> System.out.println("ifPresent() 사용 : " + a));

    }
}
```

# 10. 커스텀 집계(reduce())

기본 집계 메소드 외에도 프로그램화해서 다양한 집계 결과물을 만들 수 있도록 reduce() 메소드를 제공한다.

![](https://images.velog.io/images/dev_jhjhj/post/2b4fc222-3f71-4119-a0e3-d07c8b569a4c/reduce().png)

각 인터페이스에는 매개타입으로 XXXOperator, 리턴 타입으로 OptionalXXX, int, longm double 을 가지는 reduce() 메소드가 오버로딩 되어 있다.

스트림 요소가 없을 경우, 디폴트 값으로 `identity` 가 리턴된다.

XXXOperator 매개값은 집계 처리를 위한 람다식을 대입한다.

```java
import java.util.Arrays;
import java.util.List;

public class ReductionExample {

    public static void main(String[] args) {
        List<Person> personList = Arrays.asList(new Person("홍길동", 20, Person.MALE)
                , new Person("김미미", 21, Person.FEMALE)
                , new Person("JHJ", 22, Person.FEMALE));

        int sum1 = personList.stream()
                .mapToInt(Person::getAge)
                .sum();

        int sum2 = personList.stream()
                .map(Person::getAge)
                .reduce((a, b) -> a + b)
                .get();

        int sum3 = personList.stream()
                .map(Person::getAge)
                .reduce(0, (a, b) -> a + b);

        System.out.println("sum1 = " + sum1);
        System.out.println("sum2 = " + sum2);
        System.out.println("sum3 = " + sum3);
    }
}
```


출처 : [이것이 자바다 (저자 : 신용권, 출판사 : 한빛미디어)](https://www.google.com/search?q=%EC%9D%B4%EA%B2%83%EC%9D%B4+%EC%9E%90%EB%B0%94%EB%8B%A4&oq=%EC%9D%B4%EA%B2%83%EC%9D%B4+%EC%9E%90%EB%B0%94%EB%8B%A4&aqs=chrome..69i57j0i20i263i512j0i512l3j69i60j69i61l2.2118j0j7&sourceid=chrome&ie=UTF-8)
