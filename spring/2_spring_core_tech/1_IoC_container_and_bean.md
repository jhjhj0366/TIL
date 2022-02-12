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




---
</br><br>


