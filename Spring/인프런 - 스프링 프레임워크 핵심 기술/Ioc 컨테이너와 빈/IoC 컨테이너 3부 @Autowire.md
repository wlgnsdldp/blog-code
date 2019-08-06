# @Autowire

스프링 IoC 컨테이너에 담겨있는 **Bean의 타입(객체 타입)이 일치하는 것을 찾아서 일치하는 Bean(객체)을 자동으로 주입해주는 어노테이션** 이다.

## 1. @Autowired를 사용할 수 있는 위치

@Autowired는 **생성자, setter, field**에서 사용가능 하다.

### 생성자
```
public class BookService {

	private BookRepository bookRepositiry;
        	
    //생성자
    @Autowired
    public BookService(BookRepository bookRepositiry) {
    	this.bookRepositiry=bookRepositiry;
     }
}
```

### setter
```
public class BookService {
        	
	private BookRepository bookRepositiry;
        	
   //setter
   @Autowired
   public void setBookRepositiry(BookRepository bookRepositiry) {
   		this.bookRepositiry = bookRepositiry;
   }
}
```

### field
```
public class BookService {
        	
   //field
   @Autowired
   private BookRepository bookRepositiry;
}
```

## 2. @Autowired의 required 속성

기본적으로 @Autowired 사용시 스프링 IoC 컨테이너에 **해당 타입의 Bean 객체가 존재하지 않거나 또는 해당 타입의 Bean 객체가 2개 이상 존재시 예외를 발생 시키며, 애플리케이션 구동이 실패**된다.

만약 위와 같은 경우가 발생해도 예외를 발생시키지 않고 애플리케이션을 구동할려면 @Autowired의 required 속성을 사용하면 된다. required 속성은 Boolean이며 기본값은 true이다. required 속성의 값이 false 일시 해당 Bean을 주입받지 못해도 예외가 발생되지 않고, 애플리케이션은 구동된다.

> 생성자에 @Autowired 사용시 required 속성은 무의미하다. 왜냐하면 생성자는 객체 생성시 필수 요소인데, 필요한 의존성을 주입받지 못하면 객체 생성을 할 수 없기 때문이다.

```
public class BookService {
    	
   //field
   @Autowired(required=true)
   private BookRepository bookRepositiry;
    	
   //생성자
   @Autowired(required=false)
   public BookService(BookRepository bookRepositiry) {
    	this.bookRepositiry=bookRepositiry;
   }
    
   //setter
   @Autowired(required=false)
   public void setBookRepositiry(BookRepository bookRepositiry) {
    	this.bookRepositiry = bookRepositiry;
   }
```

## 3. Bean을 주입받는 경우의 수

1. 해당 타입의 Bean이 없는 경우
    * required 속성이 true이면 예외 처리되어, 애플리케이션 구동이 실패된다.
    * required 속성이 false이면 예외 처리 되지않아, 애플리케이션이 구동된다.

2. 해당 타입의 Bean이 한 개인 경우
    * 정상적으로 빈이 주입된다.

3. 해당 타입의 Bean이 여러개인 경우
    * 컨테이너는 어느 Bean을 주입해줘야 할지 모르기 때문에 예외를 발생시킨다. 이러한 문제를 해결하기 위해서는 어떤 Bean을 주입받을지 정해줘야 하는데 **@Primary, @Qualifier 어노테이션을 사용하여 주입받을 Bean을 지정해 줄 수 있다.**

## 4. @Primary와 @Qualifier

### @Primary
@Componet와 @Bean 어노테이션이 사용된 곳에 붙일 수 있는 어노테이션으로, 동일한 타입의 Bean이 2개 이상일시 **@Primary가 붙여진 Bean을 주입**받는다.

Ioc 컨테이너에 BookRepository 타입의 Bean이 2개 이상일시 @Primary 어노테이션이 붙은 아래 빈을 주입 받는다.

```
@Component
@Primary
public class BookRepository{}
```

### @Qualifier
@Autowired 어노테이션을 통해 의존 주입 받는 대상에 붙이는 어노테이션으로,  **동일한 타입의 Bean이 2개 이상일시 Bean 객체 이름이 일치하는 것을 주입** 받는다.

Bean의 타입이 BookRepository 이고, Bean의 이름이 BookRepository 인 것을 주입 받는다.

```
public class BookService{
   @Autowired
   @Qualifier("BookRepository")
   private BookRepository bookRepository;
}
```
