# 데이터 바인딩 추상화: PropertyEditor

## 1.데이터 바인딩이란?

- 기술적인 관점에서 데이터 바인딩
    - 기술적인 관점에서 데이터 바인딩이란 어떤 property의 값을 Target 객체에 저장하는 기능이다.
- 사용자 관점에서 데이터 바인딩
    - 사용자 관점에서 데이터 바인딩이란 사용자가 입력한 값을 애플리케이션 도메인 모델에 동적으로 변환해 넣어주는 기능이다. 

## 2. 데이터 바인딩이 필요한 이유
사용자가 입력한 값은 대부분 "문자열"인데, 그 값을 객체가 가지고 있는 int, long, Boolean, Date등 심지어 Event, Book 같은 도메인 타입으로 변환해서 넣어주는 기능이다

## 3. PropertyEditor

스프링 3.0 이전에는 DataBinder가 변환 작업을 위해 사용하던 인터페이스는 PropertyEditor 이다.

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

Event 클래스에 데이터 바인딩을 처리하기 위한 클래스이다.

PropertyEditor를 상속 받으면 구현해야할 메소드가 많기 때문에 PropertyEditor를 상속받은 PropertyEditorSupport를 상속받아 사용하면 구현해야할 메소드만 선택해서 사용할 수 있다.

```
public class EventEditor extends PropertyEditorSupport {
    
    @Override
    public String getAsText() {
        //PropertyEditor가 받은 객체를 getValue()로 가져올 수 있다. (getValue()는 Object 타입)
        Event event = (Event)getValue();
        return event.getId().toString();
    }

    //String을 Event로 변환하기 위한 메서드
    @Override
    public void setAsText(String text) throws IllegalArgumentException {
        //문자열로 들어오는 Event의 id 값을 숫자로 변경하여 Event 생성자에 전달해주고 setValue 메서드에 넣어준다.
		//만약 /event/1 이라는 요청이 들어오면 1이라는 문자열을 Integer로 변환해서 Event 객체 생성자에 전달해 준다.
       setValue(new Event(Integer.parseInt(text)));
    }
}
```

@InitBinder를 사용해서 Controller에서 사용할 바인더를 등록할 수 있다.(사용할 바인더를 전역적으로 등록하는 방법도 있다.)

```
@RestController
public class EventController {

    //DataBinder의 구현체 중 하나인 WebDataBinder에 PropertyEditor를 등록
    //Controller가 요청을 처리하기 전에 Controller에서 정의한 DataBiner에 들어있는 PropertyEditor를 사용한다.
    @InitBinder
    public void init(WebDataBinder webDataBinder){
        webDataBinder.registerCustomEditor(Event.class, new EventEditor());
    }

    @GetMapping("/event/{event}")
    public String getEvent(@PathVariable Event event){
        System.out.println(event);
        return event.getId().toString();
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

## 4. PropertyEditor사용시 유의사항

PropertyEditor는 절대 Singleton Bean으로 등록해서 사용하면 안된다.

getValue(), setValue()에서 value는 PropertyEditor가 가지고 있는 값이다.

이 값들이 서로 다른 쓰레드에게 공유가 되기 때문에 statefull 하며, 그렇기 때문에 쓰레드-세이프 하지 않다.

쓰레드-세이프 하지 않기 떄문에 PropertyEditor의 는 여러 쓰레드에 공유해서 쓰면 절대 안된다.

예를들어 Singleton Bean으로 등록해서 사용하게 되면 1번 회원이 2번회원 정보를 변경하고 2번회원이 5번회원의 정보를 변경하는 문제가 생길 수 있다.

만약 Bean으로 등록한다면 Scope를 Thread Scope를 지정하면 그나마 괜찮긴 하지만 그래도 불안하기 때문에 Bean으로 등록하지 않는 것이 좋다.

PropertyEditor사용시는 구현도 편리하지 않고 Object와 String간의 변환만 가능하고 쓰레드-세이프 하지 않는다는 단점을 보완하기 위해 스프링 3.0 부터는 데이터 바인딩과 관련된 인터페이스와 기능인 Converter와 Formatter이 추가되었다.