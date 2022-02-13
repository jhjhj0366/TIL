# 1. Resource 추상화

[org.springframework.core.io.Resource](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/io/Resource.html)

### 특징

- `java.net.URL`을 추상화했다. → java.net.URL을 Spring core io Resource로 감싼 것

### 추상화 한 이유는 ?

- 기존 `java.net.URL` 에는 classpath를 기준으로 리소스를 읽어오는 기능이 없었다.
- 기존 `java.net.URL` 에는 ServletContext를 기분으로 상대 경로를 읽어오는 기능이 없었다.

### 구현체

- [AbstractFileResolvingResource](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/io/AbstractFileResolvingResource.html)
- [AbstractResource](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/io/AbstractResource.html)
- [ByteArrayResource](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/io/ByteArrayResource.html)
- [ClassPathResource](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/io/ClassPathResource.html) : 지원하는 prefix : classpath
- [DefaultResourceLoader.ClassPathContextResource](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/io/DefaultResourceLoader.ClassPathContextResource.html)
- [DescriptiveResource](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/io/DescriptiveResource.html)
- [FileSystemResource](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/io/FileSystemResource.html)
- [FileUrlResource](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/io/FileUrlResource.html)
- [InputStreamResource](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/io/InputStreamResource.html)
- [PathResource](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/io/PathResource.html)
- [ServletContextResource](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/context/support/ServletContextResource.html) : 웹 애플리케이션 루트에서 상대 경로로 리소스를 찾는다. (가장 많이 사용)
- [TransformedResource](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/reactive/resource/TransformedResource.html)
- [TransformedResource](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/resource/TransformedResource.html)
- [UrlResource](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/io/UrlResource.html)
- [VfsResource](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/core/io/VfsResource.html)

### 소스 읽어오기

Resource의 타입은 location 문자열과 ApplicationContext 타입에 따라 결정된다.

- 예시
    - ClassPathXmlApplicationContext -> ClassPathResource
    - FileSystemXmlApplicationContext -> FileSystemResource
    - WebApplicationContext -> ServletContextResource

- 리소스 로더에 `prefix classpath:/` 가 있을 때
    - 리소스 로더의 타입은 `AnnotationConfigServletWebServerApplicationContext`
    - 해당 리소스 타입은 `ClassPathResource`
        
        ```java
        @Autowired
            ApplicationContext resourceLoader;
        
            @Override
            public void run(ApplicationArguments args) throws Exception {
        				// 리소스 로더 타입은 class org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext
                System.out.println("resourceLoader.getClass() : " + resourceLoader.getClass());
        
                Resource resource = resourceLoader.getResource("classpath:/test.txt");
                // 리소스 타입은 class org.springframework.core.io.ClassPathResource
        				System.out.println("resource.getClass() = " + resource.getClass());
            }
        ```
        
    
- 리소스 로더를 prefix 없이 받을 때
    - 리소스 로더의 타입은 `AnnotationConfigServletWebServerApplicationContext`
    - 해당 리소스 타입은 `ServletContextResource` → 디폴트로 ServletContextResource로 받는다.
    
    ```java
    @Autowired
        ApplicationContext resourceLoader;
    
        @Override
        public void run(ApplicationArguments args) throws Exception {
    				// 리소스 로더 타입은 class org.springframework.boot.web.servlet.context.AnnotationConfigServletWebServerApplicationContext
            System.out.println("resourceLoader.getClass() : " + resourceLoader.getClass());
    
            Resource resource = resourceLoader.getResource("test.txt");
            // 리소스 타입은 class org.springframework.core.io.ServletContextResource
    				System.out.println("resource.getClass() = " + resource.getClass());
        }
    ```
    
- 따라서 Resource의 경로를 작성할 때는 Prefix를 사용하는 것을 권장한다. (명시적이고 가시성 좋음)

📕 [https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#resources-as-dependencies](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#resources-as-dependencies)

</br><br>
 ---


# 2. Validation 추상화

[org.springframework.validation.Validator](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/validation/Validator.html)

객체를 검증하기 위해 사용하는 인터페이스 이다.

애노테이션을 사용하여 객체를 검증한다.

### 인터페이스

- `boolean* supports(Class<?> clazz)` : 어떤 객체를 검증할 것인지 정의
- `void validate(Object target, Errors errors)` : 실제 검증 로직을 구현
    - ValidationUtils 사용하면 편함
    
- Springboot 2.0.5 이상 버전
    - LocalValidatorFactoryBean 으로 자동 등록
    - 참고
        - [https://beanvalidation.org/](https://beanvalidation.org/)
        - [https://docs.jboss.org/hibernate/beanvalidation/spec/1.1/api/](https://docs.jboss.org/hibernate/beanvalidation/spec/1.1/api/)
        
 </br><br>       
 ---


출처 : [[인프런 강의] 백기선 - 스프링 프레임워크 핵심기술](https://www.inflearn.com/course/spring-framework_core)
