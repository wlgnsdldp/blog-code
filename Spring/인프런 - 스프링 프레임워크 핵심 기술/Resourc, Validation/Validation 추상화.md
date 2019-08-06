# Validation 추상화


## 1. Validation 인터페이스란?
에플리케이션에서 사용하는 객체들을 검증할때 사용하는 인터페이스로 주로 스프링MVC에서 사용하지만 모든 계층(웹, 서비스, 데이터)에서 사용할 수 있다.

>JSR-303(Bean Validation 1.0)과 JSR-349(Bean Validation 1.1)와 JSR-380(Bean Validation 2.0) 을 지원한다.

## 2. Validation 인터페이스
Validation 인터페이스 사용시 supports(Class clazz) 메서드와 validate(Object target, Errors errors)메서드를 구현해야 한다.

- boolean supports(Class clazz)
	- 검증해야하는 인스턴스의 클래스가 Validation가 검증할 수 있는지 클래스 인지 확인하는 메소드
- void validate(Object target, Errors errors)
	- 실제 검증 로직을 이 안에서 구현하며, ValidationUtils를 사용하여 편리하게 구현할 수 있다.

아래 클래스에서 title은 null이 되면 안되는 조건이다.

```    
public class Event {

        Integer id;
        String title;
            
        public Integer getId() {
             return id;
        }
            
        public void setId(Integer id) {
             this.id = id;
        }
            
        public String getTitle() {
             return title;
        }
            
        public void setTitle(String title) {
             this.title = title;
        }
}
```
   
Event 클래스를 Validator 하기 위한 클래스이다.

```
public class EventValidator implements Validator {
            
   //인자로 넘어온 클래스가 Event 타입인지 검증한다.
   @Override
   public boolean supports(Class<?> clazz) {
         return Event.class.equals(clazz);
   }
            
   @Override
   public void validate(Object target, Errors errors) {
   		//ValidationUtils의 rejectIfEmptyOrWhitespace 메서드를 사용하여 객체의 title 필드가 비어있거나 공백일 경우에는 errors에 에러 정보를 담는다.
  		//notempty는 에러 코드, Empty title is now allowed는 디폴트 메세지로 error 코드로 메세지를 찾지못했을때 사용한다.
         ValidationUtils.rejectIfEmptyOrWhitespace(errors, "title", "notempty", "Empty title is now allowed");
    }
}
```

검증을 테스트 하기위한 Runner이다..

```            
@Component
public class AppRunner implements ApplicationRunner {
            	
   @Override
   public void run(ApplicationArguments args) throws Exception {
            		
    	Event event = new Event();
            
       EventValidator eventValidator = new EventValidator();
            		
       //첫 번쨰 매개변수 : 어떤 타켓을 검사할 것인가
       //두 번쨰 매개변수 : 어떤 이름인가?
       //BeanPropertyBindingResult는 Errors의 구현체로, event 객체에 에러가 있을 경우 이 객체에 정보가 담기게 된다.
       //실제로 BeanPropertyBindingResult는 자주 볼일이 없다. 왜냐하면 스프링MVC가 자동으로 생성해서 넘겨주기 때문이다.
       Errors errors = new BeanPropertyBindingResult(event, "event");
            		
       //event 객체 검사, 검증 애러 담아줌
       eventValidator.validate(event, errors);
            		
       //에러가 있는지 화인
       System.out.println(errors.hasErrors());
            		
       //모든 에러 코드 출력
       errors.getAllErrors().forEach(e ->{
       	System.out.println("===== error code =====");
          Arrays.stream(e.getCodes()).forEach(System.out::println);
          System.out.println(e.getDefaultMessage());
         });
        }
}
```

## 3.Bean Validation을 이용한 검증 로직 구현

- 스프링 부트 2.0.5 이상 버전을 사용시에는 Validator 중 스프링이 제공해 주는 LocalValidatorFactoryBean 빈이 자동 등록 된다.
- LocalValidatorFactoryBean 빈은 Bean Validation 어노테이션을 지원하는 Validator 이다.
- JSR-380(Bean Validation 2.0.1)은 구현체로 hibernate-validator 사용한다.
- 어노테이션 으로 검증할 수 있을 만한 것은 Validator 없이도 충분히 검증할 수 있다.


        - @NotEmpty, @Min, @Email 등을 사용하여 Bean Validation을 체크하여 검증한다.
```
public class Event {
        
      Integer id;
        		
      @NotEmpty
      String title;
        
      @NotNull @Min(0)
      Integer limit;
        		
      @Email
      String email;
        		
      public Integer getLimit() {
      		return limit;
      }
        
      public void setLimit(Integer limit) {
        	this.limit = limit;
      }
        
      public String getEmail() {
        	return email;
      }
        
      public void setEmail(String email) {
        	this.email = email;
      }
        
      public Integer getId() {
        	return id;
      }
        
      public void setId(Integer id) {
        	this.id = id;
      }
        
      public String getTitle() {
        	return title;
      }
        
      public void setTitle(String title) {
        	this.title = title;
        }
}
```

검증을 테스트 하기위한 Runner이다.

* Validator을 의존 주입 받으면 LocalValidatorFactoryBean이 의존 주입된다.
* Event에 limt와 email에 이상한 값을 넣어서 강제로 오류를 발생시켰다.

```
@Component
public class AppRunner implements ApplicationRunner {
        
     @Autowired
     Validator validator;
        	
     @Override
     public void run(ApplicationArguments args) throws Exception {
     		System.out.println(validator.getClass());
        		
        	Event event = new Event();
        	event.setLimit(-1);
        	event.setEmail("aaa");
        	Errors errors = new BeanPropertyBindingResult(event, "event");
        		
        	//event 객체 검사, 검증 애러 담아줌
        	validator.validate(event, errors);
        		
        	//에러가 있는지 화인
        	System.out.println(errors.hasErrors());
        		
        	//모든 에러 코드 출력
        	errors.getAllErrors().forEach(e ->{
        		System.out.println("===== error code =====");
        		Arrays.stream(e.getCodes()).forEach(System.out::println);
        		System.out.println(e.getDefaultMessage());
        	});
     }
}
```