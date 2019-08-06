## 1. 스프링 AOP 특징
1. Proxy 기반의 AOP 구현체이다.
2. 스프링 bean에만 AOP를 적용할 수 있다.
3. 모든 AOP 기능을 제공하는 것이 목적이 아니라, 스프링 IoC와 연동하여 엔터프라이즈 애플리케이션에서 가장 흔한 문제에 대한 해결책을 제공하는 것이 목적

## 2. Proxy Pattern
프록시 패턴의 목적은 접근 제어 또는 부가 기능을 추가하는 용도로 쓰인다.

![image](https://user-images.githubusercontent.com/31675104/57967394-3f45b180-7999-11e9-8bd2-0b5bb9e4a0be.png)

위의 그림은 프록시 패턴에 대한 그림이다.
Client는 인터페이스 타입을 통해 Proxy 객체를 사용하며, Proxy 객체는 참조하고 있는 Real Subject 객체를 감싸서 실제 Client의 요청을 처리한다.

## 3. Proxy Pattern 예제
Real Subject인 SimpleEvnetService 객체의 createEvent 메서드와 publishEvent 메서드의 실행시간을 측정하는 예제이다.

### interface Subject

```
public interface EventService {

    void createEvent();

    void publishEvent();

    void deleteEvent();
}
```

### Real Subject
EventService 인터페이스를 상속받아 createEvent, publishEvent, deleteEvent 메서드를 구현한 Real Subject이다.

```
@Service
public class SimpleEvnetService implements EventService {

    @Override
    public void createEvent() {
        try {
            Thread.sleep(1000);
        }catch (InterruptedException e){
            e.printStackTrace();
        }

        System.out.println("Created an event");
    }

    @Override
    public void publishEvent() {
        try {
            Thread.sleep(2000);
        }catch (InterruptedException e){
            e.printStackTrace();
        }
        System.out.println("published an event");
    }

    @Override
    public void deleteEvent(){
        System.out.println("Delete an event");
    }
}

```

### Proxy
Client에서 EventService타입의 Bean을 주입받을시 ProxySimpleEventService을 주입해주기 위해 @Primary를 사용하였다. Real Subject인 SimpleEvnetService클래스의 메서드들을 delegate(위임) 받아서 부가기능인 메서드 실행시간을 측정하는 코드를 추가하였다.

```
@Primary
@Service
public class ProxySimpleEventService implements EventService {

    
    @Autowired
    SimpleEvnetService simpleEvnetService;

    @Override
    public void createEvent() {
        long begin = System.currentTimeMillis();
        //delegate
        simpleEvnetService.createEvent();
        System.out.println(System.currentTimeMillis() - begin);
    }

    @Override
    public void publishEvent() {
        long begin = System.currentTimeMillis();
        //delegate
        simpleEvnetService.publishEvent();
        System.out.println(System.currentTimeMillis() - begin);
    }

    @Override
    public void deleteEvent() {
        //delegate
        simpleEvnetService.deleteEvent();
    }
}
```

### Client
EventService 타입의 bean을 주입받을시 @Primary가 붙어져 있는 ProxySimpleEventService를 주입받기 때문에 createEvent, publishEvent 메서드 실행시 부가기능인 메서드 실행시간이 정상적으로 출력된다.

```
@Component
public class AppRunner implements ApplicationRunner {

    @Autowired
    EventService eventService;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        eventService.createEvent();
        eventService.publishEvent();
        eventService.deleteEvent();
    }
}

```

### 3-1. Proxy Pattern 장단점

1. 장점
* Real Subject와 Client를 수정하지 않고 부가기능 추가가 가능하다.

2. 단점
* 매번 Proxy Class를 작성해야 한다.
* 여러 클래스 여러 메소드에 적용시 중복 코드가 발생한다.
    * 위의 코드 ProxySimpleEventService에서 볼 수 있듯이 Proxy Class에서 중복코드가 발생한다.
    * 만약 ProxySimpleEventService에 정의된 메서드 실행시간을 측정하는 부가기능이 다른 Proxy에서도 필요로 한다면 Proxy를 만들고 해당 부가 기능을 또 심어줘야 한다.
* 객체들 관계도가 복잡해 진다.

위의 Proxy Pattern의 단점을 보완하기 위해 등장한 것이 스프링 AOP이다.

## 5. 스프링 AOC
* 스프링 iOC 컨테이너가 제공하는 기반 시설과 Dynamic Proxy를 사용하여 여러 복잡한 문제를 해결할 수 있다.
    * Dynamic Proxy란 Application이 동작하고 있을때 어떤 객체의 Proxy 객체를 만드는 것이다.
* 스프링 IoC는 BeanPostProcessor 인터페이스와 해당 인터페이스를 상속받은 AbstractAutoProxyCreator 클래스를 사용해서 기존 Bean을 대체하는 Dynamic Proxy를 만들어 등록 시킨다.

* BeanPostProcessor
    * 어떤 bean이 등록이 되면 그 bean을 가공할 수 있는 라이프사이클을 제공하는 인터페이스이다.
* AbstractAutoProxyCreator implements BeanPostProcessor
    * Real Subject를 감싸는 Proxy Bean을 만들어서 해당 Bean을 등록해주는 역할을 하는 클래스이다.
    * 위의 코드에서 예를들면 SimpleEvnetService Bean이 Bean으로 등록이 되면 AbstractAutoProxyCreator라는 BeanPostProcessor로 SimpleEvnetService Bean을 감싸는 Proxy Bean을 만들어서 그 Bean을 SimpleEvnetService Bean 대신에 등록을 해준다.
