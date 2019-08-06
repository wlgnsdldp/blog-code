# argument

## 1.arguments 객체란?

arguments 객체는 함수 안에서 인자에 대한 정보를 담고 있는 객체이며, arguments 객체는 사용방법이 배열과 상당히 유사하기 때문에 유사 배열이라고도 한다. 

```
function sum(){
    var i, _sum = 0;    
    //arguments는 유사배열이기 때문에 argumemts.length를 사용하여 배열의 길이를 구할 수 있다. 
    for(i = 0; i < arguments.length; i++){
        //arguments는 유사배열이기 때문에 배열의 각 index에 접근하는 법처럼 arguments[i] 로 접근할 수 있다.
        document.write(i+' : '+arguments[i]+'<br />');
        _sum += arguments[i];
    }   
    return _sum;
}

    //sum 함수에 파라미터가 정의되어 있지 않은데 인자값을 넘겨주는 상태이다.
    //자바스크립트는 관대한 언어이기 때문에 함수에 파라미터를 정의하지 않은데 인자가 존재하더라고, 또는 매개변수와 인자의 개수가 다르더라도 error가 발생하지 않는다.
    document.write('result : ' + sum(1,2,3,4));
```

>arguments 객체는 인자값이 몆 개가 들어올지 알 수 없을때 사용하면 유용하다.

## 2.arguments.length

함수에 length를 사용하면 함수에 정의된 파라미터의 개수를 return 하며, arguments 객체에 length를 사용하면 함수 호출시 넘겨진 인자의 개수를 return 한다.


```
function zero(){
    console.log(
        'zero.length', zero.length,
        'arguments', arguments.length
    );
}

zero();
    

function one(arg1){
    console.log(
        'one.length', one.length,
        'arguments', arguments.length
    );
}

one('val1', 'val2');
    
function two(arg1, arg2){
    console.log(
        'two.length', two.length,
        'arguments', arguments.length
    );
}
two('val1');
```

> 자바스크립트는 관대한 언어이기 때문에 파라미터의 개수와 인자의 개수가 달라도 문제가 되지 않는다. 오히려 관대하기 때문에 오류가 발생할 수 있는다. 예를들어 A개발자가 만든 함수에서 파라미터가 1개만 넘어오길 바라는데 B라는 개발자는 인자를 2개 넘겨줄시 A개발자의 의도와 달리 함수가 다르게 실행될 문제가 있다. 이때는 함수.length와 arguments.length를 사용하여 파라미터와 인자의 개수가 다를시 함수 실행을 막거나 경고창을 띄워줄 수 있다.