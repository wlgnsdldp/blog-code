# SpEL (Spring Expression Language)

## 1. SpEL 이란
* 스프링 프로젝트에 전반에 걸쳐 사용하는 Expression Language 이다.
* 객체 그래프를 조회하고 조작하는 기능을 제공한다.
* Unified Expression Language과 비슷하지만, SpEL은 메소드 호출을 지원하며 문자열 템플릿 기능도 제공한다.
    * Unified Expression Language는 JSP에서 제공한 EL이다.
    ```
    <c:if test="${sessionScope.cart.numberOfItems > 0}">
    ...
    </c:if>
    ```
    
* 스프링 3.0 부터 지원하며, 스프링 코어 뿐만 아니라 스프링 시큐리티, 스프링 데이터 등 여러 프로젝트에서 사용이 가능하다.

## 2. SpEL 문법
1. #{"표현식"}
   * 표현식을 평가 한다.
   * 표현식은 프로퍼티를 가질 수 있다.

2. ${"프로퍼티"}
   * 프로퍼티의 값을 가져온다.
   * 프로퍼티는 표현식을 가질 수 없다.
    
3. 이 외에도 다양한 Operation 이 가능하다. 

* [SpEL Reference](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#expressions)

## 3. SpEL 예제(표현식/프로퍼티)

```
@Component
public class AppRunner implements ApplicationRunner {
    //@Value안에 표현식(#{}) 사용시 SpEL로 평가후 결과 값을 변수에 저장한다.
    @Value("#{1 + 1}")
    int value;

    @Value("#{'hello' + 'world'}")
    String greeting;

    @Value("#{1 eq 1}")
    boolean trueOffalse;

    //@Value안에 프로퍼티(${}) 사용시 해당 프로퍼티를 가져와 변수에 저장한다. 
    @Value("${my.value}")
    int myValue;

    //표현식 안에 프로퍼티를 사용할 수도 있다.
    @Value("#{${my.value} eq 100}")
    boolean inMyValue100;

    //표현식을 사용해 Bean에 있는 값을 가져올 수도 있다.
    @Value("#{sample.data}")
    int samAnIntData;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println("==============");
        System.out.println(value);
        System.out.println(greeting);
        System.out.println(trueOffalse);
        System.out.println(myValue);
        System.out.println(inMyValue100);
        System.out.println(samAnIntData);
    }
}
```

프로퍼티 값을 가져올 수 있는지 테스트 하기 위해 application.properties에 정의 하였다.
```
my.value = 100
```

표현식을 사용하여 Bean의 값을 가져올 수 있는지 테스트 하기 위한 클래스 이다.
```
@Component
public class Sample {

    private int data = 200;

    public int getData() {
        return data;
    }

    public void setData(int data) {
        this.data = data;
    }
}
```

## 4. SpEL 예제(코드로 구현)

SpEL 표현식을 코드로 구현할 수도 있다.
```
@Component
public class AppRunner implements ApplicationRunner {

    @Override
    public void run(ApplicationArguments args) throws Exception {
        ExpressionParser parser = new SpelExpressionParser();
        Expression expression = parser.parseExpression("1 eq 1");
        Boolean value = expression.getValue(Boolean.class);
        System.out.println(value);
    }
}
```
