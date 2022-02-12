# 1. 스프링 IoC 컨테이너와 빈

### Inversion Of Control (제어의 역전)
    
   - 객체에 대한 제어 흐름을 직접 제어하는 게 아니라, 외부에서 관리하는 것 
    </br> → 스프링 IoC 컨테이너를 사용 가능 (꼭 Spring 에서만 사용되는 것이 아니라, 일반 POJO에서도 코드로 구현 가능하다.)
    

### Dependency Injection (의존 관계 주입)
    
   - 어떤 객체가 사용하는 의존 객체를 개발자가 직접 코드로 구현하는 것이 아니라, 외부에서 주입받아 사용하는 방법을 말한다. 
   </br> 
    
### Spring IoC 컨테이너
   - [BeanFactory](https://docs.spring.io/spring-framework/docs/5.0.8.RELEASE/javadoc-api/org/springframework/beans/factory/BeanFactory.html) : 스프링 컨테이너 가장 최상단의 인터페이스 (reference docs 참고)
   - Bean 설정 소스부터 Bean의 정의를 읽어들여 Bean을 구성하고 제공한다.
   - 
    </br> 
    
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
