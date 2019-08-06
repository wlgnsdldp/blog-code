# 스프링 IoC 컨테이너와 빈

## 1. Ioc(Inversion of Control)
Inversion of Control의 약자로 의존 관계 주입(Dependency Injection)이라고도 하며, 어떤 객체가 사용하는 ​의존 객체를 직접 만들어 사용하는게 아니라, **어떠한 장치(생성자, Setter 등이 해당)를 통해 주입 받아 사용하는 방법​**을 말한다.

예를들어 아래 코드에서 BookService 클래스는 BookRepository 클래스를 의존하는데 이때 new BookRepositiry(); 를 통해 의존 객체를 직접 생성하는 것이 아니라 생성자라는 장치를 통해 의존 객체를 통해 주입받는 것을 알 수 있다.

```
public class BookService {
    
    private BookRepository bookRepository 
    	
    public BookService(BookRepository bookRepository) {
    	this.bookRepositiry=bookRepositiry;
    }
  }
```

## 2. 스프링 IoC 컨테이너
스프링 IoC 컨테이너란 IoC 기능을 스프링 프레임 워크에 구현한 것이다. 스프링 IoC 컨테이너는 **Bean 설정 파일로 부터 Bean 정의를 읽어들이고, Bean을 구성하고 제공하는 역할**을 한다.

> 스프링 IoC 컨테이너라 불리는 이유는 IoC 기능을 제공하는 Bean들을 담고 있기 때문에 컨테이너라 불린다.

## 3. BeanFactory와 ApplicationContext
* BeanFactory는 스프링 IoC 컨테이너의 핵심 인터페이스로, **Bean을 생성하고 관리하는 인터페이스** 이다. 
* ApplicationContext는 **가장 자주 사용하는 인터페이스**로 BeanFactory를 상속받으며 메시지 소스 처리 기능(i18n), 이벤트 발행 기능, 리소스 로딩 기능 들이 구현되어 있다.

## 4. 빈(Bean)

**스프링 컨테이너가 관리하는 객체**를 빈(Bean)이라 부른다. 빈의 장점은 다음과 같다.

1. 의존성을 스프링 컨테이너가 관리해 준다.

2. Bean의 기본 Scope는 싱글톤 이기 때문에 런타임시 성능상 이점을 가진다.

3. Bean의 라이프 사이클을 활용하여 추가적인 작업이 가능하다.