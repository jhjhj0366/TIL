# 2.1 기본요소 : 함수와 변수

## 2.1.1 Hello, World !

```kotlin
fun main(args: Array<String>) {
    println("Hello, world!")
}
```

- 함수를 선언할 때 fun 키워드 사용
- 파라미터 이름 뒤에 그 파라미터의 타입을 쓴다.
- 함수를 최상위 수준에 정의 가능/ 자바와 달리, 꼭 클래스 안에 함수를 넣을 필요가 없다.
- 배열도 일반적인 클래스, 코틀린에는 자바와 달리 배열 처리를 위한 문법이 따로 없다.
- System.out.println대신에 println 이라고 쓴다.
- 끝 줄에 세미콜론 생략 가능하다.

## 2.1.2 함수

![](https://images.velog.io/images/dev_jhjhj/post/46a6a552-fdb1-4cce-9126-8d4d10a79d7e/%E1%84%8F%E1%85%A9%E1%84%90%E1%85%B3%E1%86%AF%E1%84%85%E1%85%B5%E1%86%AB_%E1%84%92%E1%85%A1%E1%86%B7%E1%84%89%E1%85%AE.png)

**✅ 문(statement)와 식(expression)의 구분**

- 문(statement)은 자신은 둘러싸고 잇는 가장 안쪽 블록의 최상위 요소로 아무런 값을 만들어 내지 않는다.
- 식(expression)은 값을 만들어 내며 다른 식의 하위 요소로 계산에 참여할 수 있다.

코틀린에서 if는 식이다. (문이 아니다.) 자바에서는 모든 제어 구조가 문(statement)이지만, 코틀린에서는 Loop를 제외하면 대부분 제어 구조가 식(expression)이다.

반면, 대입문은 자바에서는 식이었으나, 코틀린에서는 문(statement)이다.

### 식이 본문인 함수

```kotlin

// 블록이 본문인 함수
fun maxEx1(a: Int, b: Int) : Int {
    return if (a > b) a else b
}

// 식이 본문인 함수
fun maxEx2(a: Int, b:Int) : Int = if (a>b) a else b

// 반환 타입을 생략한 함수
fun maxEx3(a: Int, b:Int) = if (a>b) a else b

```

- 블록이 본문인 함수 : 본문이 중괄호로 둘러싸인 함수
- 식이 본문인 함수 : 등호와 식으로 이뤄진 함수

- 반환 타입을 생략할 수 있는 이유 (식이 본문인 함수의 반환 타입만 생략 가능)
    
    코틀린은 정적 타입 지정 언어 이므로 컴파일 시점에 모든 식의 타입을 지정해야한다. 하지만 식이 본문이니 함수의 경우, 굳이 반환 타입을 적지 않아도 컴파일러가 함수 본문 식을 분석해서 결과 타입을 함수 반환 타입으로 정해준다. → 타입 추론이 가능하다.
    

## 2.1.3 변수

```kotlin
// 타입 생략
val answer = 42
// 타입 명시
val answer: Int = 42
```

### 변경 가능한 변수와 변경 불가능한 변수

- val : immutable한 참조를 저장하는 변수
    
    val로 선언된 변수는 일단 초기화 하고 나면 재대입 불가능, java의 final과 같다.
    
- var : mutable 참조, 변수의 값이 바뀔 수 있다. 자바의 일반 변수에 해당

기본적으로 모든 변수는 val, 불변 변수로 선언하고 추후 필요에 따라 var 로 변경하는 것을 추천한다.

immutable한 참조와 immutable한 객체를 부수 효과가 없는 함수와 조합해 사용하면, 코드가 함수형 코드에 가까워진다.

- val 참조 자체는 불변이지만, 그 참조가 가리키는 객체의 내부 값은 변경될 수 있다.

```kotlin
val languages = arrayListOf("Java")   // 불변 참조를 선언
languages.add("Kotlin")   // 참조가 가리키는 객체 내부 변경
```

- var 키워드를 사용하면 변수 값 변경 가능, 변수 타입은 고정되어서 바뀌지 않는다.

컴파일러는 변수 선언 시점의 초기화 된 식으로부터 변수의 타입을 추론, 변수 재대입이 이뤄질 때, 이미 먼저 추론한 변수 타입으로 컴파일한다.

```kotlin
// Error : type mismach 발생 - 컴파일 오류
var answer = 123;
answer = "hello world"
```

### 2.1.4 더 쉽게 문자열 형식 지정 : 문자열 템플릿

```kotlin
// 문자열 템플릿 사용
fun main(args: Array<String>) {
    val name = if(args.size > 0) args[0] else "Kotlin"
    println("Hello, $name!")

    if(args.size > 0){
        println("Bye, ${args[0]}")   // args 배열의 원소를 넣기 위해${} 구문 사용
    }

    println("\$")  // 달려 표시 ($)를 쓸 경우에는 이스케이프(\) 사용

}
```

- 코틀린에서도 변수를 문자열 안에 사용할 수 있다. → 문자열 리터럴이 필요한 곳에 $변수명
- 한글과 문자 템플릿 사용할 경우 주의 점
    - 코틀린은 자바와 마찬가지로 변수 이름에 한글이 들어갈 수 있다. (모든 유니코드 문자는 변수명 사용 가능)
    - 문자열 템플릿 안에 `$name님, 반가워요` 처럼 “$변수명”과 한글을 붙여서 사용할 경우, 컴파일 에러가 발생한다. 코틀린 컴파일러가 영문자와 한글을 한꺼번에 식별자로 사용해서 그렇다.
    
    → `${name}님, 반가워요`  변수 명을 {}로 감싸라, 평소에소 {}로 감싸서 사용하는 것이 가독성에 좋다.
    

</br></br>
# 2.2 클래스와 프로퍼티

```java
// Java Person 클래스
public class Person {
    private final String name;
    
    public String getName() {
        return name;
    }

    public Person(String name) {
        this.name = name;
    }
}
```

Java의 경우 필드가 늘어날 수록 생성자의 파라미터 개수도 늘어나게 된다. 즉 반복적으로 늘어나는 코드가 많아진다.

```kotlin
// 코틀린으로 변환한 Person 클래스
class Person (val name : String)
```

- 코틀린의 접근제어자는 기본이 public 이다. 따라서 생략 가능하다.

### 2.2.1 프로퍼티

자바에서는 필드와 접근자를 한데 묵어 Property라고 부른다. 코틀린 프로퍼티는 자바의 필드와 접근자 메소드를 완전히 대신한다.

```kotlin
class Person(
    val name: String,   // private 변수, 읽기 전용, 단순한 공개 getter만 만들어낸다.
    var isMarried: Boolean  // private 변수, 공개 getter, 공개 setter 만들어낸다.
)

val person = Person("Bob", true)  // new 키워드 사용 안하고 생성자 호출
// 프로퍼티 이름을 직접 사용해도 코틀린이 자동으로 getter 호출
println(person.name)
println(person.isMarried)
```

### 2.2.2 커스텀 접근자

```kotlin
class Rectangle(val height: Int, val width: Int) {
    val isSquare: Boolean
        get() {
            return height == width
        }
			// get() = height == width
}
```

isSquare 프로퍼티는 자체 구현을 제공하는 getter가 존재

클라이언트가 프로퍼티에 접근할 때, get() 에 해당하는 로직으로 프로퍼티 값을 매번 다시 계산한다.

파라미터가 없는 함수를 정의하는 방식과 커스텀 getter를 정의하는 방식은 구현이나 성능상 차이는 없다.

### 2.2.3 코틀린 소스코드 구조 : 디렉터리와 패키지

모든 코틀린 파일의 맨 앞에는 package문을 넣을 수 있다. 
같은 패키지에 속해 있다면 다른 파일에서 정의한 선언일지라고 직접 사용 가능

반면, 다른 패키지에서 정의한 선언을 사용하려면 import를 통해 선언

```kotlin
// 클래스와 함수 선언을 패키지에 넣기
package geometry.shapes

import java.util.*

class Rectangle(val height: Int, val width: Int) {
    val isSquare: Boolean
        get() = height == width
}

fun createRandomRectangle(): Rectangle {
    val random = Random()
    return Rectangle(random.nextInt(), random.nextInt())
}
```

```kotlin
// 다른 패키지에 있는 함수 임포트하기
package geometry.shapes.createRandomRectangle

fun main(args: Array<String>) {
	println(createRandomRectangle().isSquare)
}
```

자바와 같이 패키지별로 디렉터리를 구성하는 편이 낫다. 특히, 자바와 코틀린을 함께 사용하는 프로젝트에서는 자바 방식을 따르는 게 중요하다. 
</br></br>
# 2.3 선택 표현과 처리 : enum과 when

### 2.3.1 클래스 정의

```kotlin
// 간단한 enum 클래스
enum class Color {
    RED, ORANGE, YELLOW, GREEN, INDIGO, VIOLET
}
```

Java에서는 `enum`으로 선언, 코틀린에서는 `enum class` 로 선언

```kotlin
// 프로퍼티와 메소드가 있는 enum 클래스

enum class Color(val r: Int, val g: Int, val b: Int)   // 상수 프로퍼티 정의
{  

    RED(255, 0, 0),
    ORANGE(255, 165, 0),
    YELLOW(255, 255, 0),
    GREEN(0, 255, 0),
    BLUE(0, 0, 255),
    INDIGO(75, 0, 130),
    VIOLET(238, 130, 238);    // 반드시 세미콜론으로 종료

    fun rgb() = (r * 256 + g) * 256 + b  // enum 클래스 안에 메소드 정의
}

fun main() {
    println(Color.BLUE.rgb())
}
```

### 2.3.2 when으로 enum 클래스 다루기

```kotlin
fun getMnemonic(color: Color) =
 when (color) {   // 함수 반환 값으로 when 식을 사용
    Color.RED -> "Rechard"
    Color.ORANGE -> "Of"
    Color.YELLOW -> "York"
    Color.GREEN -> "Grave"
    Color.BLUE -> "Battle"
    Color.INDIGO -> "In"
    Color.VIOLET -> "Vain"
}

fun getWarmth(color: Color) = when (color) {
    Color.RED, Color.ORANGE, Color.YELLOW -> "warm"
    Color.GREEN -> "neutral"
    Color.BLUE, Color.INDIGO, Color.VIOLET -> "cold"
}

fun main() {
    println(getMnemonic(Color.BLUE))
    println(getWarmth(Color.ORANGE))
}
```

자바와 달리 각 분기 끝에 break 를 넣지 않아도 된다.

한 분기 안에 여러 값을 매치 패턴으로 사용할 경우, 콤마(,)로 구분한다.

enum 상수 값을 import 해서 enum 클래스 수식자 없이 enum을 사용 가능하다.

```kotlin
import ch2.Color.*

fun getMnemonic(color: Color) = when (color) {
    RED -> "Rechard"
    ORANGE -> "Of"
    YELLOW -> "York"
    GREEN -> "Grave"
    BLUE -> "Battle"
    INDIGO -> "In"
    VIOLET -> "Vain"
}
```

### 2.3.3 when과 임의의 객체를 함께 사용

자바의 `swtich`와 달리 코틀린 `when` 의 분기 조건은 임의의 객체를 허용한다.

```kotlin
fun mix(c1: Color, c2: Color) =
    // when 식의 인자로 아무 객체나 사용 가능, when은 받은 인자 객체가 각 분기 조건에 있는 객체와 같은지 테스트
    when (setOf(c1, c2)) {
        setOf(RED, YELLOW) -> ORANGE
        setOf(YELLOW, BLUE) -> GREEN
        setOf(BLUE, VIOLET) -> INDIGO
        // 매치되는 분기 조건이 없으면 이 문장 실행
        else -> throw Exception("Dirty color")
    }
```

- setOf 함수 란?
    
    인자로 전달받은 여러 객체를 그 객체들을 포함하는 집한인 Set 객체로 만드는 함수,
    
    Set은 원소가 모여있는 컬렉션으로 각 원소의 순서는 중요하지 않다.
    

### 2.3.4 인자 없는 when 사용

when에 아무 인자도 없으려면 각 분기의 조건이 불리언 결과를 계산하는 식이어야 한다.
```kotlin
fun mixOptimized(c1: Color, c2: Color) =
    // when 에 아무 인자가 없다.
    when {
        (c1 == RED && c2 == YELLOW) ||
                (c1 == YELLOW && c2 == RED) -> ORANGE
        (c1 == YELLOW && c2 == BLUE) ||
                (c1 == BLUE && c2 == YELLOW) -> GREEN
        (c1 == BLUE && c2 == VIOLET) ||
                (c1 == VIOLET && c2 == BLUE) -> INDIGO
        else -> throw Exception("Dirty color")
    }
```

### 2.3.5 스마트 캐스트 : 타입 검사와 타입 캐스트를 조합

`( 1 + 2 ) + 4` 와 같은 산술식을 계산하는 함수를 만들어 보자.

```kotlin
// 식을 위한 인터페이스
interface Expr

// value라는 프로퍼티만 존재하는 단순한 클래스, Expr 인터페이스를 구현
class Num(val value: Int) : Expr

// Expr 타입의 객체라면 Sum 연산의 인자가 될 수 있다.
// 따라서, Num이나 다른 Sum이 Sum의 인자로 올 수 있다.
class Sum(val left: Expr, val right: Expr) : Expr

// Java 스타일
// if 사용해서 식 계산
fun eval(e: Expr): Int {
    if (e is Num) {
        val n = e as Num
        return n.value
    }
    if (e is Sum) {
        // 변수 e 에 대해 스마트 캐스트를 사용한다.
        return eval(e.right) + eval(e.left)
    }

    throw IllegalArgumentException("Unkown expression")
}
```

Expr은 식을 위한 인터페이스

Sum, Num 클래스는 Expr 인터페이스를 구현하는 클래스

코틀린에서는 `is` 를 사용해서 변수 타입을 검사 (Java의 `instanceof` 와 유사) 

- 하지만 Java에서는 `instanceof` 로 변수타입 확인 후, 그 타입에 속한 멤버에 접근하기 위해서는 개발자가 명시적으로 타입 캐스팅이 필요
- 코틀린에서는 `is` 로 검사한 후, 참이면, 컴파일러가 알아서 캐스팅 해준다. → Smart Cast
- 스마트 캐스트는 `is` 로 변수에 든 값의 타입을 검사한 다음, 그 값이 바뀔 수 없는 경우에만 동작

명시적으로 타입 캐스팅 하려면 `as` 키워드를 사용 → `val n = e as Num`

### 2.3.6 리팩토링 : if를 when으로 변경

코틀린의 if는 식 → 값을 반환한다. 값을 만들어 낸다.→ 따라서 자바와 달리 3항 연산자가 따로 없다.

Java의 if는 문 → 값을 반환하지 않는다.

```kotlin
// 값을 만들어내는 if 식

fun eval(e: Expr): Int =
    if (e is Num) {
        e.value
    } else if (e is Sum) {
        eval(e.right) + eval(e.left)
    } else {
        throw IllegalArgumentException("Unkown expression")
    }
```

```kotlin
// if 중첩 대신 when 사용하기
fun eval(e: Expr): Int =
    when (e) {
        is Num -> e.value  // 인자 타입을 검사하는 when 분기에서 스마트 캐스트 됨
        is Sum -> eval(e.left) + eval(e.right)
        else -> throw IllegalArgumentException("Unkown expression")
    }
```

### 2.3.7 if와 when의 분기에서 블록 사용

if나 when 모두 분기에 블록 사용 가능 → 블록의 마지막 문장이 블록 전체의 결과가 된다.

```kotlin
fun evalWithLogging(e: Expr): Int =
    when (e) {
        is Num -> {
            println("num: ${e.value}")
            e.value   // 블록의 마지막 식이므로 e.value가 반환된다.
        }
        is Sum -> {
            val left = evalWithLogging(e.left)
            val right = evalWithLogging(e.right)
            println("sum: $left + $right")
            left + right
        }
        else -> throw IllegalArgumentException("Unkown expression")
    }
```

## 2.4 대상을 이터레이션 : while 과 for 루프

### 2.4.1 while 루프

```kotlin
// 조건이 참인 동안 본문 반복 실행
while(조건) {
	/*....*/
}

// 맨 처음 무조건 본문 한번 실행 후, 조건이 참인 동안 본문 반복 실행
do{
	/*....*/
} while(조건)
```

### 2.4.2 수에 대한 이터레이션 : 범위와 수열

코틀린은 자바의 for 루프에 해당하는 요소가 없다.

```kotlin
val oneToTen = 1..10 // 양 끝 구간 포함, 1 ~ 10
```

```kotlin
fun fizzbuxx(i: Int) =
    when {
        i % 15 == 0 -> "FizzBuzz "
        i % 3 == 0 -> "Fizz "
        i % 5 == 0 -> "Buzz "
        else -> "$i "
    }

for (i in 1..100) {
        println(fizzbuxx(i))
    }

    for (i in 100 downTo 1 step 2){
        print(fizzbuxx(i))
    }
```

`step` : 증가값 

`downTo` : 역방향

`..` : 항상 범위의 끝 포함

`until` : 끝 값 포함 하지 않음 → `for(x in 0 until size)`  or `for (x in 0 .. size-1)`

### 2.4.3 맵에 대한 이터레이션

```kotlin
// A ~ F 까지 문자 범위 이터레이션
for(c in 'A'..'F'){
    // 아스키 코드를 2진 표현으로 변경
    val binary = Integer.toBinaryString(c.toInt())
    // c를 키로 c의 2진 표현을 맵에 넣는다.
    binaryReps[c] = binary  // 자바의 map put 과 유사: binaryReps.put(c, binary)
}

// 맵에 대한 이터레이션, 맵의 Key와 Value를 두 변수에 대입
for((letter, binary) in binaryReps) {
    println("$letter = $binary")
}
```

```kotlin
val list = arrayListOf("10", "11", "1001")

// 인덱스와 함께 컬렉션을 이터레이션 한다.
for ((idx, element) in list.withIndex()){
    println("$idx: $element")
}
```

### 2.4.4 in으로 컬렉션이나 범위의 원소 검사

in 연산자 : 어떤 값이 범위에 속하는 지 확인

!in 연산자 : 어떤 값이 범위에 속하지 않는 지 확인

```kotlin
fun isLetter(c: Char) = c in 'a'..'z' || c in 'A'..'Z'
fun isNotDigit(c: Char) = c !in '0'..'9'

println(isLetter('q'))      // true
println(isNotDigit('x'))    // true

fun recognize(c: Char) = when (c) {
    in '0'..'9' -> "It's a digit!"
    in 'a'..'z', in 'A'..'Z' -> "It's a letter!"
    else -> "I don't know"
}

println(recognize('8'))     // It's a digit!
```

비교가 가능한 클래스(java.lang.Comparable 인터페이스를 구현한 클래스)라면 그 클래스의 인스턴스 객체를 사용해 범위를 만들 수 있다.

## 2.5 코틀린의 예외 처리

코틀린의 기본 예외 처리 구문은 자바와 유사

예외 인스턴스를 만들 때 new 키워드를 붙일 필요가 없다.

자바와 달리 코틀린의 throw는 식이므로, 다른 식에 포함이 가능

```kotlin
val percentage =
    if (number in 0..100)
        number
    else
				// throw는 식이다. -> 값을 만들어낸다.
        throw IllegalArgumentException("A percentage value must be between 0 and 100: $number")
```

### 2.5.1 try, catch, finally

```kotlin
fun readNumber(reader: BufferedReader): Int? {
    try {
        val line = reader.readLine()
        return Integer.parseInt(line)
    } catch (e: NumberFormatException) {
        return null
    } finally {
        reader.close()
    }
}
```

Java 코드와 가장 큰 차이는 throws 절에 코드가 없다는 점

- Java에서는 함수를 작성할 때, 함수 선언 뒤에 throws IOException을 붙여야한다.
    
    → IOException이 Checked Exception이기 때문, 자바에서는 Checked Exception을 명시적으로 처리해야한다.
    
    → 어떤 함수가 던질 가능성이 있는 예외나 그 함수가 호출한 다른 함수에서 발생할 수 있는 모든 예외를 모두 catch 로 처리해야 하며, 처리하지 않은 예외는 throws 절에 명시
    

코틀린도 다른 최신 JVM 언어 처럼 Checked Exception, UnChecked Exception을 구별하지 않는다. (자바는 Checked Exception 처리를 강제한다.)

### 2.5.2. try를 식으로 사용

코틀린의 try 키워드는 식이다. → 값을 만들어낸다.

따라서 try 의 값을 변수에 대입할 수 있다. try는 if와 달리 중괄호 {}로 둘러싸야한다.

```kotlin
fun readNumber(reader: BufferedReader) {
		// 예외가 발생하지 않으면, 이 값을 사용
    val number = try {
        Integer.parseInt(reader.readLine())
    } catch (e: NumberFormatException) {
        // return //catch 블록 다음 코드는 실행 되지 않음
        null      // 예외가 발생하면 null값을 사용
    }
    println(number)
}
```



출처 : (서적) Kotlin in Action 드미트리 제메로프, 스베트라나 이사코바 저자 /오현석 옮김
