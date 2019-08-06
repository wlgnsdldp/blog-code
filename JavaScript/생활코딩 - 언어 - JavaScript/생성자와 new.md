# 생성자와 new

## 1.객체란?

객체란 서로 연관된 변수와 함수를 그룹핑한 그릇이라고 할 수 있다. 객체 내의 변수를 프로퍼티(property), 프로퍼티에 함수가 담겨있으면 메소드(method)라고 부른다.

```
//person 객체 생성
var person = {};
    
//person 객체에 name 프로퍼티 추가
person.name = 'hong';
    
//person 객체에 introduce 메소드 추가
person.introduce = function(){
    return 'My name is'+ this.name
}
    
document.write(person.introduce());
```

위의 코드는 아래 처럼 객체 생성시 프로퍼티와 메서드를 한번에 정의하는 방식으로 작성할 수 있다.

```
    var person = {
        'name' : 'hong',
        'introduce' : function(){
            return 'My name is' + this.name
        }
    }
    
    document.write(person.introduce());
```

## 2. 생성자

생성자(constructor)는 객체를 만드는 역할을 하는 함수다. 자바스크립트에서 함수는 재사용 가능한 로직의 묶음이 아니라 객체를 만드는 창조자 라고 할 수 있다.

생성자의 기능은 객체 생성시 초기화하는 역할을 하며, 키워드 new 다음에 함수를 붙이면 그떄 함수는 함수가 아니라 객체 생성자로써의 역할을 수행한다

```
 function Person(){}
    
//함수를 호출하여 리턴받은 값을 변수 p에 담는다.
var p = Person();
    
//new 키워드에 의해 Person()은 함수가 아닌 객체 생성자가 되며, Person 객체를 리턴한다.
var p1 = new Person();
```

### 생성자 미사용시

아래 코드에서 같은 객체를 사용하지만 introduce 메소드는 동일한 기능을 수행하므로 코드가 중복된다. 이때 생성자를 사용하면 해당 코드의 중복을 줄일 수 있다.

```
function Person(){}
    
var p1 = new Person();
p1.name = 'hong';
p1.introduce = function(){
    return 'My name is' + this.name;
}    

document.write(p1.introduce()+"<br/>");
    
var p2 = new Person();
p1.name = 'shin';
p1.introduce = function(){
    return 'My name is' + this.name;
}
    
document.write(p2.introduce()+"<br/>");
```

### 생성자 사용시

생성자를 사용하면 코드가 훨씬 간결해 지고 중복이 줄어들고 재사용성이 높아진 것을 알 수 있다.

```
function Person(){
    this.name = name;
    this.introduce = function(){
        return 'My name is' + this.name;    
    }
}
    
var p1 = new Person('hong');
document.write(p1.introduce()+"<br/>");
    
var p2 = new Person('shin');
document.write(p1.introduce()+"<br/>");
```