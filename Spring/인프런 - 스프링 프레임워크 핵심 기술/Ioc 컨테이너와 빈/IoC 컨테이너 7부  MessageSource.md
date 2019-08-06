# MessageSource

MessageSource는 **국제화(i18n) 기능을 제공하는 인터페이스로, 사용자의 Locale에 따라 표출되는 언어를 변경하는 서비스**이다. Spring에서는 MessageSource 인터페이스를 통해 국제화(i18n) 기능을 지원한다.

>국제화란 사용자의 Locale에 따라 표출되는 언어를 변경하는 서비스로, i18n이라 불리는 이유는 internationalization 이 단어의 첫글자인 i 와 끝글자인 n 사이에 18개의 글자가 있다고해서 i18n 이라고 한다.

## 1. Spring Boot에서 MessageSource

Spring Boot를 사용한다면 별다른 설정 없이 messages.properties를 사용하여 국제화 기능을 사용할 수 있다. **메시지 설정파일 기본은 messages.properties이며, 특정 언어를 지원할 때는 messages&#95;[언어].properties 또는 messages&#95;[언어]&#95;[국가].properties 형식으로 추가**한다.

>Spring Boot에서 messages.properties를 자동으로 읽어올 수 있는 이유는 ResourceBundleMessageSource가 Bean으로 등록되어 있어 messages라는 리소스 번들(message.properties, message_ko_kr.properties 등...)을 읽어오기 때문이다.

## 2. 메세지를 다국화하는 방법

MessageSource 인터페이스를 상속받는 **ApplicationContext를 사용하거나 MessageSource 인터페이스를 사용**한다.


아래 예제는 messageSource로 부터 메세지를 가져와서 Locale에 따라 맞게 메세지를 보여주는 예제이다.
#### messages.properties
```
greeting=Hello {0} {1}
```

#### messages&#95;ko&#95;kr.properties
```
greeting=안녕 {0} {1}
```

#### AppRunner.java
```
@Component
public class AppRunner implements ApplicationRunner {
            
      @Autowired
      MessageSource messageSource;
            	
      @Override
      public void run(ApplicationArguments args) throws Exception {
          //Locale.getDefault()의 값이 ko_KR이기 때문에 messages_ko_kr.properties에 정의된 메세지가 출력된다.
          System.out.println(message.getMessage("greeting", new String[] {"지훈", "테스트"}, Locale.getDefault()));

          //Locale의 값을 영어로 변경하였다.
          Locale.setDefault(Locale.ENGLISH);
          //Locale.getDefault()의 값이 en이기 때문에 messages.properties에 정의된 메세지가 출력된다.
          System.out.println(message.getMessage("greeting", new String[] {"jihun", "test"}, Locale.getDefault()));
    }          
}
```

## 3. MessageSource reloading

MessageSource를 reloading 하는 방법은 **ReloadableResourceBundleMessageSource의 객체를 통해 message.properties 파일을 갱신하며 읽는 것**이다.

#### messages.properties
```
greeting=Hello {0} {1}
```

#### messages&#95;ko&#95;kr.properties
```
greeting=안녕 {0} {1}
```

#### AppRunner.java
reloading 되는 것을 확인하기 위해 messages를 계속 출력한다.

```
@Component
public class AppRunner implements ApplicationRunner {
            
    @Autowired
    MessageSource message;
            	
    @Override
    public void run(ApplicationArguments args) throws Exception {
        while(true) {
            //Locale.getDefault()의 값이 ko_KR이기 때문에 messages_ko_kr.properties에 정의된 메세지가 출력된다.
            System.out.println(message.getMessage("greeting", new String[] {"지훈", "테스트"}, Locale.getDefault()));

            //Locale의 값을 영어로 변경하였다.
            Locale.setDefault(Locale.ENGLISH);
            //Locale.getDefault()의 값이 en이기 때문에 messages.properties에 정의된 메세지가 출력된다.
            System.out.println(message.getMessage("greeting", new String[] {"jihun", "test"}, Locale.getDefault()));

         //1초후 Thread 실행
         Thread.sleep(1000l);
        }  
    }        
}
```

#### SpringDemoApplication.java
ReloadableResourceBundleMessageSource를 사용하여 classpath에 있는 messages를 3초마다 reloading 한다.

```          
@SpringBootApplication
public class SpringDemoApplication {
            
    public static void main(String[] args) {
        SpringApplication.run(SpringDemoApplication.class, args);
    }

    //Bean 이름은 messageSource가 되어야 한다!
    @Bean
    public MessageSource messageSource() {
        ReloadableResourceBundleMessageSource source=new ReloadableResourceBundleMessageSource();
        //classpath 기준으로 messages를 읽어옴
        source.setBasename("classpath:/messages");
        //3초마다 message 프로퍼티 파일 체크
        source.setCacheSeconds(3);

        return source;
    }       
}
```
