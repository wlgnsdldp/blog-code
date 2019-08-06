## 1. AOP란?
Aspect Oriented Programming의 약자로 관점 지향 프로그래밍이라고 한다.
관점 지향 프로그래밍이란 로직을 핵심기능 관점과 부가기능 관점으로 나누어서 부가기능 관점에서의 공통된 부분을 Aspect로 모듈화 하는 것을 뜻한다.

## 2. 흩어진 관심사 (crosscutting concern)
![image](https://user-images.githubusercontent.com/31675104/57965712-da7f5c80-7982-11e9-9ce7-eddb794f1b50.png)

crosscutting concern이란 concern이 흩어져 있는 상황을 의미한다. 그렇다면 concern이란 무엇일까?

위의 그림에서 각각의 색상은 concern을 의미한다. 
concern이란 여러 클래스 또는 여러 메서드에서 나타나는 비슷한 코드들을 의미한다. 예를들어 비슷한 Field나 비슷한 Method가 있다.
가장 대표적인 에로 Transactions이 있다. 만약 위의 A,B,C 클래스에서 모두 Transactions 처리를 한다고 할 때 AOP를 사용하지 않는다면 동일한 코드 또는 비슷한 코드가 A,B,C 클래스에 존재할 것이다. 만약 이러한 상황에서 concern의 변경이 발생한다면 해당 concern을 사용하고 있는 모든 코드를 변경해야 하는 번거로움이 발생한다.

## 3. AOP 주요 개념

### 3-1. Aspect와 Target
Aspect는 crosscutting concern으로 부터 Concern을 꺼내서 모듈화 한 것을 뜻하며, Aspect에는 Advice와 PointCut이 들어가 있다.

Target은 Aspect가 적용되는 곳을 뜻한다.

### 3-2. Adivce
Advice는 해야하는 일, 즉 실질적인 부가기능을 담은 구현체를 뜻한다.

### 3-3. Join Point와 PointCut은
Join point는 Advice가 적용될 위치를 뜻한다. 여러 Join Point가 있으며( 생성자 호출했을때, 필드에 접근하기 전, 필드에서 값을 가져갔을 때 등...)가장 자주 사용하는 Join Point는 메서드 실행시점이다. Join Point는 스펙적인 성향이 강하다.

PointCut은 Join point의 subset으로 Advice가 구체적으로 어디에 적용되는지 정의한 것이다. 예를들어 A 클래스의 B 메서드를 호출할떄 Hello Aspect를 적용해라 알려주는 것이 PointCut이다.

## 4. 스프링 AOP
1. 스프링 AOP는 AOP의 구현체를 제공한다.
2. 자바에 만들어져 있는 AOP 구현체인 AspectJ와 연동해서 사용할 수 있는 기능도 제공한다.
3. 스프링 자체에서 구현한 스프링 AOP 기능을 활용할 수 있다. 이 기능을 기반으로 스프링 트랜잭션이나 여러 가지 다른 기능들에 적용이 되고 있다.

## 5, AOP 적용 방법

### 5-1. 컴파일
자바파일을 클래스파일로 만들 때 바이트 코드들을 조작을 하면서 조작이 된 바이트 코드를 생성해 내는 것

예를들어 A 클래스의 Foo 메서드가 있고 ,Hello 라는 Aspect가 있는 상태에서 A 클래스의 Foo 메서드가 실행되기 전에 Hello가 출력되어야 한다면, 클래스 파일에 Hello를 찍는 메서드가 같이 들어 있어야 한다.

장점 : 이미 컴파일 되었기 때문에 로드 타임/런타임에 비해 성능적인 이점을 가진다.
단점 : 별도의 컴파일 과정이 필요하다.

### 5-2. 로드 타임
컴파일 된 클래스 파일을 JVM에 로딩하는 시점에 끼워넣는 것이다. 컴파일 된 클래스 파일은 변경사항이 없지만 JVM에 올라가 있는 클래스 파일에는 변경이 일어나있다.

예를들어 A 클래스는 A 클래스로 컴파일 되며, JVM에서 A 클래스 파일을 로딩하는 시점에 Hello를 출력하는 메서드를 끼워 넣는 것이다.

>로드타임 위빙(Load-time weaving) : Weaving is an AOP concept and it is the phase of integrating the aspects with the targeted code.

장점 : 다양한 문법이 사용가능 하다.
단점 : 클래스 로딩 할 때 약간의 부하가 생기며, Load-time weaver 설정이 필요하다.

### 5-3. 런타임
스프링 AOP가 사용하는 방법이다.

스프링 AOP가 적용되는 과정은 아래와 같다.
1. A bean에 특정 Aspect를 적용해야 한다는 것을 스프링이 알고 있다. 
2. A bean을 만들 때 A bean을 감사는 A 타입의 Proxy를 만들어 낸다.(스프링에서 bean이 생성되는 시기는 Application이 실행될 때 이다.)
3. 생성된 A 타입의 proxy bean을 통해 A 타입의 proxy bean이 실제 A bean이 가지고 있는 Foo라는 메서드를 호출하기 직전에 Hello를 먼저 찍는 일을 한 뒤 A bean을 호출한다.

장점 : 별로의 설정이 필요가 없으며, 러닝커브가 낮고 문법이 쉽다.
단점 : bean 생성시 약간의 부하가 생긴다.

## 6. 정리
정리하면 AOP란 흩어진 crosscutting concern을 Aspect로 모듈화 하고 핵심기능에서 분리하여 재사용 하기 위한 것이다.