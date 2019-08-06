# ApplicationContext와 다양한 빈(Bean) 설정 방법

## 1. 스프링 IoC 컨테이너 역할

스프링 IoC 컨테이너는 **Bean 인스턴스를 생성하며, 의존 관계를 설정하고, Bean을 제공**한다.

## 2. Bean 설정파일

스프링 IoC 컨테이너는 아래 그림 같이 **Bean 설정 파일을 읽어와서 Bean을 스프링 IoC 컨테이너에 등록**한다.  

![untitled-de2385ae-2583-45c2-8924-4cba03abd8e0](https://user-images.githubusercontent.com/31675104/52952071-d618c700-33c6-11e9-9ba3-6241337c8f06.png)

## 3. XML Bean 설정파일

가장 고전적인 Bean 설정파일이며, **ApplicationContext를 통해 xml 설정파일을 읽어와 스프링 IoC 컨테이너에 등록하여 Bean을 사용**할 수 있다.

#### application.xml
bookService와 bookRepository를 Bean으로ㄴ 등록하며, bookService는 setter 메소드를 통해 bookRepository를 주입 받도록 설정하였다.

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans.xsd">
     
   <bean id="bookService" class="com.hong.BookService">
 		<property name="bookRepository" ref="bookRepository"></property>
 	</bean>
    	
	<bean id="bookRepository" class="com.hong.BookRepository"></bean>
             
</beans>
```

#### SpringInitApplication.java
ClassPathXmlApplicationContext을 사용하여 application.xml 파일에 있는 Bean 들을 등록한다.
> ClassPathXmlApplicationContext는 클래스패스로 된 설정파일 위치를 파라미터로 받는다.

```
public class SpringInitApplication {

	public static void main(String[] args) {
		ApplicationContext ctx = new ClassPathXmlApplicationContext("application.xml");
    	}
    }
```

## 4. context:component-scan

위처럼 xml을 사용하여 Bean 설정파일 작성시, 설정 파일이 많아지면 설정 파일 마다 Bean을 등록해줘야 하는 번거로움 존재 한다. 이러한 문제를 해결하기 위해 등장한 방법이 ``context:component-scan`` 이다.

context:component-scan은 스프링 2.5부터 등장하였으며, **지정한 패키지 부터 스캔하여 특정 어노테이션이 등록된 클래스를 Bean으로 등록**한다. 특정 어노테이션은 ``@Component, @Service, @Repository, @Controller, @Configuration`` 이며,  @Service, @Repository, @Controller, @Configuration 어노테이션은 @Component 어노테이션을 확장한 것이다.

#### application.xml
com.hun 패키지 아래있는 특정 어노테이션이 사용된 클래스를 Bean으로 등록한다.

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context=" http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans">
    	
 <context:component-scan base-package="com.hun"></context:component-scan>
    	
</beans>
```

## 5. Java 설정파일

xml에 Bean 설정파일을 작성하는 것이 불편함에 따라 등장한 것이 Java 설정파일 이다. 

Java 설정파일은 **@Configuration을 사용하여 해당 클래스가 Bean 설정파일임을 지정하며, AnnotationConfigApplicationContext 클래스를 통해 Java 설정 파일을 읽어와 스프링 IoC 컨테이너에 등록**한다.

#### ApplicationConfig.java

bookRepository와 bookService를 Bean으로 등록하는 설정파일 이다.

```
@Configuration
public class ApplicationConfig {
    @Bean
    public BookRepository bookRepository() {
    	return new BookRepository();
    }
    	
    @Bean
    public BookService bookService() {
    	return new BookService();
    }
  }
```

#### SpringInitApplication.java
AnnotationConfigApplicationContext을 사용하여 ApplicationConfig.java 파일에 있는 Bean을 등록한다.

```
public class SpringInitApplication {
    
	public static void main(String[] args) {
	
   		ApplicationContext ctx = new AnnotationConfigApplicationContext("ApplicationConfig.class");
    	String[] beanDeanDefinitionNames=ctx.getBeanDefinitionNames();
    	System.out.println(Arrays.toString(beanDeanDefinitionNames));
    	}
    }
```

## 6. @ComponentScan

위의 Java 설정방법도 xml 처럼 설정파일의 수가 증가시 설정파일들을 등록해줘야 하는 번거로움이 증가한다. 이러한 문제를 해결하기 위해 등장한 방법이 @ComponentScan이다.

@ComponentScan은 **지정한 패키지 또는 지정한 클래스가 위치한 곳 부터 스캔하여 @Component, @Service, @Repository, @Controller, @Configuration 어노테이션이 사용된 클래스를 Bean으로 등록**한다.

#### SpringInitApplication.java

* ``basePackages`` 속성은 **패키지를 기준으로 스캔**한다.
* ``basePackageClasses`` 속성은 **해당 클래스가 속한 패키지를 기준으로 스캔**한다.

```
@ComponentScan(basePackages="com.hong")
@ComponentScan(basePackageClasses=com.hong.SpringInitApplication.class)
public class SpringInitApplication {
    
}
```