## 1.Null-safety

스프링 프레임워크 5에 추가된 Null 관련 어노테이션이다.

해당 어노테이션의 목적은 IDE의 지원을 받아 컴파일 시점에 최대한 NullPointException 미연 방지하는 것 이다.

## 2. Null-safety 종류

1. @NonNull
- Null을 허용하지 않음
- return시에도 Null을 허용하지 않을시 메서드에 붙여준다.

2. @Nullable
- Null을 허용한다.
- return시에도 Null을 허용할려면 메서드에 붙여준다.

3. @NonNullApi
-Pacakget 레벨 설정
-package-info.java 최상단에 선언한다.
-해당 package에 이하에 있는 모든 parameter와 return에 NonNull을 설정

4. @NonNullFields
- Package 레벨에 설정한다.
-package-info.java 최상단에 선언한다.
- 해당 package에 이하에 있는 모든 parameter와 return에 Nullable을 설정

## 3. IDE Setting(InteillJ)
Null-safety 관련 경고를 받기 위해서는 IDE 세팅이 필요하다.

File - Settings - Bulid, Execution, Deployment - Compiler에서 Configure annotations...을 클릭

![image](https://user-images.githubusercontent.com/31675104/57981525-843e1680-7a73-11e9-8555-345ad33e5d03.png)

Nullable annotations에는 Nullable를 등록하며, NotNull annotations에는 NonNull을 등록한다.

![image](https://user-images.githubusercontent.com/31675104/57981546-b2235b00-7a73-11e9-9960-a201cab0e5c5.png)



