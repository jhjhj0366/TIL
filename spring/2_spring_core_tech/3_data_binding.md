# 1. 데이터 바인딩 추상화 : PropertyEditor

[org.springframework.validation.Databinder](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/validation/DataBinder.html)

### 데이터 바인딩이란 ?

Property 값을 타겟 객체에 설장하는 기능이다.

사용자의 입력 값을 Model 값에 동적으로 변환해서 넣어주는 기능이다.

### 사용하는 이유

사용자가 입력하는 값은 대부분 문자열, 숫자 일 것이다. 그 값을 객체가 가지고 있는 속성 타입이나 도메인 타입으로 변환해서 넣어주기 위해 사용한다.

### PropertyEditor

- Spring 3.0 이전까지 Databind가 변환 작업을 사용하던 interface (고전적인 방법이다.)
- Thread-Safe 하지 않다.
    - statefulll 하다.
    - 상태 정보를 저장하고 있기 때문에 싱글톤 객체로 등록하여 사용하는 것은 금한다.
    - Object와 String 간의 변환만 가능, 제한적인 사용

```java

@RestController
public class EventController {

    @InitBinder
    public void init(WebDataBinder webDataBinder){
        webDataBinder.registerCustomEditor(Event.class, new EventEditor());
    }

    @GetMapping("/event/{event}")
    public String getEvent(@PathVariable Event event){
        System.out.println("event = " + event);
        return event.getId().toString();
    }
}

// Thread-Safe 하지 않는다.
// 절대로 proportyEditor 절대로 싱글톤 빈으로 등록해서 사용하면 안됨
// thread-scope의 빈 일 경우에는 그나마 가능
public class EventEditor extends PropertyEditorSupport {

    @Override
    public String getAsText() {
        Event event = (Event) getValue();
        return event.getId().toString();
    }

    @Override
    public void setAsText(String text) throws IllegalArgumentException {
        setValue(new Event(Integer.parseInt(text)));
    }
}

```

---
</br>

# 2. 데이터 바인딩 추상화 : Converter와 Formatter

### Converter

[https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/convert/converter/Converter.html](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/convert/converter/Converter.html)

- Source 타입을 Target 클래스로 변환할 수 있는 인터페이스
- 상태 정보를 저장하지 않음
    - stateless, Thread-Safe 하다.
- ConverterRegistry에 등록하여 사용한다.

```java
public class EventConverter {

    @Component
    public static class StringToEventConverter implements Converter<String, Event> {
        @Override
        public Event convert(String source) {
            return new Event(Integer.parseInt(source));
        }
    }

    @Component
    public static class EventtoStringConverter implements Converter<Event, String> {

        @Override
        public String convert(Event source) {
            return source.getId().toString();
        }
    }

}
```

### Formatter

[https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/format/Formatter.html](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/format/Formatter.html)

- PropertyEditor와 유사
- Object와 String 간의 변환을 담당하는 인터페이스
- 문자열을 Locale에 따라 다국화(i18n) 기능도 제공
- FormatterRegistry에 등록하여 사용한다.
- 👉  보통 Web과 관련된 데이터 바인딩을 진행할 경우, Formatter 사용을 추천 (보통 String to Object 이기 때문)

```java
@Component
public class EventFormatter implements Formatter<Event> {

    @Override
    public Event parse(String text, Locale locale) throws ParseException {
        return new Event(Integer.parseInt(text));
    }

    @Override
    public String print(Event object, Locale locale) {

        return object.getId().toString();
    }
}
```

### ConversionService

[https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/convert/ConversionService.html](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/convert/ConversionService.html)

- `ConversionService`에서 `Converter`와 `Formatter`를 등록해준다.
- Spring MVC, Bean 설정, SpEL에 사용한다.
- `DefaultFormattingConversionService` 클래스에서 `FormatterRegistry`, `ConversionService` 인터페이스를 다 구현한다.
- 상속 구조

![](https://images.velog.io/images/dev_jhjhj/post/8a8d1492-65e0-48af-b58b-c0b6ec752c88/defaultformatter.png)

### SpringBoot에서는...

- 웹 애플리케이션인 경우에는 `DefaultFormattingVonversionService` 를 상속하여 만든 `WebConversionService`를 빈으로 등록해준다.
- Formatter와 Converter 로 사용할 클래스를 빈으로 등록하면, 자동으로 Formatter와 Converter  역할로 등록해준다.
- SpringBoot에 등록되어 있는 Formatter와 Converter 를 확인하는 방법
    
    ```java
    @Component
    public class AppRunner implements ApplicationRunner {
    
        @Autowired
        private ConversionService conversionService;
    
        @Override
        public void run(ApplicationArguments args) throws Exception {
            System.out.println(conversionService);
        }
    
    }
    ```
    

### 테스트 코드

- MockMvcTest 사용 시 기본적인 spring web 관련 빈들만 생성하여 테스트를 진행한다.
- 따라서 `@WebMvcTest({EventFormatter.class, EventController.class})` 와 같이 Bean으로 등록된 클래스들을 추가하여 테스트 할 수 있다.



---
출처 : [[인프런 강의] 백기선 - 스프링 프레임워크 핵심기술](https://www.inflearn.com/course/spring-framework_core)
