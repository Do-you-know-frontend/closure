# 클로저

# 요약

**클로저란?** 

클로저는 자신이 선언될 당시의 환경을 기억하는 함수

해당 함수의 생명 주기가 종료되더라도 함수의 반환된 값이 변수에 의해 아직 참조되고 있다면 생명 주기가 종료되더라도 (실행 컨텍스트 스택에서 푸시되더라도) 렉시컬 환경에 남아 참조가 가능한 것.

**렉시컬스코프(=정적스코프)**

자바스크립트 엔진은 함수를 어디서 호출했는지가 아니라 함수를 어디에 정의했는지에 따라 상위 스코프를 결정하는 것.

**클로저 사용이유?**

상태가 의도치 않게 변경되지 않도록 상태를 안전하게 은닉(information hiding)하고 특정 함수에게만 상태변경을 허용하기 위해 사용한다.

**활용예시** 

useState hook

**단점**

메모리 누수

---

# 클로저란?

> - 내부함수로부터 외부함수에 접근권한을 준다.
> 
> 
> - 함수가 특정 스코프에 접근할 수 있도록 의도적으로 그 스코프에서 정의하는 것
> 
> - 클로저는 함수와 그 함수가 선언될 당시의 lexical environment의 상호관계에 따른 현상
> 
> - 어떤 함수에서 선언한 변수를 참조하는 내부 함수에서만 발생하는 현상
> 
> - 외부 함수의 렉시컬환경이 가비지 컬렉팅 되지 않는 현상
> 
> - **어떤 함수 A에서 선언한 변수 a를 참조하는 내부함수 B를 외부로 전달한 경우, A의 실행 컨텍스트가 종료된 이후에도 변수 a가 사라지지 않는 현상**
> 
> -‘둘러싸여진 상태의 참조’와 함께 다발로 묶여진 함수의 콤비네이션이다.
> 

클로저는 함수형 프로그래밍 언어에서 나타나는 특성으로, 함수 **생성시점(선언시점)**에 언제나 생긴다.

```jsx
const a = 1;

var outer = function () {
  var a = 10;
  var inner = function () {
    console.log(++a);
  }; // innerFunc와 outerFunc 사이의 closure (oER)
  inner();
}; //outerFunc와 전역 컨텍스트 사이의 closure (oER)
outer(); // 2
```

### 문제 1

```jsx
const x = 1;

function foo(){
	const x = 10;
	bar(); 
}

function bar(){
	console.log(x);
}

foo(); // ?
bar(); // ?
```

- 정답
    
    ```jsx
    const x = 1;
    
    function foo(){
    	const x = 10;
    	// 상위 스코프는 함수의 정의 환경(위치)에 따라 결정된다.
    	// 함수 호출 위치와 상위 스코프는 아무런 관계가 없다.
    	bar(); 
    }
    
    // 함수 bar는 자신의 상위 스코프, 즉 전역 렉시컬 환경을 [[Environment]]에 저장하여 기억함
    function bar(){
    	console.log(x);
    }
    
    foo(); // 1
    bar(); // 1
    ```
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/53ad03ab-5d91-434e-b21d-5db077296182/Untitled.png)
    
    foo와 bar가 선언되는 시점에 내부[[Environment]] 슬롯에 window를 바라보도록 됨(상위스코프의 참조를 저장) 나중에 LE를 참조가능하게 함. 
    

## 렉시컬 스코프

상위 스코프를 결정하는 방법은 두 가지 패턴이 존재.

> 첫 번째. 함수를 어디서 **'호출'** 하는가
> 
> 
> 두 번째. 함수를 어디서 **'선언'** 하는가
> 

첫 번째 방식을 동적 스코프(Dynamic scope)라 하고, 두 번째 방식을 렉시컬 스코프(Lexical scope) 또는 정적 스코프(Static scope)라고 합니다.

**자바스크립트를 비롯한 대부분의 프로그래밍 언어는 렉시컬 스코프 즉 함수를 어디서 '선언' 하였는지에 따라 결정됩니다.**

즉, 함수의 상위 스코프는 함수를 정의한 위치에 의해 정적으로 결정되고 변하지 않는다.

**스코프의 실체는 실행 컨텍스트의 렉시컬 환경**

이 렉시컬 환경은 자신의 **외부 렉시컬 환경에 대한 참조**를 통해 상위 렉시컬 환경과 연결됨.

⇒이것이 **스코프 체인**이다.

따라서 함수의 상위 스코프를 결정한다는 것은 렉시컬 환경의 외부 렉시컬환경에 대한 참조에 저장할 참조값을 결정한다는 것과 같다.

```jsx
const x = 1;

function outer(){
	const x = 10;
    const inner = function(){console.log(x);};
    return inner;
}

const innerFunc = outer();
innerFunc(); //10
```

outer 함수를 호출하면 outer함수는 중첩 함수 inner를 반환하고 생명주기를 마감한다. 즉 실행컨텍스트의 스택에서 제거가 된다. 이때 outer 함수의 지역 변수 x 또한 생명 주기를 마감한다. 따라서 outer 함수의 지역 변수 x는 더이상 유효하지 않게 되지만, innerFunction()에는 10이 호출됨을 볼 수 있다.

**외부 함수보다 중첩 함수가 더 오래 유지되는 경우 중첩 함수는 이미 생명 주기가 종료한 외부 함수의 변수를 참조할 수 있다. 이러한 중첩함수를 클로저(closure)라고 부른다.**

**outer함수 호출이 되고 나면 outer함수의 실행 컨텍스는 실행 컨텍스트 스택에서 제거되지만 outer 함수의 렉시컬 환경까지 소멸하는 것은 아님.**

+ 자바스크립트의 모든 함수는 상위 스코프를 기억하므로, 이론적으로 모든 함수는 클로저 이지만, 상위 스코프의 어떤 식별자도 참조하지 않는 함수는 클로저가 아니다.

```jsx
function foo(){
 	  const x = 1;
    const y = 2;

    //일반적으로 클로저라고 하지 않는다.
    function bar(){
    	const z = 3;

        // 상위 스코프의 식별자를 참조하지 않는다.
        console.log(z);
        }
      return  bar;
      }
      const bar = foo();
      bar();
```

   

# 클로저 사용이유

클로저는 **상태(state)를 안전하게 변경하고 유지하기 위해 사용.**

다시 말해, 상태가 의도치 않게 변경되지 않도록 상태를 안전하게 **은닉(information hiding)하고 특정 함수에게만 상태변경을 허용하기 위해 사용한다.**

```jsx
let num = 0;

const increase = function () {
	return ++num;
}

//1억개의 코드
num = 100
//1억개의 코드
console.log(increase()) // 101
```

```jsx
const increase = (function() {
	let num = 0;
    ****
    return function () {
    	return ++num;
    };
}());

console.log(increase()); // 1
console.log(increase()); // 2
console.log(increase()); // 3
```

즉시실행함수는 선언할 때 바로 실행됨.

increase = function(){ return ++num} 

return fuction() return ++num; 의 LE에 num=0을 저장하고 불릴때마다 increase()만 호출됨.

# 클로저 활용 예시

## 1. useState

```jsx
const [state, setState] = useState(initialValue)
```

함수형 컴포넌트에서 이전 상태와 현 상태의 변경이 있는지를 감지하기 위해서는 함수가 실행되었을 때 이전 상태에 대한 정보를 가지고 있어야 한다. React는 이 과정에서 클로저를 사용한다.
다시 말해 useState는 외부에 선언된 상태값에 접근해서 이전 상태를 가져오고, 변경된 상태값을 관리하고 있다. **함수형 컴포넌트도 결국 함수이기 때문에, 클로저를 통해 선언되는 시점에 접근 가능했던 외부 상태값에 계속 접근할 수 있는 것이다.** 함수형 컴포넌트에서 상태값을 변경하면 외부의 값이 변경되고, 리렌더링(=함수 재호출)을 통해 새로운 값을 받아오게 된다.

(2)간단히 구현

useState와 비슷한 역할을 하는 코드는 쉽게 구현할 수 있다.

```jsx
let _value;

export useState(initialValue){
  if (_value === 'undefined') {
    _value = initialValue;
  }
  const setValue = newValue => {
    _value = newValue;
  }
  
  return [_value, setValue];
}
```

## 2. 부분적용함수- 디바운스

부분 적용 함수란 n개의 인자를 받는 함수에 미리 m개의 인자만 넘겨 기억시켰다가, 나중에 n-m개의 인자를 넘기면 비로소 원래 함수의 실행 결과를 얻을 수 있게끔 하는 함수이다.

```jsx
var debounce = function (eventName, func, wait) {
  var timedoutId = null;
  return function (event) {
    var self = this; //event발생한 dom요소(body)
    console.log(eventName, "event 발생");
    clearTimeout(timedoutId);
    timedoutId = setTimeout(func.bind(self, event), wait); //바인드 안하면 setTimeoutdml객체인 window가 this로 바인딩됨. 
  };
};

var moveHandler = function (e) {
  console.log("mouseEvent 처리");
};

var wheelHandler = function (e) {
  console.log("wheel event 처리");
};

document.body.addEventListener("mousemove", debounce("move", moveHandler, 500));
document.body.addEventListener("mousewheel",debounce("wheel", wheelHandler, 500)
);
```

최초 이벤트가 발생하면 8번째 줄에 의해 timeout의 대기열에 'wait 시간 뒤에 func를 실행할 것'이라는 내용이 담긴다. 그런데 wait 시간이 경과하기 이전에 다시 동일한 event가 발생하면 이번에는 7번째 줄에 의해 앞서 저장했던 대기열을 초기화하고, 다시 8번째 줄에서 새로운 대기열을 등록한다. 결국 각 이벤트가 바로 이전 이벤트로부터 wait 시간 이내에 발생하는 한 마지막에 발생한 이벤트만이 초기화되지 않고 무사히 실행될 것이다.
참고) 예제에서 클로저로 처리되는 변수에는 eventName, func, wait, timeoutId가 있음.

# 클로저와 메모리 누수

메모리 소모는 클로저의 본질적인 특성. 

과거에는 의도치 않게 누수가 발생하는 여러 가지 상황들(순환 참조, 인터넷 익스플로러의 이벤트 핸들러 등) 이 있었지만 그중 대부분은 최근 자바스크립트 엔진에서는 발생하지 않거나 거의 발견하기 힘들어졌으므로 이제는 의도대로 설계한 ‘메모리 소모’에 대한 관리법만 잘 파악해서 적용하는 것으로 충분함.

## 메모리 관리 방법

클로저는 어떤 필요에 의해 의도적으로 함수의 지역변수를 메모리를 소모하도록 함으로써 발생.

그렇다면 필요성이 사라진 시점에 참조 카운트를 0으로 만들면 언젠가 GC가 수거해갈 것이고, 이때 소모됐던 메모리가 회수될 것이다. 

참조카운트를 0으로 만드는 방법: 식별자에 참조형이 아닌 기본형 데이터(보통 null이나 undefined)를 할당

```jsx
var outer = function () {
  var a = 1;
  var inner = function () {
    return ++a;
  };
  return inner;
};
var outer2 = outer();
console.log(outer2()); // 2
console.log(outer2()); // 3
outer = null; // outer의 inner 함수 참조를 끊음
```

# 참고
