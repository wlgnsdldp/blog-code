# @Component와 @ComponentScan

@Component 어노테이션은 **해당 클래스를 Bean으로 등록해 주는 역할**을 한다. @Component를 확장한 어노테이션으로는 @Configuration, @Service, @Repository, @Controller가 있다.

## 1. @ComponentScan

스프링 3.1부터 도입된 어노테이션으로 **지정한 패키지 또는 지정한 클래스가 위치한 곳 부터 스캔하여 @Component, @Configuration, @Service, @Repository, @Controller 어노테이션이 등록된 클래스를 Bean으로 등록**한다.

### 1-1. @ComponentScan 스캔 위치 설정

@ComponentScan에서 가장 중요한 설정은 스캔 위치를 어디서 부터 할 것이냐 이다.

스캔 위치는 **basePackages와 basePackageClasses** 속성을 사용하여 지정할 수 있다.

#### 1. basePackages 속성

basePackages에 **지정된 패키지부터 스캔**한다.

아래 @ComponentScan 범위는 com.hong.test 패키지에 속한 모든 패키지를 스캔한다.

```
@ComponentScan(basePackages="com.hong.test")
```

#### 2. basePackageClasses 속성

**해당 클래스가 등록되어 있는 패키지를 스캔**하며, class를 사용하기 때문에 문자열을 사용하는 basePackages에 비해 타입 안정성을 보장한다.

아래 @ComponentScan 범위는 Application 클래스가 속해 있는 com.hong.test 패키지에 속한 모든 패키지를 스캔한다.

```
@ComponentScan(basePackageClasses=com.hong.test.Application.class)
```

## 2. @Filter

@ComponentScan에서 @Filter 어노테이션을 사용하여 **특정 값에 따라 Bean으로 등록하지 않을 수도 있다.**

```
@ComponentScan(excludeFilters = {
   @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
   @Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
```
