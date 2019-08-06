## 1. 의존성 추가

aop를 사용하기 위해서는 의존성 추가가 필요하다.
> 아래 의존성은 Spring Boot용 이다.

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

## 2. Aspect 정의

Aspect 정의시 @Aspect를 사용하여 해당 클래스가 Aspect class 임을 알려주며, @Component를 사용하여 Bean으로 등록한다.

```
@Component
@Aspect
public class PerfAspect {}
```

## 3. PointCut 정의

Aspect에는 Adivce와 PonitCut이 필요하다.
PointCut을 정의하는 방법으로는 @Around, @Before, @AfterReturning, @AfterThrowing와 표현식을 사용해서 정의할 수 있다.

### 3-1. @Around
Target 메서드를 감싸는 형태로 적용이 되기 때문에 Target 메서드 호출 전, Target 메서드 호출 이후, Target 메서드 호출시 에러 발생 시 등 여러 Join point를 사용할 수 있다. @Around는 광범위하기 때문에 만약 특정 Join point 필요시에는 @Befor나, @AfterReturning, @AfterThrowing를 사용하는 것이 좋다.

### 3-2. @Before
Target 메서드가 호출되기 전에 Advice를 실행한다.

### 3-3. @AfterReturning
Target 메서드가 return 후 Advice를 실행한다.

### 3-4. @AfterThrowing
Target가 Exception을 던지면 Advice를 실행한다.

### 3-5. 주요 표현식

1. execution(value)
- value에 지정된 곳에 Advice를 적용한다.

2. @annotation(annotation)
- 해당 annotation이 사용된 곳에 Advice를 적용한다.

3. bean
- 해당 bean에 있는 모든 public 메서드에 Advice를 적용한다.

### 3-6. 포인트컷 조합
* &&, ||, !

## 4. 스프링 AOP 예제

Aspect에는 아래 2가지가 필요하다.
1. Advice - Advice는 해야하는 일, 즉 실질적인 부가기능을 담은 구현체이다.
2. PointCut - Advice가 구체적으로 어디에 적용되는지에 대한 것이다.

```
public interface EventService {

    void createEvent();

    void publishEvent();

    void deleteEvent();
}
```

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

@Around는 PointCut의 역할을 하며, logPert는 Advice의 역할을 한다. @Around는 Target 메서드를 감싸는 형태로 적용이 되기 때문에 ProceedingJoinPoint의 pjp.proceed()메서드를 사용해서 Target 메서드를 호출한다.

PointCut의 표현식으로 execution(* com.example..*.EventService.*(..))을 사용하였는데, 해당 표현식의 뜻은 com.example 패키지 밑에 있는 모든 클래스 에서 EventService 클래스에 있는 모든 메서드에 해당 Advice를 적용하라는 뜻이다.

```
@Component
@Aspect
public class PerfAspect {

    //com.example 패키지 밑에 있는 EventService 클래스에 메서드 실행시간을 측정
    @Around("execution(* com.example..*.EventService.*(..))")
    public Object logPert(ProceedingJoinPoint pjp) throws Throwable{
        long begin = System.currentTimeMillis(); //메서드 실행시간
        Object retval = pjp.proceed(); //Target 메서드 호출
        System.out.println(System.currentTimeMillis() - begin); //메서드 종료시간
        return retval;
    }
}

```

코드를 테스트 하기 위한 Runner 이며, 실행시 com.example 패키지 밑에 있는 EventService 클래스의 실행시간에 정상적으로 측정되는 것을 알수 있다.

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

위의 코드에서 문제점은 Logging이 필요없는 deleteEvent 메서드까지 적용이 된다는 점이다.
이 문제를 해결할 수 있는 방법으로는 표현식 중 특정 annotation이 붙여진 곳에 Advice를 적용하는 @annotation을 사용하거나, execution을 사용한 방법이 있는데, execution은 포인트 컷 조합이 불가능하기 때문에 번거로워서 @annotation을 사용하는 것이 편하다.

### 4-1. @annotation 예제

Annotation 생성시 주의할 점은 Annotation의 범위를 CLASS 이상으로 줘야한다는 것이다. 그래야 컴파일해서 생성된 클래스 파일에도 해당 Annotation의 정보가 남기 때문이다. Custom Annotation 생성시 @Retention의 기본값은 CLASS이다.

```
//자바 Doc에 Annotation 정보를 표현하기 위한 Annotation
@Documented
//Annotation이 적용될 위치로 METHOD로 한정
@Target(ElementType.METHOD)
//Annotation의 범위로 CLASS로 지정하였으며, @Retention를 사용하지 않으면 기본값은 CLASS 이다.
@Retention(RetentionPolicy.CLASS)
public @interface PerLogging {
}
```

PerLogging Annotation이 붙은 메서드에 해당 Advice를 실행하도록 수정하였다.

```
@Component
@Aspect
public class PerfAspect {

    @Around("@annotation(PerLogging)")
    public Object logPert(ProceedingJoinPoint pjp) throws Throwable{
        long begin = System.currentTimeMillis();
        Object retval = pjp.proceed();
        System.out.println(System.currentTimeMillis() - begin);
        return retval;
    }

}

```

PerLogging Annotation이 붙은 createEvent메서드와 publishEvent 메서드만 메서드 실행시간이 측정된다.

```
@Service
public class SimpleEvnetService implements EventService {

    @Override
    @PerLogging
    public void createEvent() {
        try {
            Thread.sleep(1000);
        }catch (InterruptedException e){
            e.printStackTrace();
        }

        System.out.println("Created an event");
    }

    @Override
    @PerLogging
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

### 4-2. bean 예제

만약 특정 bean에 있는 모든 public 메서드에 Advice를 적용하고 싶으면 표현식에서 bean을 사용하면 된다.

simpleEvnetService bean에 있는 모든 public 메서드에 해당 Advice가 적용된다.
```
@Component
@Aspect
public class PerfAspect {

    @Around("bean(simpleEvnetService)")
    public Object logPert(ProceedingJoinPoint pjp) throws Throwable{
        long begin = System.currentTimeMillis();
        Object retval = pjp.proceed();
        System.out.println(System.currentTimeMillis() - begin);
        return retval;
    }

}
```

### 4-3. 그 외 표현식 예제

@Before와 @AfterReturning를 사용해여 simpleEvnetService 클래스의 public 메서드들이 시작과 끝날때 hello와 end를 출력하도록 하였다.

```
@Component
@Aspect
public class PerfAspect {

    @Before("bean(simpleEvnetService))")
    public void hello(){
        System.out.println("hello");
    }

    @AfterReturning("bean(simpleEvnetService)")
    public void end(){
        System.out.println("end");
    }


}

```




