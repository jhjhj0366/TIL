# [Spring Expression Language](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#expressions)란?

- 객체의 그래프를 조작하고 조회하는 기능을 제공
- 문법은 EL ([Unified EL](https://docs.oracle.com/javaee/5/tutorial/doc/bnahq.html))과 비슷하지만, 메소드 호출 및 문자열 템플릿 기능 등 추가적인 기능을 제공한다.
- OGNL, MVEL, JBoss EL 등의 Java에서 사용할 수 있는 여러 EL이 있지만, SpEL은 모든 Spring 프로젝트에 전반에 걸쳐 사용할 EL로 만들어졌다.
- Spring 3.0 부터 지원한다.

### SpEL의 구성

- ExpressionParser parser = new SpelExpressionParser()
    - ExpressionParser 인터페이스는 문자열 표현을 파싱한다ㅏ.
    
    ```java
    ExpressionParser parser = new SpelExpressionParser();
    Expression exp = parser.parseExpression("'Hello World'");
    String message = (String) exp.getValue();
    ```
    
- Expression expression = parser.parseExpression(“SpEL 표현식”)
- StandardEvaluationContext context = new StandardEvaluationContext(bean)
    - EvaluationContext 인터페이스는 프로퍼티, 메서드, 필드를 처리하고 타입변환을 수행하는 표현식을 평가할 때 EvaluationContext 인터페이스를 사용한다.
    - StandardEvaluationContext는 setRootObject() 메서드나 생성자에 루트객체를 전달해서 평가에 사용할 루트객체를 지정하는 곳이다.
- String value = expression.getvalue(context, String.class)

### SpEL 문법

- `#{표현식}`
- `${”프로퍼티”}`
- 표현식 안에 프로퍼티를 쓸 수 있지만, 반대는 안됨
    - `#{${my.data(프로퍼티명) + 1}}`
- 레퍼런스에 더 자세히 기재되어 있음

```java
// SpEL #, 표현식을 참고하는 방법
    @Value("#{1 + 1}")
    int value;

    @Value("#{'hello' + 'worlld'}")
    String greeting;

    @Value("#{1 eq 1}")
    boolean trueOrFalse;

    @Value("test")
    String test;

    // property를 참고하는 방법
    @Value("${my.value}")
    int myValue;

    @Value("#{${my.value} eq 100}")
    boolean isMyValue100;

    @Value("#{sample.data}")
    int sampleData;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println("value = " + value);
        System.out.println("greeting = " + greeting);
        System.out.println("trueOrFalse = " + trueOrFalse);
        System.out.println("test = " + test);
        System.out.println("myValue = " + myValue);
        System.out.println("isMyValue100 = " + isMyValue100);
        System.out.println("sampleData = " + sampleData);

        ExpressionParser parser = new SpelExpressionParser();
        Expression expression = parser.parseExpression("2 + 100");
        Integer value = expression.getValue(Integer.class);
        System.out.println("value = " + value);

    }
```

### 사용하는 곳

- `@Value`  애노테이션
- @ConditionalOnExpression 애노테이션
- [Spring Security](https://docs.spring.io/spring-security/site/docs/3.0.x/reference/el-access.html)
    - 메소드 시큐리티, @PreAuthorize, @PostAuthorize, @PreFilter, @PostFillter
    - XML Intercepter URL 설정
- [Spring Data](https://spring.io/blog/2014/07/15/spel-support-in-spring-data-jpa-query-definitions)
    - @Query 애노테이션
- [Thymeleaf](https://blog.outsider.ne.kr/997)





---
출처 : [[인프런 강의] 백기선 - 스프링 프레임워크 핵심기술](https://www.inflearn.com/course/spring-framework_core)
참고 : https://blog.outsider.ne.kr/997
