# this 란?

## 1. this란?

- this는 함수 내에서 함수 호출 맥락(context)를 의미한다.
- 맥락이라는 것은 상황에 따라서 달라진다는 의미이다. 즉 함수를 어떻게 호출하느냐에 따라서 this가 가르키는 대상이 달라진다는 뜻이다.
- this는 함수 안에서 사용되는 keyword 이다. this는 함수 안에서 사용할 수 있는 일종의 변수이면서 약속되어 있는 변수이며 this안에 담겨 있는 값의 의미는 함수 호출 맥락(context)에 따라 달라진다는 것이다.

## 2. 함수 안에서 this

함수내에서 사용된 this는 전역객체 window를 가리킨다.

```
function func(){
    if(window === this){
        document.write("window === this");
    }
}
    
func();
```

## 3. 메소드 안에서 this

객체의 소속인 메소드의 this는 메소드가 속한 해당 객체를 가리킨다.

```
var o = {
    func : function(){
        if(o === this){
            document.write("o === this");
        }
    }
}
    
    o.func();
```

함수 안에서 this 와 메소드 안에서 this 예제를 보면 한 가지 결론을 도출할 수 있다. this는 자신이 속한 객체를 가리킨다는 것이다. 1번에서 func() 함수는 window 객체의 프로퍼티이며, 2번에서 o.func() 메소드는 o 객체의 프로퍼티이다.


## 4. 생성자 내에서 this

생성자 함수내의 this는 new로 생성자 함수를 생성한 객체에 바인딩 된다.

아래 예제에서 this는 Person 함수로 부터 생성된 Person 객체에 바인딩 된다.

```
    function Person(name){
        this.name = name;
    }
    
    var hong = new Person('hong');
    console.log(hong.name);
```

> 함수에 new 연산자를 붙여서 호출하면, 해당 함수는 생성자 함수로 동작하며 일반적인 함수인지 객체를 만들기 위한 생성자 함수인지 구분하기 위해 생성자 함수의 첫 문자는 대문자로 표기한다.

## 5.생성자 함수 동작방식

![Untitled-55f003ab-e29d-491c-9d28-fbb30c203faa](https://user-images.githubusercontent.com/31675104/60381121-7922d100-9a8a-11e9-97e3-0c4b8b674441.png)

### 1.빈 객체 생성 및 this 바인딩

생성자 함수의 코드가 실행되기 전 비어있는 객체가 생성된다. 이 비어있는 객체가 생성자 함수가 새로 생성하는 객체이다. 이후 생성자 함수 내에서 사용되는 this는 이 비어있는 객체를 가리킨다. 그리고 생성된 비어있는 객체는 생성자 함수의 prototype 프로퍼티가 가리키는 객체를 자신의 프로토 타입 객체로 설정한다.

### 2.this를 통한 프로퍼티 생성

생성된 객체에 this를 사용하여 동적으로 프로퍼티나 메소드를 생성할 수 있다. this는 새로 생성된 객체를 가리키므로 this를 통해 생성한 프로퍼티와 메소드는 새로 생성된 객체에 추가된다.

### 3.생성된 객체 반환

- 반환문이 없는 경우, this에 바인딩된 새로 생성된 객체가 반환된다. 명시적으로 this를 반환하여도 결과는 같다.
- 반환문이 this가 아닌 다른 객체를 명시적으로 반환하는 경우, this가 아닌 해당 객체가 반환된다. 이때 this를 반환하지 않은 함수는 생성자 함수로서의 역할을 수행하지 못한다. 따라서 생성자 함수는 반환문을 명시적으로 사용하지 않는다.

```
    function Person(name){
        //생성자 함수 코드 실행 전 ----- 1번
        this.name = name;    // ------ 2번
        //생성된 함수 반환    --------- 3번
    }
    
    var hong = new Person('hong');
    console.log(hong.name);
```

## 5. 객체로서 함수

자바스크립트에서는 함수 생성자가 아닌 리터럴을 사용해서 객체를 생성할 수 있다. 리터럴을 사용하면 자바스크립트 해석기가 변환시켜서 객체를 생성한다. 
   
```
    //리터럴
    functin sum3(){}; //함수 리터럴
    var sum3 = new Function(); //함수 생성자
    
    var obj = {}; //객체 리터럴
    var obj = new Object(); //객체 생성자
    
    var arr = []; //배열 리터럴
    var arr = new Array(); //함수 생성자
```

## 6. apply와 call

함수는 객체이기 때문에 메서드인 apply와 call을 가지고 있으며 해당 메서드를 통해 this의 값을 제어할 수 있다.

### apply/call
- fun.apply(thisArg, [argsArray])
    - thisArg는 호출될 함수에게 지정될 this의 값이다.
    - argsArray는 유사배열 객체로, 함수가 호출될 때 생성된 arguments 객체이다. 함수에 전달할 인자가 없는 경우 null 또는 undefined 이다.
- fun.call(thisArg[, arg1[, arg2[, ...]]])
    - apply 메소드와 기능은 동일하나 차이점은 apply는 인수 배열 하나를 받으며 call은 인수 목록을 받는다.

func 함수내에 this는 기본적으로 window를 가리키고 있다.
이때 apply왜 call을 사용하여 this가 가르키는 것을 변경시킬수 있다.
```
    var o = {};
    var p = {};
    function func(){
        switch(this){
            case o:
                document.write('o<br />');
                break;
            case p:
                document.write('p<br />');
                break;
            case window:
                document.write('window<br />');
                break;
        }
    }
    func(); //window가 출력된다.
    func.apply(o); //o가 출력된다.
    func.apply(p); //p가 출력된다.
```







