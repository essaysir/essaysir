# 16 장 : 프로퍼티 어트리뷰트

## 중요한 내용 
 내부 슬롯과 메소드는 자바스크립트 엔진에서 실제로 동작 <br/>
 But . <font color='yellow'>외부로 공개된 객체의 프로퍼티 X</font> <br/>
 프로퍼티를 생성 => 프로퍼티 어트리뷰트를 기본값으로 자동 정의 <br/>
 프로퍼티의 상태로는 <font color='orange'><b>value(값), writable(갱신여부),enumerable(열거 가능 여부 ) , configuarable ( 재정의 가능 여부 ) </b></font>를 말한다.
 
```js
// 이는 밑의 메서드를 사용하여 간접적으로 확인가능
const person = { name : 'Sir' } 
console.log(Object.getOwnPropertyDescriptor(person,'name'));
// 결과값 => {value : 'Sir' , writable : true , enumerable : true , configurable : true } => 해당 값들이 기본값이다.
// 위를 프로퍼티 디스크립터 객체라고 한다.
```

만약에 , 존재하지 않는 프로퍼티나 상속받은 프로퍼티에 대한 디스크립터를 요구하면, **undefined** 가 반환된다. 

|  프로퍼티 |          |    
| -------- | -------- | 
| 데이터 프로퍼티   | 접근자 프로퍼티   | 
| 키와 값으로 이루어짐   | 접근자 함수로 구성된 프로퍼티    |
| 일반적인 프로퍼티 | 자체적으로 값 X |

 접근자 프로퍼티는 데이터 프로퍼티와 다르게 프로퍼티 어트리뷰트를 갖는다. <br/> 
    => [[Get]] , [[Set]] , [[Enumerable]] , [[Configurable]]

```js
 const person = {
    // 데이터 프로퍼티 
    firstName : 'Essay' ,
    lastName : 'Sir' ,
    // 접근자 프로퍼티 
    get fullName(){
        return `${this.firstName} ${this.lastName}`
    },
    set fullName(name){
        [this.firstName , this.lastName] = name.split(' ');
    }
 } 

// 밑의 경우에는 getter 함수가 호출된다.
 console.log(person.fullName);
 // 작동원리 
 // 1. 프로퍼티 키 ( 문자열 또는 심벌 ) => 유효한 지 확인
 // 2. 프로토타입 체인에서 프로퍼티를 검색
 // 그에 맞는 데이터 프로퍼티 또는 접근자 프로퍼티를 부른다. 
```
## 프로퍼티 정의란 ? 
새로운 프로퍼티를 추가하면서, 프로퍼티 어트리뷰트를 명시적으로 정의 또는 재정의하는 것을 의미 => 이는 Object.defineProperty 를 통해 가능
예를 들면 다음과 같다.
```js
const person = {};
// 프로퍼티 정의 
Object.defineProperty(person, 'firstName', {
    value : 'Essay' , 
     // 생략시 , get , set 과 마찬가지로   undefined가 된다 .
    writable: true ,  // 생략시 false
    enumerable : true , // 생략시 false
    configurable : true, // 생략시 false 
});
// 다음과 같이 하나씩 정의할 수도 있고, 여러개의 프로퍼티를 뒤이어 정의 할 수 도 있다. 
```
<font color='orange'><b>const 로 객체를 정의해도 재할당은 불가능해지지만, 객체 변경이 가능하다.</b></font> 밑의 메소드를 사용하면 , 아예 객체가 변경이 불가능하게 만들 수 있다.

```js
const person = { name : 'Essay'};
Object.preventExtensions(person); // 객체 확장 금지
Object.seal(person); // 객체 밀봉
Object.freeze(person); // 객체 동결 
// 이들은 모두 얕은 변경 방지로 직속 프로퍼티만ㄴ 변경이 방지되고
// 중첩 객체 영향 X 

// 중첩 객체에도 영향을 주려면 , 
// 재귀적으로 해당 메서드를 호출해야한다.
```
<br/>

## 느낀 점 및 더 배워야 할 점 



# 17 장 : 생성자 함수에 의한 객체 생성

## Object 생성자 함수

```js
  const member = new Object();
  member.name = 'Essay';
  // 이를 생성자 함수를 통해 객체를 만든 것이다.
```

그렇다면, 생성자 함수란 무엇인가 ? <br/>
생성자 함수란, <font color='orange'><b>new 연산자</b></font> 로 호출하여 객체 ( 인스턴스 ) 를 생성하는 함수를 일컫는다 .
Object 말고도 , String , Number 등이 존재한다. 
=> 이를 Class 가 대체하기 시작함 

객체 리터럴 함수로 생성하는 방식은 <font color='orange'><b>프로퍼티 구조가 같은 여러 개의 객체</b></font> 를 만들어야 하는 경우에 있어서, 반복적으로 똑같은 일을 많이 수행해야하는 단점이 있다.
생성자 함수에 의한 객체 생성 방식은 그러한 단점을 보완해준다. 

## 자바스크립트에서의 this 의 쓰임새
this 란 무엇인가 ? => 면접에서 주요 질문<br/>
=> 객체 자신의 프로퍼티나 메서들를 참조하기 위한 자기 참조 변수 
<br/>

|  함수 호출 방식 | this가 가리키는 값 (this 바인딩)         |    
| -------- | -------- | 
| 일반 함수로서 호출   | 전역 객체   | 
| 메서드로서 호출  | 메서드를 호출한 객체(마침표 앞의 객체)  |
| 생성자 함수로서 호출 | 생성자 함수가 (미래에) 생성할 인스턴스  |

new 연산자와 함께 생성자 함수를 호출하지 않으면 생성자 함수가 아니라 일반 함수로 동작한다. 

함수가 일반 함수로서 호출됨 => 내부 메서드 [[Call]] 호출 <br/>
함수가 생성자 함수로서 호출됨 => 내부 메서드 [[Construct]] 호출
 
모든 함수 객체는 callable 이지만 모든 함수 객체가 constuctor 인 것은 아니다. non-constuctor 일 수 도 있다. 
그렇다면, constructor 와 non-constructor 인지는 어떻게 구분 할 수 있는 가? 

### 함수 정의 방식을 통해 구분 
constructor : 함수 선언문 , 함수 표현식 , 클래스 
non-constructor : 메서드 , 화살표 함수 

# 18 장 : 함수와 일급객체 

### 일급 객체란 무엇인가 ? 

일급객체는 4가지 조건을 만족하는 객체를 말한다. 그렇다면 4가지 조건이 무엇인 지 살펴보자 ! 

1. 무명의 리터럴로 생성 가능( => 런타임에 생성 가능 )
2. 변수나 자료구조 등에 저장 가능 
3. 함수의 매개변수에 전달 가능
4. 함수의 반환값으로 사용 가능 

<b>자바스크립트 에서의 함수는 위의 조건을 모두 만족하므로 <font color='orange'>일급 객체</font>이다.</b> 
그렇다고 해서, 객체와 함수가 모든 게 똑같은 것은 아니다. 함수는 객체와 달리 , 호출될 수 있고, 일반 객체에는 없는 함수 고유의 프로퍼티를 소유한다는 차이가 존재한다. 

그럼 , 함수 고유의 프로퍼티 들에 대해서 살펴보도록 하자 

### arguments 프로퍼티 는 무엇인가 ?
arguments 프로퍼티 값은 arguments 객체이고, 이 객체는 함수 호출 시 전달된 인수들의 정보를 담고 있고, 이는 순회가능한 유사 배열 객체(?) , 함수 내부에서 지역 변수처럼 사용됨.

arguments 객체의 프로러티인 callee 는 arguments 객체를 생성한 함수를 나타내고, length 는 프로퍼티 인수의 개수를 가리킨다. 

arguments 객체는 매개변수 개수를 확정할 수 없는 가변 인자 함수를 구현할 때 유용하다. 예를 들면 다음과 같다. 
```js
    function solution(){
        let res = 0 ;
        for ( let i=0; i< arguments.length; i++){
            res += arguments[i];
        }
        return res; 
    }
    console.log(solution());       // 0
    console.log(solution(1,2));    // 3
    console.log(solution(1,2,3));  // 6
    // arguments 객체는 실제 배열이 아닌 유사 배열 객체라는 데 이게 
    // 정확히 무엇인지 다음에 알아보자
```

arguments 는 유사 배열 객체 이므로 배열 메소드를 사용할 경우 에러가 발생한다. 그러하여, 간접 호출( Function.prototype.call/apply/bind 메서드) 하고, 배열 메소드를 사용해야 한다는 번거로움이 존재한다. 그러나, ES6 에 들어가면서, Rest Parameter ( function answer(...
args )) 를 도입하면서 이를 해결했다.

___
### caller 프로퍼티는 무엇인가 ? 
 비표준 프로퍼티로 그렇게 중요한 부분은 아니라고 한다.

### length 프로퍼티는 무엇인가 ? 
length 프로퍼티는 <font color='red'><b>함수를 정의할 때</b></font> 선언한 매개변수의 개수를 가리킴
<br/>주의해야 할점이다!! arguments 객체의 length 프로퍼티는 인자의 개수를 가리키고, 함수 객체의 length 프로퍼티는 매개 변수의 개수를 가리킨다.

여기서 말하는 함수의 정의는 언제 일어나는 것인가 ? 

# 19 장 : 프로토 타입 

자바스크립트는 명령형 , 함수형 , 프로토타입 기반, 객체지향 프로그래밍을 지원하는 멀티 패러다임 프로그래밍 언어이다. 

## 객체 지향 프로그래밍 ( OOP )
### 절차 지향적 관점에서 벗어나 객체의 집합으로 프로그램을 표현하려는 프로그래밍 패러다임.

다양한 속성 중에서 필요한 속성만 간추려 내어 나타내는 것을 추상화라고 한다. 
객체지향 프로그래밍은 객체의 상태(state) 를 나타내는 데이터 
와 동작을 하나의 논리적인 단위로 묶은 복합적인 자료구조이다. 

상속은 객체지향 프로그래밍의 핵심 개념으로, 어떤 객체의 프로퍼티 또는 메서드를 다른 객체가 상속 받아 그래도 사용할 수 있도록 하는 것인데 , <br/>이는 <font color='yellow'><b>프로토타입( Prototype ) 을 기반으로 상속을 구현한다.</b></font> 예를 들면 , 다음과 같다.

```js
    function Circle(radius){
        this.radius = radius ;
    }

    Circle.prototype.getArea = function(){
        return Math.PI * this.radius ** 2 ; 
    }
    // Circle 생성자 함수가 생성하는 모든 인스턴스는 
    // 하나의 getArea 를 공유한다.

```

## 프로토타입 객체
프로토타입 객체( = 프로토타입 ) => 객체간 상속을 구현하기 위해 사용
<br/> 모든 객체는 '_ _ proto _ _' 접근자 프로퍼티를 통해, 즉 [[Prototype]] 내부 슬롯에 간접적으로 접근 할 수 있다.


### 느낀 점

