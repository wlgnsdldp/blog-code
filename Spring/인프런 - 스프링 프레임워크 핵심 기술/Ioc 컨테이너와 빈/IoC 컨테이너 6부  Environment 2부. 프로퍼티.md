# Environment - property

## 1. Environment란?

**profile과 property를 다루는 인터페이스** 이며, Environment를 통해 활성화할 프로파일을 확인 및 설정할 수도 있고, Application에 등록되는 key:value 쌍으로 제공되는 property에 접근할 수도 있다.

## 2. property란?

모든 형태의 key:value 쌍을 말한다. .properties 파일에 정의되어 있을 수도 있고, 환경 변수에 저장되어 있을 수도 있고, 커맨드 라인 인자로 전달되는 값일 수도 있다.

**즉, 여러 형태로 제공 되는 key:value**를 의미한다. 

## 3. property우선순위

Environment를 통해 property에 접근시 계층형으로 접근한다. 계층형으로 접근한다는 말은 **우선순위가 있다는 뜻**이다.

- StandardServletEnvironment의 우선 순위
    1. ServletConfig 매개변수
    2. ServletContext 매개변수
    3. JNDI(java:comp/env/)
    4. JVM 시스템 프로퍼티(-Dkey="value")
    5. JVM 시스템 환경 변수(운영 체제 환경 변수)

## 4. JVM 시스템 프로퍼티값 가져오기

![untitled-504fd03a-db7d-4970-9be0-af245b29c7b1](https://user-images.githubusercontent.com/31675104/53163114-bc6ac000-3610-11e9-894c-5410c102aeb5.png)

JVM 시스템 프로퍼티에 정의한 app.name값인 test를 가져온다.

```
@Component
public class AppRunner implements ApplicationRunner {
    
   @Autowired
   ApplicationContext ctx;
    	
   @Override
   public void run(ApplicationArguments args) throws Exception {
  		Environment environment=ctx.getEnvironment();
  		System.out.println(environment.getProperty("app.name"));
    	}
    }
```        

## 5. @PropertySource

@PropertySource를 사용하여 프로퍼티 파일을 읽어와 Environment를 사용하여 가져올 수 있다.
> @PropertySource는 @Configuration이 정의된 곳에서 사용할 수 있다.

#### app.properties 

```
app.name="spring"
```

#### AppContext.java
app.properties를 읽어와서 Environment를 객체를 사용해 해당 property를 읽어오는 코드이다. 

```
@Configuration
@PropertySource("/app.properties")
public class AppContext{
    
    @Autowired
    ApplicationContext ctx;
        	
    Environment environment=ctx.getEnvironment();
    System.out.println(environment.getProperty("app.name"))
    }
```

