# 함수 호출

## 1.함수 호출

자바스크립트에서 함수를 호출하는 기본적인 방법은 아래와 같다.


```
function func(){
    
}
```

> 자바스크립트에서 함수는 Function 객체의 인스턴스이다. 그렇기 때문에 함수는 Function 객체의 메소드를 사용할 수 있다.

## 2.Function.apply(thisArg, [argsArray])

apply는 Function 객체의 메소드로 apply를 사용하여 함수를 호출할 수 있으며, apply 메소드는 두개의 인자를 가지고 있다.

- thisArg
    - 호출될 함수에게 지정될 this의 값이다.
- [argsArray]
    - 함수에 전달될 인수 집합이다.

apply를 사용하면 함수가 호출되는 시점에서 함수의 this 값을 변경해서 사용할 수 있기 때문에 해당 객체에 속한 메소드인것 처럼 실행할 수 있다.

### apply 예제

apply를 사용해서 sum 함수 내의 this를 자기 자신에 정의된 메소드 처럼 사용하고 있다.

```
function sum(){
    var _sum = 0;
    //함수에서 this는 기본적으로 window 객체를 가리키지만, apply를 사용해서 this의 값을 변경해서 사용할 수 있다.
    for(name in this){
        _sum += this[name];
    }
    return _sum;
}
    
    var o1 = {val1:1, val2:2, val3:3};
    var o2 = {v1:10, v2:50, v3:100, v4:25};
    
    //o1/o2는 sum 함수의 this가 되며, sum.apply(o1)/sum.apply(o2)가 실행되는 순간 o1/o2 객체에 sum 이라는 메소드인 것처럼 사용할 수 있다.
    alert(sum.apply(o1));
    alert(sum.apply(o2));
```

### apply 미사용시 예제

```
function sum(){
    var _sum = 0;
    for(name in this){
        //sum은 객체 property의 합을 구하는 건데, 객체내의 메소드도 존재하여 결과값이 이상하게 출력된다.
        //결과값이 이상하게 출력되는 것을 막기위해 객체 순회중 함수가 아닌 값만 더해 출력하는 조건문을 추가한다.
        if(typeof this[name] !== 'function'){
                _sum += this[name];
        }
    }
    return _sum;
}

    var o1 = {val1:1, val2:2, val3:3, sum:sum};
    var o2 = {v1:10, v2:50, v3:100, sum:sum};
    alert(o1.sum());
    alert(o2.sum());
```

## 3.Function.apply(thisArg , [argsArray]) 과 Function.call(thisArg[,arg1[,agr2[, ...]]])의 차이점

- apply 메소드는 두개의 인자를 가지고 있다.
    - thisArg
        - 호출될 함수에게 지정될 this의 값이다.
    - [argsArray]
        - 함수에 전달될 인수 집합이다.
- call 메소드는 두개의 인자를 가지고 있다.
    - thisArg
        - 호출될 함수에게 지정될 this의 값이다.
    - [,arg1[,agr2[, ...]]]
        - 함수에 전달될 인수 목록이다.

가장 큰 차이는 call()은 인수 목록을 전달하고, apply()는 인수 배열을 전달한다는 것이다.