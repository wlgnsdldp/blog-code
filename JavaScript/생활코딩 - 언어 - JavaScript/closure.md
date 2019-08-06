# closure

## 1.클로저(closure)란?

클로저(closure)는 내부함수가 외부함수의 맥락(context)에 접근할 수 있는 것을 가르킨다. 내부함수는 외부함수의 지역변수에 접근할 수 있는데 외부함수의 실행이 끝나서 외부 함수가 소멸된 이후에도 내부함수가 외부함수의 변수에 접근할 있는데, 이러한 매커니즘을 클로저 라고 한다.

클로저는 자바스크립트를 이용한 고난이도의 테크닉을 구사하는데 필수적인 개념으로 활용된다.

아래 예제에서 inner 변수에는 function(){alert(title)} 함수가 저장되어 있으며, inner 함수를 호출하면 outter 함수의 지역변수인 title의 값이 경고창에 출력이 된다.
inner 함수 호출시 outter 함수는 값을 return 했기 때문에 생을 마감한 함수인데 어떻게 outter의 지역변수 값이 출력되는 것일까?

이것이 가능한 이유는 외부 함수가 소멸된 이후에도 내부함수를 통해서 외부 함수의 지역변수에 접근할 수 있는 클로저 덕분이다.

```
function outter(){
    var title='coding everybody';
    return function(){
        alert(title);
    }
}
    
//inner에는 function(){alert(title)}가 저장되어 있다.
var inner = outter();
    

inner();
```

## 2.내부함수와 외부함수

자바스크립트는 함수 안에서 또 다른 함수를 선언할 수 있으며, 내부함수는 외부함수의 지역변수에 접근할 수 있다.

아래 예제에서는 outter 함수가 호출되면 outter 함수내에서 내부함수인 inner를 호출하며, inner 함수는 자신 함수에 있는 지역변수인 title의 값을 경고창에 출력한다.

```
//외부함수
function outter(){

    //내부 함수
    function inner(){
        var title = 'coding everybody';
        alert(title);
    }
        inner();
}

outter();
```

아래 예제에서는 outter 함수가 호출되면 outter 함수내에서 내부함수인 inner()을 호출하며, inner 함수는 외부함수의 지역변수인 title의 값을 경고창에 출력한다.

```
//외부함수
function outter(){
    //외부함수에 정의된 지역변수
    var title = 'coding everybody';

    //내부 함수
    function inner(){
        //외부함수의 지역변수인 title의 값 출력
        alert(title);
    }
    inner();
}

outter();
```

## 3.private variable

클로저는 객체의 메소드에서도 사용할 수 있으며, 클로저를 사용하여 private variable를 만들 수 있다. 

아래 예제에서 factory_movie의 title는 get_title과 set_title로만 접근이 가능한 private variable이다. title 변수가 private variable인 이유는 factory_movie는 return 하는 순간 생을 마감하기 때문에 외부에서 접근이 불가능하기 때문이다. 

JavaScript는 기본적으로 Private한 속성을 지원하지 않는데, 클로저의 이러한 특성을 이용해서 Private한 속성을 사용할 수 있게된다.

```
//함수의 매개변수는 지역변수
function factory_movie(title){
    //객체 안에 메소드가 정의 되어 있는 상태
    //이때 메소드가 내부함수
    return {
        get_title : function (){
            return title;
        },
        set_title : function(_title){
            if(typeof _title === 'string'){
                title = _title;
            }else{
                alert('제목은 문자열이여야 합니다.')
            }
        }
    }
}
    
var ghost = factory_movie('Ghost in the shell');
var matrix = factory_movie('Matrix');
    
alert(ghost.get_title());
alert(matrix.get_title());
    
/*factory_movie 함수를 사용해 두 개의 객체를 생성하였으며, 그 두개의 객체는 각자 자신들이 실행된 그 시점에서의 외부함수의 지역변수에 접근할 수 있었고, 그 지역변수의 값은 유지가 되기 때문에 ghost 함수에 set_title을 공각기동대로 바꾼다는 것은 ghost 객체가 접근할 수 있는 title의 값만 바꿀뿐이지 matrix 객체가 접근할 수 있는 title 값에는 어떠한 영향도 미치지 않는다.*/

ghost.set_title('공각기동대') 
ghost.set_title(1);
alert(ghost.get_title());
alert(matrix.get_title());
```

## 4.클로저의 응용

### 문제 코드

아래 코드 생각한 결과는 arr[0] = 0 , arr[1] = 1, arr[2] = 2, arr[3] = 3, arr[4] = 4 라는 값이 출력될 것이라 생각하였는데 실제 결과는 arr[0] = 5 , arr[1] = 5, arr[2] = 5, arr[3] = 5, arr[4] = 5 였다. 왜 이런 결과값이 나온것일까?

일단 return의 i는 for문의 i에 접근하지 못한다. 왜냐하면 for문은 외부함수가 아니기 때문이다. 그렇기 때문에 i의 값이 존재하지 않아 arr[0]~arr[4]에는 각각 0,1,2,3,4가 아닌 function(){return i}가 저장되는 것이다.

arr[0]~arr[4]에는 익명함수가 저장된 뒤 아래 for문에서 arr에 저장된 배열의 값을 읽어올 때 위의 for문에서 사용된 전역변수 i의 값인 5개 대입되어서 arr 배열의 모든 값이 5가 되는 것이다.

```
var arr = [];

for(var i=0; i< 5; i++){
    arr[i] = function(){
            return i;
        }
    }

for(var index in arr){
    console.log(arr[index]());
}
```    
    
### 위의 문제를 해결한 코드

위와 같은 문제를 해결하는 방법은 arr[i]=function(){return i}를 내부함수로 하는 외부함수를 정의한 후 외부함수의 지역변수값을 내부함수가 참조할 수 있도록 변경하면 우리가 원하는 결과를 얻을 수 있다.

아래 코드에서 위의 for문이 실행시 즉시실행함수도 실행되어 외부함수의 id에는 i값이 대입되며, arr[i]에 위에처럼 function(){return id}가 저장되는 것은 동일하지만 클로저로 인해 외부 함수인 id 값에 접근할 수 있기 때문에 원했던 값들이 출력된다.

```
var arr = []

for(var i = 0; i < 5; i++){
    //id는 for문의 i값이다.
    arr[i] = function(id) {
        return function(){
            return id;
        }
    }(i);
}

for(var index in arr) {
    console.log(arr[index]());
}
```

또 다른 방법으로는 let을 사용하면 훨씬 쉽다. for문의 Statement에 let으로 선언된 변수는 for문의 반복횟수 만큼 클로저를 생성하기 때문에, 출력시 정상적으로 값이 출력되는 것이다.

```    
    //
    var arr = [];
    for(let i=0; i< 5; i++){
        arr[i] = function(){
                return i;
            }
        }
    for(var index in arr){
        console.log(arr[index]());
    }
```

[클로저](https://developer.mozilla.org/ko/docs/JavaScript/Guide/Closures)