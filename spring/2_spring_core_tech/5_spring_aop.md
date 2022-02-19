# 스프링 AOP 개념

### AOP(Aspect-Oriented-Programming)

OOP를 보완하는 수단으로 흩어진 Aspect를 모듈화 할 수 있는 프로그래밍 기법

### 흩어진 관심사 (Crosscutting Concerns)

Concerns는 여러 클래스들에 걸쳐서 나타나는 비슷한 코드들, Filed 주입들을 말한다..

ex ) Transaction 처리, 성능관련 로깅 등

![](https://images.velog.io/images/dev_jhjhj/post/10469f72-3bae-4485-a24e-3850f7154756/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202022-02-19%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%202.12.19.png)

### AOP를 적용하면...
![](https://images.velog.io/images/dev_jhjhj/post/f7d1b08f-9436-463f-b60b-d1b335b74194/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202022-02-19%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%202.12.47.png)


⇒ 이처럼 공통된 로직, 코드들을 모듈화하여 묶어서 개발할 수 있따.

### AOP의 주요 개념

- Aspect와 Target
    - Aspect는 모듈을 묶음화 한 것
    - Target은 적용 대상
- Advice
    - 모듈이 해야할 일들
- Joint point와 Pointcut
    - joint point는 합류점을 의미한다. 어떤 메소드, 클래스, filed값 접근시점, 실행시점에 이 Advice를 끼워 넣어라
    - Pointcut은 이 모듈을 어디에 적용하는 지를 의미한다.
    

### AOP의 구현체

- AOP는 꼭 Spring, Java에만 국한된 것이 아니다.
- 참고 : [https://ko.wikipedia.org/wiki/관점_지향_프로그래밍](https://ko.wikipedia.org/wiki/%EA%B4%80%EC%A0%90_%EC%A7%80%ED%96%A5_%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D)
- AspectJ는 자바, 스프링은 Spring AOP(극한적인 기능만 존재)

### AOP 적용 방법

- 컴파일 시점
    - Java를 .class 파일로 컴파일하는 시점에 조작된 바이트 코드를 실행시킨다. 로드, 런타임은 따로 안들지만, 별도의 컴파일 과정이 필요하다.
- 로드 타임
    - 클래스 파일을 로딩하는 시점에 weaving, load time weaving
    - 클래스 로딩 시간 증가, load time weaver를 Java agent 에 설정해줘야 함
    - 다양한 Aspect 사용 가능
- 런타임
    - Proxy 객체로 감싸는 것, 최초의 Bean 설정 시간이 증가한다.
    

⇒ 컴파일 시점, 로드타임에 AOP 적용 방법은 주로 Aspect에서 사용

⇒ 런타임 시점에 AOP 적용은 주로 Spring AOP에서 사용한다.

</br>
</br>



# 스프링 AOP : 프록시 기반 AOP

### 스프링 AOP 특징

- Proxy 기반의 AOP 구현체
- Spring Bean에만 AOP를 적용할 수 있다.
- 모든 AOP 기능을 제공하는 것이 목적이 아니라, 스프링 IoC와 연동해서 Enterprise Application에서 가장 흔한 문제에 대한 해결책을 제공하는 것이 목적이다.

### Proxy Pattern

- 프록시 패턴을 사용하는 이유
    - 기존 코드 변경없이 접근 제어, 부가기능을 추가하기 위해서
    - 클라이언트는 기존 코드를 건드리지 않고, 인터페이스 타입의 프록시 객체를 사용할 수 있다.
    
![](https://images.velog.io/images/dev_jhjhj/post/4d867c9c-0880-4dc3-a520-ba55736048cd/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202022-02-19%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%203.07.29.png)
    

- 프록시 패턴의 문제점
    - 매번 프록시 캘르스를 작성해야 한다.
    - 여러 클래스, 여러 메소드에 적용하려면 객체들과의 관계도가 복잡해진다.
    
    ex ) 
    
    ```java
    
    // interface
    public interfaceEventService {
    
    	voidcreateEvent();
    
    	voidpublishEvent();
    
    	voiddeleteEvent();
    }
    
    // Proxy
    @Primary
    @Service
    public class ProxySimpleEventService implements EventService{
    
        @Autowired
        SimpleEventService simpleEventService;
    
        @Override
        public void createEvent() {
            long begin = System.currentTimeMillis();
            simpleEventService.createEvent();
            System.out.println(System.currentTimeMillis()-begin);
        }
    
        @Override
        public void publishEvent() {
            long begin = System.currentTimeMillis();
            simpleEventService.publishEvent();
            System.out.println(System.currentTimeMillis()-begin);
    
        }
    
        @Override
        public void deleteEvent() {
            simpleEventService.deleteEvent();
        }
    }
    
    // Real Subject
    @Service
    public class SimpleEventService implements EventService{
    
        @Override
        public void createEvent() {
            long begin = System.currentTimeMillis();
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("create an evnet");
    
            // Crosscutting concern : 각 메소드 마다 공통된 모듈 
            System.out.println(System.currentTimeMillis()-begin);
        }
    
        @Override
        public void publishEvent() {
            long begin = System.currentTimeMillis();
    
            try {
                Thread.sleep(2000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("Published an event");
    
            System.out.println(System.currentTimeMillis()-begin);
        }
    
        public void deleteEvent(){
            System.out.println("Delete event");
        }
    }
    ```
    

⇒ 그래서 Spring AOP 등장

### Spring AOP

- 스프링 IoC 컨테이너가 제공하는 기반 시설과 Dynamic 프록시를 사용해서 여러 복잡한 문제를 해결한다.
- Dynamic Proxy : 동적으로 프록시 객체를 생성하는 방법
    - 자바가 제공하는 방법은 인터페이스 기반 프록시 생성
    - CGlib은 클래스 기반 프록시도 지원
- 스프링 IoC : 기존 빈을 대체하는 동적 프록시 빈을 만들어서 등록시켜준다.
    - 클라이언트 입장에서 코드 변경이 없다.
    - AbstractAutoProxyCreator는 BeanPostProcessor를 상속받는다.
    - [https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/aop/framework/autoproxy/AbstractAutoProxyCreator.html](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/aop/framework/autoproxy/AbstractAutoProxyCreator.html)
    - [https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/beans/factory/config/BeanPostProcessor.html](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/beans/factory/config/BeanPostProcessor.html)



</br>

</br>


# 스프링 AOP : @AOP

애노테이션 기반의 스프링 @AOP

사용하기 위해서는 spring-boot-starter-aop 의존성 필요

```xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
 </dependency>
```

### Aspect 정의

- Aspect에서는 Advice(해야할 일)과 Point-cut(어디에 적용할 것인가, 적용시점)를 정의해야 한다.
- Aspect로 사용할 클래스는 Bean으로 등록되어야 한다.
- @Around 애노테이션을 이용해서 Point-cut을 적용할 수 있다.

```java
@Aspect
@Component
public class PerfAspect {

    // createEvent, publlishEvent 메소드에 적용될 aop 이다.
    // @Around에 Pointcut을 정의할 수 있다. 즉 적용시점을 정의할 수 있다.
   // @Around("execution(* me.jhjhj..*.EventService.*(..))")
   // @Around("bean(simpleEventService)")
    @Around("@annotation(PerfLogging)")
    public Object logPerf(ProceedingJoinPoint pjp) throws Throwable{
        long begin = System.currentTimeMillis();
        Object retVal = pjp.proceed();
        System.out.println(System.currentTimeMillis() - begin);
        return retVal;
    }

    // 메소드 실행 이전에
    @Before("bean(simpleEventService)")
    public void hello(){
        System.out.println("hello");
    }
}

// RetentionPolicy에도 다양한 스코프가 존재, 
// RetentionPolicy.CLASS : 이 애노테이션을 class 파일까지만 적용하겠다.
@Documented
@Retention(RetentionPolicy.CLASS)
public @interface PerfLogging {
}
```

### Point-Cut 정의

- @Pointcut(표현식)
- 주요 표현식
    - execution : 패키지 경로 (패키지 경로에 해당하는 대상들 모두가 aop 에 적용)
    - @annotation : 별도의 애노테이션 정의 후, aop로 사용하고 싶은 메소드, 클래스만 적용이 가능하다.
    - bean : bean 이름 기재
- Point-Cut 조합
    - &&, ||, !

### Advice 정의

- @Before
- @AfterReturning
- @AfterThrowing
- @Around

✅ Spring AOP 공식 레퍼런스 : [https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#aop-pointcuts](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#aop-pointcuts)




</br></br>


출처 : [[인프런 강의] 백기선 - 스프링 프레임워크 핵심기술](https://www.inflearn.com/course/spring-framework_core)
