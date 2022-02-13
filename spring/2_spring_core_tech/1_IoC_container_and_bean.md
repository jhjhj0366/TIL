# 1. 스프링 IoC 컨테이너와 빈

### Inversion Of Control (제어의 역전)
    
   - 객체에 대한 제어 흐름을 직접 제어하는 게 아니라, 외부에서 관리하는 것 
    </br> → 스프링 IoC 컨테이너를 사용 가능 (꼭 Spring 에서만 사용되는 것이 아니라, 일반 POJO에서도 코드로 구현 가능하다.)
    

### Dependency Injection (의존 관계 주입)
    
   - 어떤 객체가 사용하는 의존 객체를 개발자가 직접 코드로 구현하는 것이 아니라, 외부에서 주입받아 사용하는 방법을 말한다. 
   </br> 
    
### Spring IoC 컨테이너
   - [BeanFactory](https://docs.spring.io/spring-framework/docs/5.0.8.RELEASE/javadoc-api/org/springframework/beans/factory/BeanFactory.html) : 스프링 컨테이너 가장 최상단의 인터페이스 (reference docs 참고)
   - Bean 설정 소스부터 Bean의 정의를 읽어들여 Bean을 구성하고 제공한다.    </br> 
    
### Bean
    
   - Spring IoC 컨테이너가 관리하는 객체
    
   - Bean으로 등록이 되어 있어야 Spring IoC 컨테이너에 의해 의존성 주입이 가능하다.
    
   - Spring IoC 컨테이너가 Bean의 LifeCircle 관리
    
   - 기본적인 Scope은 싱글톤이다. (싱글톤 : 인스턴스 1개 / 프로토타입 : 매번 다른 인스턴스 생성)
    
   </br> → 기본적으로 싱글톤으로 생성되기 때문에 메모리 관점, 런타임시 성능 최적화에도 효율적임
   
   </br> 
    
### [ApplicationContext](https://docs.spring.io/spring-framework/docs/5.0.8.RELEASE/javadoc-api/org/springframework/context/ApplicationContext.html)
    
   - BeanFactory 인터페이스를 상속 받는 인터페이스
    
   - 가장 많이 사용하는 인터페이스
    
   - 메시지 소스 처리 기능 (i18n, internationalization, 메세지 다국화)
    
   - 이벤트 발행 기능
    
   - Resource 로딩 기능 등... reference docs 참고
    
### 🖊 참고
    
   [BeanFactory (Spring Framework 5.0.8.RELEASE API)](https://docs.spring.io/spring-framework/docs/5.0.8.RELEASE/javadoc-api/org/springframework/beans/factory/BeanFactory.html)
   [ApplicationContext (Spring Framework 5.0.8.RELEASE API)](https://docs.spring.io/spring-framework/docs/5.0.8.RELEASE/javadoc-api/org/springframework/context/ApplicationContext.html)

---
</br><br>

# 2. ApplicationContext와 다양한 빈 설정 방법

### Spring IoC 컨테이너의 역할
   - Bean Instance 생성
   - 의존 관계 설정
   - Bean을 제공
   </br></br> 
### ApplicationContext 설정 방법
   - ClassPathXmlApplicationContext(XML) → 과거 방법
    
   ```java
   ApplicationContext context = new ClassPathXmlApplicationContext("application.xml");       // xml 기반으로 bean 설정
   ```
    
   - AnnotationConfigApplicationContext(Java) → Spring 2.5 버전부터 annotation 기반
    
   - @Configuration 어노테이션을 통해, Config 파일 안에 있는 @Bean들을 빈을 등록
    
   ```java
   ApplicationContext context = new AnnotationConfigApplicationContext(ApplicationConfig.class);   // java Config 파일을 기반으로 bean 설정
   ```
   </br></br> 
### @SpringBootApplication
    
   - 해당 어노테이션이 붙어있는 클래스의 package를 기반으로 Bean 등록 → SpringBoot
    
   ``` java
    @Target({ElementType.TYPE})
    @Retention(RetentionPolicy.RUNTIME)
    @Documented
    @Inherited
    @SpringBootConfiguration
    @EnableAutoConfiguration
    @ComponentScan
   ```
    
 </br></br> 
### Bean 설정
   - Bean 에 대한 명세 및 정의를 담고 있다.
        - 이름, 클래스, Scope, constructor argument, property 등
 </br></br> 
### ComponentScan
   - 설정 방법
        - XML 설정 (과거) : context:component-scan
        - 자바 설정 : @ComponentScan



---
</br><br>

# 3.@Autowired

@Autowired 애노테이션을 이용해서 필요한 의존 객체의 타입과 이름에 해당하는 빈을 찾아 주입한다.

required 옵션 : default가 true, false는 optional의 개념

사용할 수 있는 위치
</br></br>
#### Constructor (Spring 4.3 부터는 생략 가능)

 ```java
@Autowired
    public BookService(BookRepository bookRepository){
        this.bookRepository = bookRepository;
    }
```

####  Setter

 ```java
@Autowired(required = false)
    public void setBookRepository(BookRepository bookRepository) {
        this.bookRepository = bookRepository;
    }
 ```

#### Field

```java
@Autowired(required = false)
    BookRepository bookRepository;
```
---
</br></br>
### 같은 타입의 Bean이 여러개 있다면 ?

`Consider marking one of the beans as @Primary, updating the consumer to accept multiple beans, or using @Qualifier to identify the bean that should be consumed`

- @Primary : Bean으로 만들고 싶은 클래스에 해당 애노테이션을 사용, (기본 값으로 사용하겠다는 의미)
- @Qualifier : Bean으로 주입받는 곳에 해당 애노테이션과 Bean이름 주입 (첫글자가 소문자인 카멜 케이스 이름, Spring Container가 최초에 빈 등록할 때의 이름 형식임) → TypeSafe 하지 않음
- 해당 타입의 Bean 이름을 모두 주입 받기
- 인스턴스 이름으로 구분 (정말 비추)
    
    ```java
    // BookRepository 인터페이스를 상속받는 여러 클래스 중
    // MyBookResitory.class를 사용하겠다.
    @Autowired
    BookRepository myBookResitory;    
    ```
    
    - Spring IoC의 최상단 인터페이스 BeanFactory의 LifeCycle을 보면 알 수 있다.
    - BookRepository가 myBookResitory를 사용할 것이라는 것은 Initialization 전에 해준다.
    </br> ( ref. `postProcessBeforeInitialization` methods of BeanPostProcessors)



---
</br><br>

# 4. Component와 컴포넌트 스캔

@ComponentScan은 Spring 3.1부터 적용이 되었다.<br>

@ComponentScan은 `basePackages(String Type)` 패키지 이름으로 컴포넌트 스캔을 하는 데,<br>
String값으로 패키지 이름을 받는 것은 TypeSafe하지 않다. <br><br>
그래서 TypeSafe한 방법으로 `basePackageClasses` 라는 옵션을 사용한다.<br>
`basePackageClasses` 을 사용하면, 해당 옵션에 전달된 클래스가 있는 패키지를 기준으로 컴포넌트 스캔을 시작한다.

→ SpringBoot에서 @SpringBootApplication 애노테이션은 @ComponentScan을 포함하고 있다.<br>
따라서, @SpringBootApplication이 붙은 클래스의 Package와 다른 상속 Package에 있는 클래스들은 ComponetScan이 되지 않는다.
<br><br>

#### 컴포넌트 스캔 주요 기능
   - 스캔 위치를 설정한다.
   - filter : 어떤 애노테이션을 스캔할 지 or 스캔하지 않을 지
       - excludeFilters → TypeExcludeFilter.class, AutoConfigurationExcludeFilter.class
    <br><br>
    
#### ComponentScan의 대상
   - @Repository
   - @Service
   - @Controller
   - @Configuration
   <br><br> 
   
#### function을 이용한 Bean 등록
   - Bean 등록 시, ComponentScan 에 해당되지 않는 빈들을 등록할 수 있다.
   - 성능을 높일 수 있다. (하지만, 필요에 따라 사용해야 한다. 무조건 사용은 비추)

```java

// MyBean.class / ComPonentScan 외에 있는 bean을 function을 이용해서 등록이 가능하다. 
public static void main(String[] args) {
	new SpringApplicationBuilder()
			.sources(Demospring51Application.class)
			.initializers((ApplicationContextInitializer<GenericApplicationContext>)
		applicationContext -> {
			applicationContext.registerBean(MyBean.class);
	})
	.run(args);
}
```

# 5. 빈의 스코프

### Scope

### singleton

- (Spring의 디폴트) 전체 application 내 해당 빈의 인스턴스가 오직 1개

### prototype

인스턴스를 여러개로 만들 수 있다.

- Request: request에 따라
- Session : 세선에 따라
- WebSocket : 웹 소켓에 따라

### prototype의 bean이 singleton을 참조하면 ?

참조하는 싱글톤은 늘 인스턴스가 1개이기 때문에 개발자의 의도대로 사용이 된다.

### Singleton의 bean이 prototype의 bean을 참조하면 ?

- 프로토타입의 빈이 변경이 안된다. → 싱글톤 빈이 최초 인스턴스화 될 때, 처음으로 만들어졌던 프로토타입의 인스턴스만을 참조하기 때문
- 업데이트 하려면 ?
    - `@Scope*(value = "prototype", proxyMode = ScopedProxyMode.TARGET_CLASS)`  :  프로토타입 클래스의 선언부에 다음과 같이 설정한다.
       - 해당 클래스를 Proxy로 감싸라. → 싱글톤 객체가 프로토타입 객체를 접근할 때 proxy를 거쳐서 참조하도록
       - 기존 JVM에 있는 proxy는 interface 기준으로만 proxy를 만들어 주는 데, 이 방법을 사용하면 CGLib을 사용하여 class도 proxy가 되도록 만들어준다.
    - `ObjectProvider`
    - `Provider`




---
</br><br>


# 6. Environment 1부 프로파일(Profile)

ApplicationContext는 [EnvironmentCapable](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/env/EnvironmentCapable.html)이라는 인터페이스를 상속받는다.

getEnvironment() 함수로 컴포넌트와 연관된 [Environment](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/env/Environment.html) 를 리턴한다.

### Profile 이란 ?

- Bean들의 그룹
- [Environment](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/env/Environment.html)  의 역할은 Active할 Profile을 확인하고 설정한다.

### Profile UseCase

- Test 환경에서 A라는 Bean을 사용하지만, Release 환경에서는 B 라는 Bean을 사용하고 싶을 때
- 사용 환경에 따라 Bean의 등록 및 사용 유무를 설정하고 싶을 때

### Profile 정의하기

- Class에 정의
    - @Configuration @Profile(”test”)
    - @Component @Profile(”test”)
- 메소드에 정의
    - @Bean @Profile(”test”)
    

### Profile 설정하기

- VM Option에 설정 : -DSpring.profiles.active=”test”
- [@ActiveProfiles](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/test/context/ActiveProfiles.html) (테스트용)

### Profile 표현식

Profile 표현식에 논리연산자를 사용할 수 있다.

ex ) @Profile(”!prod”) → prod 환경이 아닐 때 bean 사용

- ! (not)
- & (and)
- |  (or)


---

# 7. Environment 2부. 프로퍼티(Property)

### Property 란?

- 개발 환경 구성에 필요한 설정값들, 다양한 방법으로 정의가 가능
- [Environment](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/env/Environment.html)  의 역할은 Property 소스 설정 및 Property 값 가져오기

### Property의 우선순위

- StandardServletEnvironment의 우선순위
    - ServeltConfig의 매개변수
    - ServletContext의 매개변수
    - JNDI (java.comp/env)
    - JVM 시스템 Property(-Dkey=”value”)
    - JVM 시스템 환경변수 (운영체제 환경변수)
    

### @PropertySource

- [Environment](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/env/Environment.html) 를 통해 Property를 추가하는 방법이다.
- 사용 방법 : `@PropertySource("classpath:/com/jhj/app.properties")`
    - 소스코드 상 Config 파일에 애노테이션을 추가
    - property 파일에 기재된 설정 값들은 `@Value(${...})` 형태로 주입받아 사용이 가능하다. → BeanPostProcessor에 의해서 처리되기 때문에BeanPostProcessor나 BeanFactoryPostProcessor 내에서는 사용할 수 없다.


---


# 8. MessageSource

국제화 기능(i18n) 기능을 제공하는 인터페이스 → 메세지를 다국화 하는 기능

- ApplicationContext는 MessageSource를 상속받는다.
- SpringBoot를 사용하면, 다른 설정 없이 [messages.properties](http://messages.properties) 파일로 메세지 다국화 기능 사용이 가능하다.
    - messages.properties
    - messages_ko_KR.preporties
    - messages_en_US.properties
    - Local.getDefault() : 운영체제의 기본으로 설정되니 국가의 값을 가져옴 (ref. [ttps://docs.oracle.com/javase/7/docs/api/java/util/Locale.html#getDefault](https://docs.oracle.com/javase/7/docs/api/java/util/Locale.html#getDefault))
        
        ```java
        messageSource.getMessage("greeting", new String[]{"jhjhj"}, Locale.getDefault());
        messageSource.getMessage("greeting", new String[]{"jhjhj"}, Locale.KOREA);
        messageSource.getMessage("greeting", new String[]{"jhjhj"}, Locale.US);
        ```
        

### Reloading 기능이 있는 메세지 소스

캐시 설정을 이용해서 메세지 reload가 가능하다.

```java
@Bean
    public MessageSource messageSource(){
        var messageSource =  new ReloadableResourceBundleMessageSource();
        messageSource.setBasename("classpath:/messages_ko_KR");
        messageSource.setDefaultEncoding("UTF-8");
        messageSource.setCacheSeconds(3);

        return  messageSource;
    }
```


---



# 9. ApplicationEventPublisher

ApplicationContext extend [ApplicationEventPublisher](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/ApplicationEventPublisher.html)

이벤트 프로그래임에 필요한 Interface를 제공한다. [Observer Pattern (옵저버 패턴)](https://ko.wikipedia.org/wiki/%EC%98%B5%EC%84%9C%EB%B2%84_%ED%8C%A8%ED%84%B4) 의 구현체이다.

### Event 만들기

- 이벤트로 만들 클래스는 ApplicationEvnet 클래스( *`*org.springframework.context.ApplicationEvent`)를 상속받는다.
- **Spring 4.2 버전 이후부터 이 클래스를 상속받지 않아도** 이벤트로 사용할 수 있다. → 이벤트 핸들러로 사용될 클래스를 빈으로 등록하고 핸들러 메소드에 `@EventListener` 애노테이션을 붙이면 된다.

### Event 발생 시키는 방법

```java
ApplicationEventPublisher.publishEvent(new Evnet);
```

### Event 처리하는 방법

1. ApplicationListener<이벤트> 로 구현한 클래스를 생성 후, Bean으로 등록한다.
2. Spring 4.2 버전부터는 `@EventListener` 애노테이션을 메소드에 사용해서 이벤트 리스너 메소드로 사용할 수 있다.
3. 기본적으로 동기 (synchronized) 이다. 비동기로 사용하고 싶다면 `@Async`  애노테이션을 클래스에 사용하면 된다.
4. 이벤트 발생 순서를 정하고 싶다면, 이벤트 리스너에 `@Order` 애노테이션을 사용하고, 우선순위에 따라 옵션값을 설정하면 된다.

### 스프링이 기본으로 제공하는 Event

- [ContextClosedEvent](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/event/ContextClosedEvent.html)
- [ContextRefreshedEvent](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/event/ContextRefreshedEvent.html)
- [ContextStartedEvent](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/event/ContextStartedEvent.html)
- [ContextStoppedEvent](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/event/ContextStoppedEvent.html)
- [RequestHandledEvent](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/context/support/RequestHandledEvent.html)



---



# 10. ResourceLoader

Resource를 읽어오는 기능을 제공하는 Interface 이다.

ApplicationContext가 ResourceLoader를 상속받는다.

- ApplicationContext extends [ResourceLoader](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/io/ResourceLoader.html)

### Resource 읽어오는 방법

- 파일 시스템에서 읽어오기
- classpath에서 읽어오기 (/target/classes가 root)
- URL 로 읽어오기
- 상대/절대 경로로 읽어오기

→ 자세한 방법은 공식 레퍼런스 참조




---
</br><br>

출처 : [[인프런 강의] 백기선 - 스프링 프레임워크 핵심기술](https://www.inflearn.com/course/spring-framework_core)
