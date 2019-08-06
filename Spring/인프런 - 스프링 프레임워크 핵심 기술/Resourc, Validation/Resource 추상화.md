# Resource


## 1. Resource 추상화

java.net.URL 클래스를 org.springframework.core.io.Resource 클래스로 감싸서 **low level에 있는 resource 접근하는 기능을 추상화한 인터페이스** 이다.

- 추상화 시킨 이유 
	* java.net.URL 클래스에는 클래스패스 기준으로 리소스 읽어오는 기능 부재
	* ServletContext를 기준으로 상대 경로로 읽어오는 기능 부재
	* 새로운 핸들러를 등록하여 특별한 URL 접미사를 만들어 사용할 수는 있지만 구현이 복잡하고 편의성 메소드가 부족하다.

## 2. Resource 인터페이스 구현체

- UrlResource
    - URL을 기준으로 리소스를 읽어들인다.
    - prefix로 http, https, ftp, file, jar 지원한다.
- ClassPathResource
    - prefix로 classpath를 지원한다.
- FileSystemResource
    - 파일 시스템 경로 기준으로 읽는다.
- ServletContextResource
    - 웹 애플리케이션 루트에서 상대 경로로 리소스 찾는다.
    - 가장 많이 사용되는 구현체이다.

> Spring Boot에서 @Autowired 같은 어노테이션으로 ApplicationContext 인터페이스 의존 주입시 ServletContextResource를 통해 리소스를 읽는 AnnotationConfigServletWebServerApplicationContext 객체가 주입된다.

## 3. Resource 인터페이스 주요 메서드

- exists()
    - 해당 resource가 존재하는지, 존재하지 않는지 판단하는 메서드이다.
- isOpen()
    - 파일이 열려 있는지 판단하는 메서드이다.
- getDescription()
    - 전체 경로 포함한 파일 이름 또는 실제 URL을 가져오는 메서드이다.
- ClassPathXmlApplicationContext("xml 설정파일")
    - xml 설정파일 문자열은 resource 클래스에 의해 classpath에 있는 xml 설정 파일을 가져온다.
- FileSystemXmlApplicationContext("파일명")
    - 파일은 resource 클래스에 의해 파일 시스템 경로에 있는 파일을 가져온다.

> ClassPathXmlApplicationContext와 FileSystemXmlApplicationContext의 공통점은 resource 클래스에 getResource()를 사용해서 resource를 가져온다는 점이며 차이점은 ClassPathXmlApplicationContext은 classpath에 있는 것을 찾고, FileSystemXmlApplicationContext은 파일 시스템 경로 기준으로 해당 파일을 찾는다는 점이다.

## 4. Resource 읽어오기

- Resource의 타입은 location 문자열과 ApplicationContext의 타입에 따라 결정 된다.
- ApplicationContext 타입이 ClassPathXmlApplicationContext일 경우 Resource 타입은 ClassPathResource이다.
- ApplicationContext 타입이 FileSystemXmlApplicationContext일 경우 Resource 타입은 FileSystemResource이다.
- ApplicationContext 타입이 WebApplicationContext일 경우 Resource 타입은 ServletContextResource 이다.

## 5. Resource 타입 강제하기
ApplicationContext의 타입에 상관없이 리소스 타입을 강제하려면 java.net.URL의 접두어를 사용하면 된다. ApplicationContext의 타입을 지정하는 것 보다 java.net.URL의 접두어를 사용하는 것이 더 좋은데 그 이유는 접두어를 사용하면 명시적이며 어떤 Resource가 오는지 알기 쉽기 때문이다.

>- classpath 접두어
 	- classpath:me/hong/config.xml -> ClassPathResource 사용
>- file 접두어
   - file:///some/resource/path/config.xml -> FileSystemResource 사용

아래 코드에서는 getResource()메소드에 접두어를 붙이냐 안붙이냐에 따라 ApplicationContext의 타입이 변경되는 걸 알 수 있다.

```
@Component
public class AppRunner implements ApplicationRunner {
    
    @Autowired
    ApplicationContext resourceLoader;
    	
    @Override
    public void run(ApplicationArguments args) throws Exception {   
    	Resource resource = resourceLoader.getResource("classpath:test.txt");
    
    	//getResource에서 classpath를 붙여주면 Resource의 타입이 ClassPathResource가 되어 resource를 정상적으로 읽어올 수 있다.
    	//만약 위에 getResource에서 classpath를 붙여주지 않으면 Resource의 타입이 ServletContextResource가 되어 resource를 정상적으로 읽어올 수 없다.
    	System.out.println(resource.getClass());
    	System.out.println(resource.exists());
    	System.out.println(resource.getDescription());
    	System.out.println(Files.readString(Path.of(resource.getURI())));
    }
}
```