# ApplicationEventPublisher
옵저퍼 패턴(Observer pattern)의 구현체로 **이벤트 프로그래밍시 유용한 인터페이스**이다.

> 옵저버 패턴(observer pattern)은 객체의 상태 변화를 관찰하는 관찰자들, 즉 옵저버들의 목록을 객체에 등록하여 상태 변화가 있을 때마다 메서드 등을 통해 객체가 직접 목록의 각 옵저버에게 통지하도록 하는 디자인 패턴이다. 주로 분산 이벤트 핸들링 시스템을 구현하는 데 사용된다. 발행/구독 모델로 알려져 있기도 하다.

- 이벤트의 전체적인 구조는 아래와 같다.
    - 이벤트 등록 → 이벤트 발생  → 이벤트 받아서 처리하는 핸들러

## 1. 스프링 4.2 이전에서 구현방법
스프링 4.2 이전에는 **ApplicationEvent를 상속 받아 이벤트를 등록**하며, **ApplicationListener를 상속 받아 이벤트 핸들러를 구현**했다.

ApplicationEvent를 상속받아 이벤트를 만들었으며, 이벤트는 빈으로 생성하지 않는다

```
public class MyEvent extends ApplicationEvent {
                
    private int data;
                	
    public MyEvent(Object source, int data) {
        super(source);
        this.data=data;
    }
                
    public int getData() {
        return data;
    }
}
                
```

ApplicationEventPublisher를 상속 받아 이벤트를 발생 시키는 클래스 이다.

```   
@Component
public class AppRunner implements ApplicationRunner {
                
    @Autowired
    ApplicationEventPublisher publishEvent;
    
    //ApplicationContext도 ApplicationEventPublisher를 상속받기 때문에 사용할 수 있다.
    //ApplicationContext appilcationContext;
                
    @Override
    public void run(ApplicationArguments args) throws Exception {
        publishEvent.publishEvent(new MyEvent(this,100));       
    }
}
```

ApplicationListener를 상속 받아 이벤트를 처리 하는 이벤트 핸들러이다.

```
@Component
public class MyEventHandler implements ApplicationListener<MyEvent> {
            
    @Override
    public void onApplicationEvent(MyEvent event) {
        System.out.println("이벤트 받았다. 데이터는" + event.getData());
    }       
}
```

## 2. 스프링 4.2 이후에서 구현방법

스프링 4.2 부터 **ApplicationEvent를 상속받지 않아도 이벤트를 등록할 수 있으며**, ApplicationListener를 사용하지 않고 **@EventListener를 사용하여 이벤트 핸들러 구현**한다.


ApplicationEvent를 상속받지 않고 이벤트를 만들었으며, 이벤트는 빈으로 생성하지 않는다.

```
public class MyEvent{
                
    private int data;
                	
    private Object source;
                	
    public MyEvent(Object source, int data) {
        this.source=source;
        this.data=data;
    }
                
    public Object getSource() {
        return source;
    }
                
    public int getData() {
        return data;
    }
}
```

ApplicationEventPublisher를 상속 받아 이벤트를 발생 시키는 클래스 이다.

```
@Component
public class AppRunner implements ApplicationRunner {
                
    @Autowired
    ApplicationEventPublisher publishEvent;
        
    //ApplicationContext도 ApplicationEventPublisher를 상속받기 때문에 사용할 수 있다.
    //ApplicationContext appilcationContext;
                
    @Override
    public void run(ApplicationArguments args) throws Exception {
        publishEvent.publishEvent(new MyEvent(this,100));
    }
}
```

ApplicationListener 대신 @EventListener 어노테이션을 사용하여 이벤트를 처리 하는 이벤트 핸들러이다.

```
@Component
public class MyEventHandler  {
            
    @EventListener
    public void handle(MyEvent event) {
        System.out.println(Thread.currentThread().toString());
        System.out.println("이벤트 받았다. 데이터는" + event.getData());
    }        	
}
```   

## 3. 이벤트가 하나인데, 이벤트 핸들러가 1개 이상이면?
동시에 다른 쓰레드에서 실행해 주지않고 **순차적으로 실행**된다.

### 3-1. 이벤트 핸들러 1개 이상일때 

ApplicationEvent를 상속받지 않고 이벤트를 만들었으며, 이벤트는 빈으로 생성하지 않는다.

```
public class MyEvent{
                
    private int data;
                	
    private Object source;
                	
    public MyEvent(Object source, int data) {
        this.source=source;
        this.data=data;
    }
                
    public Object getSource() {
        return source;
    }
                
    public int getData() {
        return data;
    }
}
```

ApplicationEventPublisher를 상속 받아 이벤트를 발생 시키는 클래스 이다.

```
@Component
public class AppRunner implements ApplicationRunner {
                
    @Autowired
    ApplicationEventPublisher publishEvent;
        
    //ApplicationContext도 ApplicationEventPublisher를 상속받기 때문에 사용할 수 있다.
    //ApplicationContext appilcationContext;
                
    @Override
    public void run(ApplicationArguments args) throws Exception {
        publishEvent.publishEvent(new MyEvent(this,100)); 
    }
}
```

ApplicationListener 대신 @EventListener 어노테이션을 사용하여 이벤트를 처리 하는 이벤트 핸들러이다.

```    
@Component
public class MyEventHandler  {
            
    @EventListener
    public void handle(MyEvent event) {
        System.out.println(Thread.currentThread().toString());
        System.out.println("이벤트 받았다. 데이터는" + event.getData());
    }            	
}
```

ApplicationListener 대신 @EventListener 어노테이션을 사용하여 이벤트를 처리 하는 이벤트 핸들러이다.

```         
@Component
public class AnotherHandler{
            
    @EventListener
    public void handle(MyEvent event) {
        System.out.println(Thread.currentThread().toString());
        System.out.println("이벤트 받았다. 데이터는" + event.getData());
    }
}
```

## 4. 만약 이벤트 핸들러 순서를 정하고 싶다면?

@Order 어노테이션을 사용하여 순서를 지정해 줄 수 있다.
@Order 어노테이션 안에 HIGHEST_PRECEDENCE는 Integer.MIN_VALUE로 값이 낮을 수록 우선 순위를 점한다를 의미를 가지고 있다.


```
@Component
public class MyEventHandler  {
            
    @EventListener
    @Order(Ordered.HIGHEST_PRECEDENCE)
    public void handle(MyEvent event) {
        System.out.println(Thread.currentThread().toString());
        System.out.println("이벤트 받았다. 데이터는" + event.getData());
    }
}
```

HIGHEST_PRECEDENCE에 +1이기 때문에 MyEventHandler 클래스에 있는 handle 메소드 뒤에 실행된다.
```        
@Component
public class AnotherHandler{
            
    @EventListener
    @Order(Ordered.HIGHEST_PRECEDENCE + 1)
    public void handle(MyEvent event) {
        System.out.println(Thread.currentThread().toString());
        System.out.println("이벤트 받았다. 데이터는" + event.getData());
    }
}
```

## 5.만약 이벤트 핸들러를 비동기로 실행하고 싶다면?

@Async 어노테이션을 사용하여 비동기로 실행할 수 있다.
>메인 클래스에 @EnableAsync를 붙여줘야 비동기적으로 실행된다. 스프링 부트의 경우 @SpringBootApplication 어노테이션이 붙은 클래스이다.

ApplicationEvent를 상속받지 않고 이벤트를 만들었으며, 이벤트는 빈으로 생성하지 않는다.

```
public class MyEvent{
            
    private int data;
            	
    private Object source;
            	
    public MyEvent(Object source, int data) {
        this.source=source;
        this.data=data;
    }
            
    public Object getSource() {
        return source;
    }
            
    public int getData() {
        return data;
    }
}
```

ApplicationEventPublisher를 상속 받아 이벤트를 발생 시키는 클래스 이다.

```     
@Component
public class AppRunner implements ApplicationRunner {
            
    @Autowired
    ApplicationEventPublisher publishEvent;
    
    //ApplicationContext도 ApplicationEventPublisher를 상속받기 때문에 사용할 수 있다.
    //ApplicationContext appilcationContext;
            
    @Override
    public void run(ApplicationArguments args) throws Exception {
        publishEvent.publishEvent(new MyEvent(this,100)); 
    }
}
```

ApplicationListener 대신 @EventListener 어노테이션을 사용하여 이벤트를 처리 하는 이벤트 핸들러이다.

```     
@Component
public class MyEventHandler  {
            
    @EventListener
    @Async
    public void handle(MyEvent event) {
        System.out.println(Thread.currentThread().toString());
        System.out.println("이벤트 받았다. 데이터는" + event.getData());
    }       	
}
```

ApplicationListener 대신 @EventListener 어노테이션을 사용하여 이벤트를 처리 하는 이벤트 핸들러이다.

```    
@Component
public class AnotherHandler {
            
    @EventListener
    @Async
    public void handle(MyEvent event) {
        System.out.println(Thread.currentThread().toString());
        System.out.println("Another" + event.getData());
    }
}
```

메인클래스로 비동기로 실행하기 위해 @EnableAsync 어노테이션을 붙여 주었다.

```
@SpringBootApplication
@EnableAsync
public class SpringDemoApplication {
            
    public static void main(String[] args) {
        SpringApplication.run(SpringDemoApplication.class, args);
    } 
}
```

## 6. 스프링이 제공하는 기본 이벤트

#### 1. ContextRefreshedEvent
ApplicationContext를 초기화 하거나 refresh 했을 때 발생하는 이벤트 이다.

#### 2. ContextStartedEvent
ApplicationContext를 start()하여 라이프사이클 빈들이 시작 신호를 받은 시점에 발생하는 이벤트 이다.

#### 3. ContextStoppedEvent
ApplicationContext를 stop()하여 라이프사이클 빈들이 정지 신호를 받은 시점에 발생하는 이벤트 이다.

#### 4. ContextClosedEvent
ApplicationContext를 close()하여 싱글톤 빈이 소멸되는 시점에 발생하는 이벤트 이다.

#### 5. RequestHandledEvent
HTTP 요청을 처리했을때 발생하는 이벤트 이다.

## 7. 기본이벤트 예제

스프링이 제공하는 기본 이벤트를 처리하기 위한 핸들러 이다.

```
@Component
public class MyEventHandler  {
            	
    @EventListener
    public void handle(ContextRefreshedEvent event) {
        System.out.println(Thread.currentThread().toString());
        System.out.println("ContextRefreshedEvent");
    }
            	
    @EventListener
    public void handle(ContextClosedEvent event) {
        System.out.println(Thread.currentThread().toString());
        System.out.println("ContextClosedEvent");
    }
}
```
