# 데이터 바인딩 추상화: Converter와 Formatter

## 1.데이터 바인딩이란?

- 기술적인 관점에서 데이터 바인딩
    - 기술적인 관점에서 데이터 바인딩이란 어떤 property의 값을 Target 객체에 저장하는 기능이다.
- 사용자 관점에서 데이터 바인딩
    - 사용자 관점에서 데이터 바인딩이란 사용자가 입력한 값을 애플리케이션 도메인 모델에 동적으로 변환해 넣어주는 기능이다. 

## 2. 데이터 바인딩이 필요한 이유
사용자가 입력한 값은 대부분 "문자열"인데, 그 값을 객체가 가지고 있는 int, long, Boolean, Date등 심지어 Event, Book 같은 도메인 타입으로 변환해서 넣어주는 기능이다.

## 3. Converter / Formatter 인터페이스
PropertyEditor은 스프링 3.0 이전에 데이터 바인딩을 위해 사용했던 인터페이스로, 상태정보를 저장하고 있어서 쓰레드 세이프 하지 않는다는 문제점을 가지고 있었다.

스프링 3.0 부터는 이러한 문제를 해결한 Converter와 Formatter 인터페이스를 제공한다.

Converter는 Source를 Target로 변환할 수 있는 일반적인 변환기 이며 Thread-safe 하다.

Formatter은 Object를 String으로 변환할 수 있으며, 문자열을 Locale에 따라 다국화하는 기능을 제공하기 때문에 Web Appllication에서는 Formatter를 주로 사용한다.

## 4. Converter 인터페이스 예제

Domain 클래스
```
public class Event {
    
   private Integer id;
    	
   private String title;
    	
   public Event(Integer id) {
    	this.id = id;
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
    	
    @Override
    public String toString() {
    	return "Event{"+
    			"id-" + id +
    			", title='" + title + '\'' +
    			'}';
    }
}
```

Event 클래스에 데이터 바인딩을 처리하기 위한 Converter이다.

Converter는 제네릭으로 두개의 인자, Source와 Target를 받아서 구현한다.
```
public class EventConverter {

    //문자를 객체로 변환
    //제네릭에 들어가 있는 것처럼 문자열을 객체로 변환해준다.
    public static class StringToEventConverter implements Converter<String, Event>{

        @Override
        public Event convert(String s) {
            return new Event(Integer.parseInt(s));
        }
    }

    //객체를 문자로 변환
    //제네릭에 들어가 있는 것처럼 객체를 문자열로 변환해준다.
    public static class EventToStringConverter implements Converter<Event, String>{

        @Override
        public String convert(Event event) {
            return event.getId().toString();
        }
    }
}
```

WebMvcConfigurer 인터페이스에 addConverter 메서드를 사용하여 EventConverter를 등록해줬다.

>Spring MVC에서는 WebMvcConfigurer의 addFormatters메서드를 통해 Converter를 등록해 줘야 모든 Controller에서 동작한다.
```
@Configuration
public class WebConfig implements WebMvcConfigurer {

    //ConverterRegistry의 addConverter를 사용해서 등록할 수도 있다.
    //FormatterRegistry는 ConverterterRegistry를 상속받기 때문에 Converter와 Formatters 둘 다 등록할 수 있다.
    @Override
    public void addFormatters(FormatterRegistry registry) {
        registry.addConverter(new EventConverter.StringToEventConverter());
    }
```

테스트 코드이며 DataBinder가 정상적으로 동작하여 통과하는 것을 알 수 있다.
```
@RunWith(SpringRunner.class)
@WebMvcTest
public class EventControllerTest {

    @Autowired
    MockMvc mockMvc;

    @Test
    public void getTest() throws Exception{
        mockMvc.perform(get("/event/1"))
                .andExpect(status().isOk())
                .andExpect(content().string("1"));
    }
}
```

## 4. Formatter 인터페이스 예제

Domain 클래스
```
public class Event {
    
   private Integer id;
    	
   private String title;
    	
   public Event(Integer id) {
    	this.id = id;
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
    	
    @Override
    public String toString() {
    	return "Event{"+
    			"id-" + id +
    			", title='" + title + '\'' +
    			'}';
    }
}
```

Event 클래스에 데이터 바인딩을 처리하기 위한 Formatter이다.

Formatter는 제네릭으로 한 개의 인자만을 받는데, 왜냐하면 Objcet와 String 간의 변환만 담당하기 때문이다.

```
//제네릭에 Formatter로 처리할 타입을 지정
public class EventFormatter implements Formatter<Event> {

    //문자를 객체로 변환
    //convert와 차이점은 Locale 정보를 기반으로 처리할 수 있다는 점이다.
    @Override
    public Event parse(String s, Locale locale) throws ParseException {
        return new Event(Integer.parseInt(s));
    }

    //객체를 문자로 변환
    @Override
    public String print(Event event, Locale locale) {
        return event.getId().toString();
    }
}
```

WebMvcConfigurer 인터페이스에 addFormatters 메서드를 사용하여 EventFormatter 등록해줬다.

>Spring MVC에서는 WebMvcConfigurer의 addFormatters메서드를 통해 Formatter를 등록해 줘야 모든 Controller에서 동작한다.

```
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addFormatters(FormatterRegistry registry) {
        registry.addFormatter(new EventFormatter());
    }
}
```

테스트 코드이며 DataBinder가 정상적으로 동작하여 통과하는 것을 알 수 있다.
```
@RunWith(SpringRunner.class)
@WebMvcTest
public class EventControllerTest {

    @Autowired
    MockMvc mockMvc;

    @Test
    public void getTest() throws Exception{
        mockMvc.perform(get("/event/1"))
                .andExpect(status().isOk())
                .andExpect(content().string("1"));
    }
}
```

## 5. ConversionService 인터페이스

ConversionService는 실제 데이터 변환이 일어나는 곳이며 ConversionService를 통해 데이터 바인딩이 된다.

ConversionService는 스프링 MVC, 스프링 XML 설정파일, SpEL에서 사용할 수 있다.

ConversionService의 구현체중 DefaultFormattingConversionService가 있는데, 가장 자주 사용되는 구현체로 FormatterRegistry 기능도 하고 ConversionService의 기능도 하며 ,여러 Converter/Formatter 등록 해주는 기능을 한다.

Converter는 ConverterRegistry에 등록해야 하고 Formatter는 FormatterRegistry에 등록해서 사용해야함
근데 FormatterRegistry는 ConverterRegistry를 상속받고 있기 때문에 FormatterRegistry에는 Converter도 등록할 수 있다.

![image](https://user-images.githubusercontent.com/31675104/56080619-429bc980-5e3e-11e9-80a3-94de0cce0bca.png)

>등록되어있는 Converter/Formatter를 보고 싶으면 conversionService를 인터페이스를 출력하면 된다.(부트에서만 테스트 해봄)

## 6. WebConversionService

Spring Boot에서 ConversionService 주입시 WebConversionService를 주입해 준다.

WebConversionService는 Spring Boot가 제공해 주는 클래스로 DefaultFormattingConversionService를 상속받기 때문에 더 많은 기능을 가지고 있다. 예를들어 Formatter와 Converter가 Bean으로 등록되어 있으면, ConversionService에 자동으로 등록해준다.(Spring MVC에서는 Formatter와 Converter를 수동으로 등록해줘야 한다.)




