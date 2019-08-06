# Environment - profile

## 1. Environment란?

**profile과 property를 다루는 인터페이스** 이며, Environment를 통해 활성화할 프로파일을 확인 및 설정할 수도 있고, Application에 등록되는 key:value 쌍으로 제공되는 property에 접근할 수도 있다.

## 2. Profile이란?

일반적인 Profile은 개발(dev), 테스트(test), 운영(prod) 등으로 구동환경을 세분화하여 서비스시, 이런 식별 키워드를 뜻한다.

Spring에서 profile은 빈들의 묶음이며, **환경에 따라 다른 빈들을 사용해야 하는 경우에 사용**하며, 특정 Profile에 Bean을 등록하지 않는 이상 Default Profile에 저장된다.

## 3. ApplicationContext extends EnvironmentCapable

ApplicationContext는 EnvironmentCapable 인터페이스를 상속받으며, 해당 인터페이스에 있는 **getEnvironment() 메소드를 사용하여 profile을 가져올 수 있다.**

아래 코드에서는 profile을 가져와서 현재 활성화 된 profile과 default profile을 출력한다.

```    
@Component
public class AppRunner implements ApplicationRunner {
    
   @Autowired
   ApplicationContext ctx;
    	
   @Override
   public void run(ApplicationArguments args) throws Exception {
    	Environment env=ctx.getEnvironment();
    	System.out.println(Arrays.toString(env.getActiveProfiles()));
    	System.out.println(Arrays.toString(env.getDefaultProfiles()));
    	}
    }
```

## 4. Profile 정의하기

Profile은 **클래스와 메소드**에 정의할 수 있다.

### 4-1. 클래스에 정의하는 방법

test 프로파일 일시 TestConfiguration 클래스의 bookRepository와 bookService가 Bean으로 등록된다.

```
@Component
@Profile("test")
public class TestConfiguration{
    
    @Bean
    public BookRepository bookRepository(){
    	return new bookRepository();
    }

    @Bean
    public BookService bookService(){
        return new bookService();
    }
}
```

### 4-2. 메소드에 정의

test 프로파일 일시 TestConfiguration 클래스의 bookRepository만 Bean으로 등록된다.
메소드에 정의하는 것이 클래스에 정의하는 방법보다 더 세부적으로 설정할 수 있다.

```
@Component
public class TestConfiguration{
    
    @Bean
    @Profile("test")
    public BookRpository bookRepository(){
    	return new bookRepository();
    }

    @Bean
    public BookService bookService(){
        return new bookService();
    }
}
```

## 5. Profile 활성화

 ```-Dspring.profiles.acvtive=”프로파일명”```을 사용하여 **Profile을 활성**화할 수 있다.

아래 그림에은 이클립스에서 test Profile을 활성화 한 것이다.
![untitled-7067e619-cdd7-463e-801d-b9090b1baffa](https://user-images.githubusercontent.com/31675104/53162844-3b132d80-3610-11e9-8515-c79b4670dbc9.png)


## 6. Profile Expression

Profile Expression을 사용하여 프로파일을 유연하게 사용할 수 있다.

- ! (not)
- & (and)
- | (or)

profile이 prod가 아닐때 bookRepository를 Bean으로 등록한다.

```
    @Component  
    @Profile("!prod")
    public class TestConfiguration{
    	@Bean
    	public BookRpository bookRepository(){
    		return nuw TestBookRepository();
    	}
    }
```
