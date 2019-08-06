# Bean의 Scope

Bean의 Scope란 **Bean 생성 범위**를 뜻한다.

## 1. Scope의 종류

Spring이 제공해 주는 Scope는 크게 **Singleton과 Prototype 타입**이 있다.

### 1. Singleton

default Scope이며, **인스턴스가 오직 하나만 생성** 된다.

### 2. Prototype

**요청시 마다 새로운 인스턴스가 생성**되며 된다.

### 3. 그 외

Request, Session, Websocket 이 있다.

## 2. Scope 지정하기

**Bean을 선언하는 부분에 @Scope 어노테이션**을 사용하여 해당 Bean의 Scope를 지정할 수 있다.

prototype class의 Bean을 Prototype 으로 지정했기 때문에 **요청시 마다 새로운 인스턴스가 생성**된다.

```
@Component
@Scope(value="prototype")
public class prototype{}
```

## 3. Prototype Bean이 Singleton Bean을 참조하면?

Singleton은 인스턴스가 오직 하나만 생성되기 때문에 문제가 발생하지 않는다.

## 4. Singleton Bean이 Prototype Bean을 참조하면?

Singleton Bean은 ApplicationContext 초기 구동시 인스턴스가 생성되기 때문에 **Prototype Bean에 변경사항이 생겨도 해당 변경사항이 업데이트 되지 않는 문제가 발생**한다.

이러한 문제는 **Prototype Bean을 proxy로 감싸므로써 해결**할 수 있다.
아래의 그림처럼 Prototype Bean을 proxy로 감싸는 이유는 Singleton Bean이 Prototype Bean을 직접 참조하면 업데이트 되는 Prototype Bean을 사용할 수 없기 때문이다.

![untitled-502727a3-809f-4601-a6f3-4352beeac004](https://user-images.githubusercontent.com/31675104/53086581-ba3d2e80-3548-11e9-81cb-b3b4b389efd6.png)

Prototype Bean을 proxy로 감싸는 방법은 @**Scope의 proxyMode 속성을 사용**한다.

* @Scope의 속성인 ScopedProxyMode.XXX에서 XXX는 DEFAULT, TARGET_CLASS, INTERFACES, NO가 있다.
    * 기본값은 DEFAULT이며, Proxy를 사용하지 않는다는 의미이다.
    * TARGET_CLASS는 해당 bean을 클래스 기반 proxy로 감싼다는 의미이다.
    * INTERFACES는 해당 bean을 인터페이스 기반 proxy로 감싼다는 의미이다.

```
@Component
@Scope(value="prototype", proxyMode=ScopedProxyMode.TARGET_CLASS)
public class prototype{}
```

## 5. Singleton 객체 사용시 주의할 점

1. Singleton 객체는 Property가 공유되며, 그로인해 Property의 값이 보장되지 않기 때문에 변경에 유의해야 한다.
2. ApplicationContext 초기 구동시 인스턴스가 생성되기 때문에 Application 구동시 시간이 걸릴수 있다.