## 8.3 네이티브 프로토타입

`prototype` 프로퍼티는 자바스크립트 내부에서도 광범위하게 사용되는데, 모든 내장 생성자 함수에서 `prototype` 프로퍼티를 사용한다.

첫 번째로 자세히 살펴본 다음 어떻게 내장 객체에 새 기능을 추가하여 프로토타입 프로퍼티를 사용하는지 알아본다.



#### Object.prototype

빈 객체를 표현한다고 해 보자.

```javascript
let obj = {};
alert( obj ); // "[object Object]" ?????
```

`"[object Object]"` 문자열을 생성하는 코드는 어디에 있을까? `toString` 메서드에서 이 문자열을 생성한다는 것을 앞서 배워서 알고 있지만 도대체 코드는 어디에 있는 걸까? `obj`는 비어 있는데 말이다.

`obj = new Object()`를 줄이면 `obj = {}`가 된다. `Object`는 내장 객체 생성자 함수인데, 이 생성자 함수의 `prototype`은 `toString`을 비롯한 다양한 메서드가 구현되어있는 거대한 객체를 참조한다.

아래 그림을 보면, 

![Screen Shot 2021-06-24 at 10 39 44 PM](https://user-images.githubusercontent.com/79819941/123272965-31ca6080-d53d-11eb-90c0-63c24ed0df7c.png) 

`new Object()`를 호출하거나 리터럴 문법 `{...}`을 사용해 객체를 만들 때, 새롭게 생성된 객체의 `[[Prototype]]`은 이전 챕터에서 언급한 규칙에 따라 `Object.prototype`을 참조한다.

![Screen Shot 2021-06-24 at 10 41 14 PM](https://user-images.githubusercontent.com/79819941/123273123-5292b600-d53d-11eb-8058-137e48371280.png) 

따라서 `obj.toString()`을 호출하면 `Object.prototype`에서 해당 메서드를 가져오게 되는 것이다.

아래 예시를 통해 이를 확인할 수 있다.

```javascript
let obj = {};

alert(obj.__proto__ === Object.prototype); // true

alert(obj.toString === obj.__proto__.toString); //true
alert(obj.toString === Object.prototype.toString); //true
```

그런데 이때 `Object.prototype` 위의 체인엔 `[[Prototype]]`이 없다는 점을 주의해야 한다.

```javascript
alert(Object.prototype.__proto__); // null
```



#### 다른 내장 프로토타입

`Array`, `Date`, `Function`을 비롯한 내장 객체들 역시 프로토타입에 메서드를 저장해 놓는다.

배열 `[1, 2, 3]`을 만들면 기본 `new Array()` 생성자가 내부에서 사용되기 때문에 `Array.prototype`이 배열 `[1, 2, 3]`의 프로토타입이 된다. 또, `Array.prototype`은 배열 메서드도 제공한다. 이런 내부 동작은 메모리 효율을 높여주는 장점을 가져다준다.

명세서에선 모든 내장 프로토타입의 꼭대기엔 `Object.prototype`이 있어야 한다고 규정하는데, 이런 규정 때문에 몇몇 사람들은 "모든 것은 객체를 상속받는다."라는 말을 한다.

세 개의 내장 객체를 이용해 전체적인 그림을 그리면 다음과 같다.

![Screen Shot 2021-06-24 at 10 43 42 PM](https://user-images.githubusercontent.com/79819941/123273495-a8fff480-d53d-11eb-813f-7957392b59f2.png) 

각 내장 객체의 프로토타입을 확인해 보자.

```javascript
let arr = [1, 2, 3];

// arr은 Array.prototype을 상속받음?
alert( arr.__proto__ === Array.prototype ); // true

// arr은 Object.prototype을 상속받음?
alert( arr.__proto__.__proto__ === Object.prototype ); // true

// 체인 맨 위엔 null이 있다.
alert( arr.__proto__.__proto__.__proto__ ); // null
```

체인 상의 프로토타입엔 중복 메서드가 있을 수 있다. `Array.prototype`엔 요소 사이에 쉼표를 넣어 요소 전체를 합친 문자열을 반환하는 자체 메서드 `toString`가 있다.

```javascript
let arr = [1, 2, 3]
alert(arr); // 1,2,3 <-- Array.prototype.toString 의 결과
```

그런데 `Object.prototype`에도 메서드 `toString`이 있다. 이렇게 중복 메서드가 있을 때는 체인 상에서 가까운 곳에 있는 메서드가 사용되는데 `Array.prototype`이 체인 상에서 더 가깝기 때문에 `Array.prototype`의 `toString`이 사용된다.

![Screen Shot 2021-06-24 at 10 44 54 PM](https://user-images.githubusercontent.com/79819941/123273727-dd73b080-d53d-11eb-9cf6-2e11b595900d.png) 

Chrome 개발자 콘솔과 같은 도구를 사용하면 상속 관계를 확인할 수 있다. `console.dir`를 사용하면 내장 객체의 상속 관계를 확인하는 데 도움이 된다.

![img](https://ko.javascript.info/article/native-prototypes/console_dir_array.png) 

배열이 아닌 다른 내장 객체들 또한 같은 방법으로 동작한다. 함수도 마찬가지이다. 함수는 내장 객체 `Function`의 생성자를 사용해 만들어지는데 `call`, `apply`를 비롯한 함수에서 사용할 수 있는 메서드는 `Fuction.prototype`에서 가져온다. 함수에도 `toString`이 구현되어 있다.

```javascript
function f() {}

alert(f.__proto__ == Function.prototype); // true
alert(f.__proto__.__proto__ == Object.prototype); // true, 객체에서 상속받음
```



#### 원시값

문자열과 숫자 불린값을 다루는 것은 엄청 까다롭다. 문자열과 숫자 불린값은 객체가 아닌데 이런 원시값들의 프로퍼티에 접근하려고 하면 내장 생성자 `String`, `Number`, `Boolean`을 사용하는 임시 래퍼(wrapper) 객체가 생성된다. 임시 래퍼 객체는 이런 메서드를 제공하고 난 후에 사라진다. 래퍼 객체는 보이지 않는 곳에서 만들어지며 최적화는 엔진이 담당한다. 그런데 명세서에선 각 자료형에 해당하는 래퍼 객체의 메서드를 프로토타입 안에 구현해 놓고 `String.prototype`, `Number.prototype`, `Boolean.prototype`을 사용해 쓸 수 있도록 규정한다.

**`null`과 `undefined`에 대응하는 래퍼 객체는 없다.**

특수 값인 `null`과 `undefined`는 문자열과 숫자 불린값과는 거리가 있다. `null`과 `undefined`에 대응하는 래퍼 객체는 없다. 따라서 `null`과 `undefined`에선 메서드와 프로퍼티를 이용할 수 없습니다. 프로토타입도 물론 사용할 수 없다.



#### 네이티브 프로토타입 변경하기

네이티브 프로토타입을 수정할 수 있다. `String.prototype`에 메서드를 하나 추가하면 모든 문자열에서 해당 메서드를 사용할 수 있다.

```javascript
String.prototype.show = function() {
  alert(this);
};

"BOOM!".show(); // BOOM!
```

개발을 하다 보면 "새로운 내장 메서드를 만드는 게 좋지 않을까?"라는 생각이 들 때가 있다. 네이티브 프로토타입에 새 내장 메서드를 추가하고 싶은 생각이 한번 쯤은 들 건데 이는 좋지 않은 방법이다.



**중요:**

프로토타입은 전역으로 영향을 미치기 때문에 프로토타입을 조작하면 충돌이 날 가능성이 높다고 한다. 두 라이브러리에서 동시에 `String.prototype.show` 메서드를 추가하면 한 라이브러리의 메서드가 다른 라이브러리의 메서드를 덮어쓰기 때문이다. 그렇기 때문에 이런 이유로 네이티브 프로토타입을 수정하는 것을 추천하지 않는다.

**모던 프로그래밍에서 네이티브 프로토타입 변경을 허용하는 경우는 딱 하나뿐인데 바로 폴리필을 만들 때이다.**

폴리필은 자바스크립트 명세서에 있는 메서드와 동일한 기능을 하는 메서드 구현체를 의미한다. 명세서에는 정의되어 있으나 특정 자바스크립트 엔진에서는 해당 기능이 구현되어있지 않을 때 폴리필을 사용한다.

폴리필을 직접 구현하고 난 후 폴리필을 내장 프로토타입에 추가할 때만 네이티브 프로토타입을 변경하자.

```javascript
if (!String.prototype.repeat) { // repeat이라는 메서드가 없다고 가정해보자
  // 프로토타입에 repeat를 추가

  String.prototype.repeat = function(n) {
    // string을 n회 반복(repeat)한다.

    // 실제 이 메서드를 구현하려면 코드는 더 복잡해질것이다.
    // 전체 알고리즘은 명세서에서 확인할 수 있다.
    // 그런데 완벽하지 않은 폴리필이라도 충분히 쓸만하다.
    return new Array(n + 1).join(this);
  };
}

alert( "La".repeat(3) ); // LaLaLa
```



#### 프로토타입에서 빌려오기

[call/apply와 데코레이터, 포워딩](https://ko.javascript.info/call-apply-decorators#method-borrowing)에서 메서드 빌리기에 대한 내용을 학습한 바 있는데, 한 객체의 메서드를 다른 객체로 복사할 때 이 기법이 사용된다. 개발을 하다 보면 네이티브 프로토타입에 구현된 메서드를 빌려야 하는 경우가 종종 생긴다고 한다.

유사 배열 객체를 만들고 여기에 `Array` 메서드를 복사해보자.

예시:

```javascript
let obj = {
  0: "Hello",
  1: "world!",
  length: 2,
};

obj.join = Array.prototype.join;

alert( obj.join(',') ); // Hello,world!
```

예시를 실행하면 에러 없이 의도한 대로 동작한다. 내장 메서드 `join`의 내부 알고리즘은 제대로 된 인덱스가 있는지와 `length` 프로퍼티가 있는지만 확인하기 때문인데, 호출 대상이 진짜 배열인지는 확인하지 않는다. 다수의 내장 메서드가 이런 식으로 동작한다. 메서드 빌리기 말고도 `obj.__proto__`를 `Array.prototype`로 설정해 배열 메서드를 상속받는 방법이 있다. 이렇게 하면 `obj`에서 모든 `Array`메서드를 사용할 수 있지만 이 방법은 `obj`가 다른 객체를 상속받고 있을 때는 사용할 수 없다. 자바스크립트는 단일 상속만을 허용한다는 점을 기억하자. 메서드 빌리기는 굉장히 유연하다. 여러 객체에서 필요한 기능을 가져와 섞는 것을 가능하게 해주기 때문이다.



> #### 요약
>
> - 모든 내장 객체는 같은 패턴을 따른다.
>   - 메서드는 프로토타입에 저장된다(`Array.prototype`, `Object.prototype`, `Date.prototype` 등).
>   - 객체 자체엔 데이터만 저장한다(배열의 요소, 객체의 프로퍼티, 날짜 등).
> - 원시값 또한 래퍼 객체의 프로토타입에 `Number.prototype`, `String.prototype`, `Boolean.prototype` 같은 메서드를 저장한다. 
> - `undefined`와 `null` 값은 래퍼 객체를 가지지 않는다.
> - 내장 프로토타입을 수정할 수 있다. 내장 프로토타입의 메서드를 빌려와 새로운 메서드를 만드는 것 역시 가능하지만내장 프로토타입 변경은 되도록 하지 않아야 한다.
> -  내장 프로토타입은 새로 명세서에 등록된 기능을 사용하고 싶은데 자바스크립트 엔진엔 이 기능이 구현되어있지 않을 때만 변경하는 게 좋다.





## 8.4 프로토타입 메서드와 __proto__가 없는 객체

이 절의 첫 번째 챕터에서 프로토타입을 설정하기 위한 모던한 방법이 있다고 했었는데, `__proto__`는 브라우저를 대상으로 개발하고 있다면 다소 구식이기 때문에 더는 사용하지 않는 것이 좋다고 표준에도 관련 내용이 명시되어있다.

대신 아래와 같은 모던한 메서드들을 사용하는 것이 좋다.

- [Object.create(proto, [descriptors\])](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/create) – `[[Prototype]]`이 `proto`를 참조하는 빈 객체를 만든다. 이때 프로퍼티 설명자를 추가로 넘길 수 있다.
- [Object.getPrototypeOf(obj)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/getPrototypeOf) – `obj`의 `[[Prototype]]`을 반환한다.
- [Object.setPrototypeOf(obj, proto)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/setPrototypeOf) – `obj`의 `[[Prototype]]`이 `proto`가 되도록 설정한다.

앞으론 아래 예시처럼 `__proto__` 대신 이 메서드들을 사용하도록 하자.

```javascript
let animal = {
  eats: true
};

// 프로토타입이 animal인 새로운 객체를 생성한다.
let rabbit = Object.create(animal);

alert(rabbit.eats); // true

alert(Object.getPrototypeOf(rabbit) === animal); // true

Object.setPrototypeOf(rabbit, {}); // rabbit의 프로토타입을 {}으로 바꾼다.
```

메서드를 소개할 때 잠시 언급한 것처럼 `Object.create`에는 프로퍼티 설명자를 선택적으로 전달할 수 있다. 설명자를 이용해 새 객체에 프로퍼티를 추가해 보자.

```javascript
let animal = {
  eats: true
};

let rabbit = Object.create(animal, {
  jumps: {
    value: true
  }
});

alert(rabbit.jumps); // true
```

설명자는 [프로퍼티 플래그와 설명자](https://ko.javascript.info/property-descriptors)에서 배운 것과 같은 형태로 사용하면 된다.

`Object.create`를 사용하면 `for..in`을 사용해 프로퍼티를 복사하는 것보다 더 효과적으로 객체를 복제할 수 있다.

```javascript
let clone = Object.create(Object.getPrototypeOf(obj), Object.getOwnPropertyDescriptors(obj));
```

`Object.create`를 호출하면 `obj`의 모든 프로퍼티를 포함한 완벽한 사본이 만들어진다, 사본엔 열거 가능한 프로퍼티와 불가능한 프로퍼티, 데이터 프로퍼티, getter, setter 등 모든 프로퍼티가 복제되며 또한  `[[Prototype]]`도 복제된다.



#### 비하인드 스토리

`[[Prototype]]`을 다룰 수 있는 방법은 다양하다. 목표는 하나지만 목표를 이루기 위한 수단은 여러 가지인데, 역사적인 이유가 있다.

- 생성자 함수의 `"prototype"` 프로퍼티는 아주 오래전부터 그 기능을 수행하고 있었다.
- 그런데 2012년, 표준에 `Object.create`가 추가되었다. `Object.create`를 사용하면 주어진 프로토타입을 사용해 객체를 만들 수 있긴 하지만, 프로토타입을 얻거나 설정하는것은 불가능했다. 그래서 브라우저는 비표준 접근자인 `__proto__`를 구현해 언제나 프로토타입을 얻거나 설정할 수 있도록 하였다.
- 이후 2015년에 `Object.setPrototypeOf`와 `Object.getPrototypeOf`가 표준에 추가되면서 `__proto__`와 동일한 기능을 수행할 수 있게 되었다. 그런데 이 시점엔 `__proto__`가 모든 곳에 구현되어 있어서 사실상 표준(de-facto standard)이 되어버렸다. 표준의 부록 B(Annex B)에 추가되기도 하였습니다. 이 부록에 추가되면 브라우저가 아닌 환경에선 선택사항이라는것을 의미한다.

이런 이유 때문에 지금은 여러 방식을 원하는 대로 쓸 수 있게 된 것이다.

그런데 "왜 `__proto__`가 함수 `getPrototypeOf/setPrototypeOf`로 대체되었을까?"라는 의문이 떠오를 수 있다. 답은 `__proto__`가 왜 나쁜지 이해하면 얻을 수 있다. 

**속도가 중요하다면 기존 객체의 `[[Prototype]]`을 변경하지 말자.**

원한다면 언제나 `[[Prototype]]`을 얻거나 설정할 수 있다. 기술적인 제약이 있는 건 아니지만 대개는 객체를 생성할 때만 `[[Prototype]]`을 설정하고 이후엔 수정하지 않는다. `rabbit`이 `animal`을 상속받도록 설정하고 난 이후엔 이를 변경하지 않는다.

자바스크립트 엔진은 이런 시나리오를 토대로 최적화되어 있다. `Object.setPrototypeOf`나 `obj.__proto__=`를 써서 프로토타입을 그때그때 바꾸는 연산은 객체 프로퍼티 접근 관련 최적화를 망치기 때문에 매우 느리다. 그러므로 `[[Prototype]]`을 바꾸는 것이 어떤 결과를 초래할지 확실히 알거나 속도가 전혀 중요하지 않은 경우가 아니라면 `[[Prototype]]`을 바꾸지 말자.



#### '아주 단순한' 객체

알다시피 객체는 키-값 쌍을 저장할 수 있는 연관 배열이다.

그런데 커스텀 사전을 만드는 것과 같이 사용자가 직접 입력한 키를 가지고 객체를 만들다 보면 사소한 결함이 발견된다. 다른 문자열은 괜찮지만 `"__proto__"`는 키로 사용할 수 없다는 결함이다.

```javascript
let obj = {};

let key = prompt("입력하고자 하는 key는 무엇이지?", "__proto__");
obj[key] = "...값...";

alert(obj[key]); // "...값..."이 아닌 [object Object]가 출력된다.
```

사용자가 프롬프트 창에 `__proto__`를 입력하면 값이 제대로 할당되지 않는다.`__proto__` 프로퍼티는 특별한 프로퍼티라는 것을 이미 알고 있고,  `__proto__`는 항상 객체이거나 `null`이어야 한다. 문자열은 프로토타입이 될 수 없다.

키가 무엇이 되었든, 키-값 쌍을 저장하려고 하는데 키가 `__proto__`일 때 값이 제대로 저장되지 않는 건 명백한 버그이다.

위 예시에선 이 버그가 그리 치명적이진 않다. 그런데 할당 값이 객체일 때는 프로토타입이 바뀔 수 있다는 치명적인 버그가 발생할 수 있다. 프로토타입이 바뀌면 예상치 못한 일이 발생할 수 있기 때문이다.

개발자들은 대개 프로토타입이 중간에 바뀌는 시나리오는 배제한 채 개발을 진행한다. 이런 고정관념 때문에 버그의 원인을 찾는 게 힘들어진다. 이유는 프로토타입이 바뀐 것을 눈치채지 못하기 때문이다. 서버 사이드에서 자바스크립트를 사용 중일 땐 이런 버그가 취약점이 되기도 한다.

`toString`을 비롯한 내장 메서드에 할당을 할 때도 같은 이유 때문에 예상치 못한 일이 일어날 수 있다.

그렇다면 이런 문제는 어떻게 예방할 수 있을까? 객체 대신 `맵`을 사용하면 모든 것이 해결된다.

그런데 자바스크립트를 만든 사람들이 아주 오래전부터 이런 문제를 고려했기 때문에 `객체`를 써도 문제를 피할 수 있는데, `__proto__`는 객체의 프로퍼티가 아니라 `Object.prototype`의 접근자 프로퍼티이다.

![Screen Shot 2021-07-03 at 11 51 10 PM](https://user-images.githubusercontent.com/79819941/124358194-291f0c00-dc5a-11eb-93dc-934d4841a135.png) 

그렇기 때문에 `obj.__proto__`를 읽거나 쓸때는 이에 대응하는 getter·setter가 프로토타입에서 호출되고 `[[Prototype]]`을 가져오거나 설정한다.

이 절을 시작할 때 언급한 것처럼 `__proto__`는 `[[Prototype]]`에 접근하기 위한 방법이지 `[[Prototype]]` 그 자체가 아닌 것이다.

이제 간단한 트릭을 써 객체가 연관 배열의 역할을 다 할 수 있도록 해보자.

```javascript
let obj = Object.create(null);

let key = prompt("입력하고자 하는 key는 무엇인가요?", "__proto__");
obj[key] = "...값...";

alert(obj[key]); // "...값..."이 제대로 출력된다.
```

`Object.create(null)`을 사용해 프로토타입이 없는 빈 객체를 만들어 보았다. `[[Prototype]]`이 `null`인 객체를 만든 것이다.

![Screen Shot 2021-07-03 at 11 51 17 PM](https://user-images.githubusercontent.com/79819941/124358207-389e5500-dc5a-11eb-8ff9-8642e5d4a1e7.png) 



`Object.create(null)`로 객체를 만들면 `__proto__` getter와 setter를 상속받지 않는다. 이제 `__proto__`는 평범한 데이터 프로퍼티처럼 처리되므로 버그 없이 예시가 잘 동작하게 된다.

이런 객체는 ‘아주 단순한(very plain)’ 혹은 ‘순수 사전식(pure dictionary)’ 객체라고 부른다. 일반 객체 `{...}` 보다 훨씬 단순하기 때문이다.

아주 단순한 객체는 내장 메서드가 없다는 단점이 있다. `toString`같은 메서드를 사용할 수 없다.

```javascript
let obj = Object.create(null);

alert(obj); // Error: Cannot convert object to primitive value (toString이 없음)
```

연관 배열로 쓸 때는 이런 단점이 문제가 되진 않는다.

객체 관련 메서드 대부분은 `Object.keys(obj)` 같이 `Object.something(...)` 형태이다. 이 메서드들은 프로토타입에 있는 게 아니기 때문에 '아주 단순한 객체’에도 사용할 수 있다.

```javascript
let chineseDictionary = Object.create(null);
chineseDictionary.hello = "你好";
chineseDictionary.bye = "再见";

alert(Object.keys(chineseDictionary)); // hello,bye
```



>요약
>
>프로토타입에 직접 접근할 땐 다음과 같은 모던 메서드를 사용할 수 있다.
>
>- [Object.create(proto, [descriptors\])](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/create) – `[[Prototype]]`이 `proto`인 객체를 만든다. 참조 값은 `null`일 수 있고 프로퍼티 설명자를 넘기는 것도 가능하다.
>- [Object.getPrototypeOf(obj)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object.getPrototypeOf) – `obj`의 `[[Prototype]]`을 반환한다(`__proto__` getter와 같다).
>- [Object.setPrototypeOf(obj, proto)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object.setPrototypeOf) – `obj`의 `[[Prototype]]`을 `proto`로 설정한다(`__proto__` setter와 같다).
>
>사용자가 키를 직접 만들 수 있게 허용하면, 내장 `__proto__` getter·setter는 안전하지 않다. 키가 `"__proto__"`일 때 에러가 발생할 수 있다. 단순한 에러면 좋겠지만 보통 예측 불가능한 결과가 생긴다.
>
>이를 방지하려면 `Object.create(null)`을 사용해 `__proto__`가 없는 '아주 단순한 객체’를 만들거나, `맵`을 일관되게 사용하는 것이 좋다.
>
>한편, `Object.create`를 사용하면 객체의 얕은 복사본(shallow-copy)을 만들 수 있다.
>
>```javascript
>let clone = Object.create(Object.getPrototypeOf(obj), Object.getOwnPropertyDescriptors(obj));
>```
>
>`__proto__`는 `[[Prototype]]`의 getter·setter라는 점과 다른 메서드처럼 `Object.prototype`에 정의되어 있다는 것도 확인해 보았다.
>
>`Object.create(null)`을 사용하면 프로토타입이 없는 객체를 만들 수 있다. 이런 객체는 '순수 사전’처럼 사용됩니다. `"__proto__"`를 키로 사용해도 문제를 일으키지 않는다.
>
>이런 내용과 더불어 아래 메서드들을 같이 살펴보면 좋다.
>
>- [Object.keys(obj)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/keys) / [Object.values(obj)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/values) / [Object.entries(obj)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/entries) – `obj` 내 열거 가능한 프로퍼티 키, 값, 키-값 쌍을 담은 배열을 반환한다.
>- [Object.getOwnPropertySymbols(obj)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertySymbols) – `obj` 내 심볼형 키를 담은 배열을 반환한다.
>- [Object.getOwnPropertyNames(obj)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/getOwnPropertyNames) – `obj` 내 문자형 키를 담은 배열을 반환한다.
>- [Reflect.ownKeys(obj)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Reflect/ownKeys) – `obj`내 키 전체를 담은 배열을 반환한다.
>- [obj.hasOwnProperty(key)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/hasOwnProperty) – 상속받지 않고 `obj` 자체에 구현된 키 중 이름이 `key`인 것이 있으면 `true`를 반환한다.
>
>`Object.keys`를 비롯하여 객체의 프로퍼티를 반환하는 메서드들은 객체가 ‘직접 소유한’ 프로퍼티만 반환한다. 상속 프로퍼티는 `for..in`을 사용해 얻을 수 있다.

#### 

## 9.1 클래스와 기본 문법

> 클래스는 객체 지향 프로그래밍에서 특정 객체를 생성하기 위해 변수와 메소드를 정의하는 일종의 틀로, 객체를 정의하기 위한 상태(멤버 변수)와 메서드(함수)로 구성된다.
>
> -위키백과-

실무에선 사용자나 물건같이 동일한 종류의 객체를 여러 개 생성해야 하는 경우가 잦은데, 이럴 때 [new 연산자와 생성자 함수](https://ko.javascript.info/constructor-new)에서 배운 `new function`을 사용할 수 있다.

여기에 더하여 모던 자바스크립트에 도입된 `클래스(class)`라는 문법을 사용하면 객체 지향 프로그래밍에서 사용되는 다양한 기능을 자바스크립트에서도 사용할 수 있다.



#### 기본 문법

클래스는 다음과 같은 기본 문법을 사용해 만들 수 있다.

```javascript
class MyClass {
  // 여러 메서드를 정의할 수 있음
  constructor() { ... }
  method1() { ... }
  method2() { ... }
  method3() { ... }
  ...
}
```

이렇게 클래스를 만들고, `new MyClass()`를 호출하면 내부에서 정의한 메서드가 들어 있는 객체가 생성된다.

객체의 기본 상태를 설정해주는 생성자 메서드 `constructor()`는 `new`에 의해 자동으로 호출되므로, 특별한 절차 없이 객체를 초기화 할 수 있다.

```javascript
class User {

  constructor(name) {
    this.name = name;
  }

  sayHi() {
    alert(this.name);
  }

}

// 사용법:
let user = new User("John");
user.sayHi();
```

`new User("John")`를 호출하면 다음과 같은 일이 일어난다.

1. 새로운 객체가 생성된다.
2. 넘겨받은 인수와 함께 `constructor`가 자동으로 실행된다. 이때 인수 `"John"`이 `this.name`에 할당된다.

이런 과정을 거친 후에 `user.sayHi()` 같은 객체 메서드를 호출할 수 있다.

**메서드 사이엔 쉼표가 없다.**

초보 개발자는 클래스 메서드 사이에 쉼표를 넣는 실수를 저지르곤 하는데, 이렇게 쉼표를 넣으면 문법 에러가 발생한다.

클래스와 관련된 표기법은 객체 리터럴 표기법과 차이가 있다. 클래스에선 메서드 사이에 쉼표를 넣지 않아도 된다.



#### 클래스란

이 시점에서 "`클래스`가 정확히 뭔가요?"라는 의문이 생기실 것이다. 클래스는 자바스크립트에서 새롭게 창안한 개체(entity)가 아니다.  **자바스크립트에서 클래스는 함수의 한 종류이다.**

코드로 확인해보자.

```javascript
class User {
  constructor(name) { this.name = name; }
  sayHi() { alert(this.name); }
}

// User가 함수라는 증거
alert(typeof User); // function
```

`class User {...}` 문법 구조가 진짜 하는 일은 다음과 같다.

1. `User`라는 이름을 가진 함수를 만든다. 함수 본문은 생성자 메서드 `constructor`에서 가져온다. 생성자 메서드가 없으면 본문이 비워진 채로 함수가 만들어진다.
2. `sayHi`같은 클래스 내에서 정의한 메서드를 `User.prototype`에 저장한다.

`new User`를 호출해 객체를 만들고, 객체의 메서드를 호출하면 [함수의 prototype 프로퍼티](https://ko.javascript.info/function-prototype)에서 설명한 것처럼 메서드를 프로토타입에서 가져오는데 이 과정이 있기 때문에 객체에서 클래스 메서드에 접근할 수 있다.

`class User` 선언 결과를 그림으로 나타내면 아래와 같다.

![Screen Shot 2021-07-04 at 10 25 25 PM](https://user-images.githubusercontent.com/79819941/124386784-f9820980-dd16-11eb-982e-6942d519e50a.png) 

지금까지 했던 설명을 코드로 표현하면 다음과 같다.

```javascript
class User {
  constructor(name) { this.name = name; }
  sayHi() { alert(this.name); }
}

// 클래스는 함수이다.
alert(typeof User); // function

// 정확히는 생성자 메서드와 동일하다.
alert(User === User.prototype.constructor); // true

// 클래스 내부에서 정의한 메서드는 User.prototype에 저장된다.
alert(User.prototype.sayHi); // alert(this.name);

// 현재 프로토타입에는 메서드가 두 개이다.
alert(Object.getOwnPropertyNames(User.prototype)); // constructor, sayHi
```



#### 클래스는 단순한 편의 문법이 아니다

어떤 사람들은 `class`라는 키워드 없이도 클래스 역할을 하는 함수를 선언할 수 있기 때문에 `클래스`는 '편의 문법’에 불과하다고 이야기한다. 참고로 기능은 동일하나 기존 문법을 쉽게 읽을 수 있게 만든 문법을 편의 문법(syntactic sugar, 문법 설탕)이라고 한다.

```javascript
// class User와 동일한 기능을 하는 순수 함수를 만들어보자.

// 1. 생성자 함수를 만든다.
function User(name) {
  this.name = name;
}
// 모든 함수의 프로토타입은 'constructor' 프로퍼티를 기본으로 갖고 있기 때문에
// constructor 프로퍼티를 명시적으로 만들 필요가 없다.

// 2. prototype에 메서드를 추가한다.
User.prototype.sayHi = function() {
  alert(this.name);
};

// 사용법:
let user = new User("John");
user.sayHi();
```

위 예시처럼 순수 함수로 클래스 역할을 하는 함수를 선언하는 방법과 `class` 키워드를 사용하는 방법의 결과는 거의 같다. `class`가 단순한 편의 문법이라고 생각하는 이유가 여기에 있다.

그런데 두 방법에는 중요한 차이가 몇 가지 있다.

1. `class`로 만든 함수엔 특수 내부 프로퍼티인 `[[FunctionKind]]:"classConstructor"`가 이름표처럼 붙는다. 이것만으로도 두 방법엔 분명한 차이가 있음을 알 수 있다.

   자바스크립트는 다양한 방법을 사용해 함수에 `[[FunctionKind]]:"classConstructor"`가 있는지를 확인한다. 이런 검증 과정이 있기 때문에 클래스 생성자를 `new`와 함께 호출하지 않으면 에러가 발생한다.

   ```javascript
   class User {
     constructor() {}
   }
   
   alert(typeof User); // function
   User(); // TypeError: Class constructor User cannot be invoked without 'new'
   ```

   대부분의 자바스크립트 엔진이 클래스 생성자를 문자열로 표현할 때 'class…'로 시작하는 문자열로 표현한다는 점 역시 다르다.

   ```javascript
   class User {
     constructor() {}
   }
   
   alert(User); // class User { ... }
   ```

   또 다른 차이점들에 대해선 더 살펴볼 예정이다.

2. 클래스 메서드는 열거할 수 없다(non-enumerable). 클래스의 `prototype` 프로퍼티에 추가된 메서드 전체의 `enumerable` 플래그는 `false`이다.

   `for..in`으로 객체를 순회할 때, 메서드는 순회 대상에서 제외하고자 하는 경우가 많으므로 이 특징은 꽤 유용하다.

3. 클래스는 항상 `엄격 모드`로 실행된다(`use strict`). 클래스 생성자 안 코드 전체엔 자동으로 엄격 모드가 적용된다.

이 외에도 `class`를 사용하면 다양한 기능이 따라오는데, 자세한 내용은 차차 알아볼 것이다.



#### 클래스 표현식

함수처럼 클래스도 다른 표현식 내부에서 정의, 전달, 반환, 할당할 수 있다.

먼저 클래스 표현식을 만들어보자.

```javascript
let User = class {
  sayHi() {
    alert("Hello");
  }
};
```

기명 함수 표현식(Named Function Expression)과 유사하게 클래스 표현식에도 이름을 붙일 수 있다.

클래스 표현식에 이름을 붙이면, 이 이름은 오직 클래스 내부에서만 사용할 수 있다.

```javascript
// 기명 클래스 표현식(Named Class Expression)
// (명세서엔 없는 용어이지만, 기명 함수 표현식과 유사하게 동작한다.)
let User = class MyClass {
  sayHi() {
    alert(MyClass); // MyClass라는 이름은 오직 클래스 안에서만 사용할 수 있다.
  }
};

new User().sayHi(); // 제대로 동작한다(MyClass의 정의를 보여줌).

alert(MyClass); // ReferenceError: MyClass is not defined, MyClass는 클래스 밖에서 사용할 수 없다.
```

아래와 같이 ‘필요에 따라’ 클래스를 동적으로 생성하는 것도 가능하다.

```javascript
function makeClass(phrase) {
  // 클래스를 선언하고 이를 반환함
  return class {
    sayHi() {
      alert(phrase);
    };
  };
}

// 새로운 클래스를 만듦
let User = makeClass("Hello");

new User().sayHi(); // Hello
```



#### getter와 setter

리터럴을 사용해 만든 객체처럼 클래스도 getter나 setter, 계산된 프로퍼티(computed property)를 포함할 수 있다.

`get`과`set`을 이용해 `user.name`을 조작할 수 있게 해보자.

```javascript
class User {

  constructor(name) {
    // setter를 활성화한다.
    this.name = name;
  }

  get name() {
    return this._name;
  }

  set name(value) {
    if (value.length < 4) {
      alert("이름이 너무 짧습니다.");
      return;
    }
    this._name = value;
  }

}

let user = new User("John");
alert(user.name); // John

user = new User(""); // 이름이 너무 짧습니다.
```

이런 방법으로 클래스를 선언하면 `User.prototype`에 getter와 setter가 만들어지므로 get과 set을 사용할 수 있다.



#### 계산된 메서드 이름

대괄호 `[...]`를 이용해 계산된 메서드 이름(computed method name)을 만드는 예시를 살펴보자.

```javascript
class User {

  ['say' + 'Hi']() {
    alert("Hello");
  }

}

new User().sayHi();
```

계산된 메서드 이름은 리터럴 객체와 유사한 형태를 띠기 때문에 사용법을 외우기 쉽다는 장점이 있다.



#### 클래스 필드

**구식 브라우저에선 폴리필이 필요할 수 있다.**

클래스 필드는 근래에 더해진 기능인데, 지금까지 살펴본 예시엔 메서드가 하나만 있었다.

'클래스 필드(class field)'라는 문법을 사용하면 어떤 종류의 프로퍼티도 클래스에 추가할 수 있다.

클래스 `User`에 `name` 프로퍼티를 추가해보자.

```javascript
class User {
  name = "John";

  sayHi() {
    alert(`Hello, ${this.name}!`);
  }
}

new User().sayHi(); // Hello, John!
```

클래스를 정의할 때 '<프로퍼티 이름> = <값>'을 써주면 간단히 클래스 필드를 만들 수 있다.

클래스 필드의 중요한 특징 중 하나는 `User.prototype`이 아닌 개별 객체에만 클래스 필드가 설정된다는 점이다.

```javascript
class User {
  name = "John";
}

let user = new User();
alert(user.name); // John
alert(User.prototype.name); // undefined
```

아울러 클래스 필드엔 복잡한 표현식이나 함수 호출 결과를 사용할 수 있다.

```javascript
class User {
  name = prompt("이름을 알려주세요.", "보라");
}

let user = new User();
alert(user.name); // 보라
```

### 

#### 클래스 필드로 바인딩 된 메서드 만들기

[함수 바인딩](https://ko.javascript.info/bind) 챕터에서 살펴본 것처럼 자바스크립트의 함수는 동적인 `this`를 갖는다.

따라서 객체 메서드를 여기저기 전달해 전혀 다른 컨텍스트에서 호출하게 되면 `this`는 원래 객체를 참조하지 않는다.

관련 예시를 살펴보자. 예시를 실행하면 `undefined`가 출력된다.

```javascript
class Button {
  constructor(value) {
    this.value = value;
  }

  click() {
    alert(this.value);
  }
}

let button = new Button("hello");

setTimeout(button.click, 1000); // undefined
```

이렇게 `this`의 컨텍스트를 알 수 없게 되는 문제를 '잃어버린 `this`(losing this)'라고 한다.

문제를 해결하기 위해 두 개의 방법을 사용할 수 있는데 [함수 바인딩](https://ko.javascript.info/bind)에서 이 방법에 대해 살펴본 바 있다.

1. `setTimeout(() => button.click(), 1000)` 같이 래퍼 함수를 전달하기
2. 생성자 안 등에서 메서드를 객체에 바인딩하기

클래스 필드는 또 다른 훌륭한 방법을 제공한다.

```javascript
class Button {
  constructor(value) {
    this.value = value;
  }
  click = () => {
    alert(this.value);
  }
}

let button = new Button("hello");

setTimeout(button.click, 1000); // hello
```

클래스 필드 `click = () => {...}`는 각 `Button` 객체마다 독립적인 함수를 만들고 함수의 `this`를 해당 객체에 바인딩시켜준다. 따라서 개발자는 `button.click`을 아무 곳에나 전달할 수 있고, `this`엔 항상 의도한 값이 들어가게 된다.

클래스 필드의 이런 기능은 브라우저 환경에서 메서드를 이벤트 리스너로 설정해야 할 때 특히 유용하다.



## 9.2 클래스 상속

클래스 상속을 사용하면 클래스를 다른 클래스로 확장할 수 있는데 기존에 존재하던 기능을 토대로 새로운 기능을 만들 수 있다.



#### ‘extends’ 키워드

먼저, 클래스 `Animal`을 만들어보면,

```javascript
class Animal {
  constructor(name) {
    this.speed = 0;
    this.name = name;
  }
  run(speed) {
    this.speed = speed;
    alert(`${this.name} 은/는 속도 ${this.speed}로 달립니다.`);
  }
  stop() {
    this.speed = 0;
    alert(`${this.name} 이/가 멈췄습니다.`);
  }
}

let animal = new Animal("동물");
```

객체 `animal`과 클래스 `Animal`의 관계를 그림으로 나타내면 다음과 같다.

![Screen Shot 2021-07-06 at 3 16 39 PM](https://user-images.githubusercontent.com/79819941/124551539-3cee8c00-de6d-11eb-89cb-80531c5ad09a.png) 



또 다른 클래스 `Rabbit`을 만들어보자.

토끼는 동물이므로 `Rabbit`은 동물 관련 메서드가 담긴 `Animal`을 확장해서 만들어야 한다. 이렇게 하면 토끼도 동물이 할 수 있는 ‘일반적인’ 동작을 수행할 수 있다.

클래스 확장 문법 `class Child extends Parent`를 사용해 클래스를 확장해 보자.

`Animal`을 상속받는 `class Rabbit`를 만들어 보면,

```javascript
class Rabbit extends Animal {
  hide() {
    alert(`${this.name} 이/가 숨었습니다!`);
  }
}

let rabbit = new Rabbit("흰 토끼");

rabbit.run(5); // 흰 토끼 은/는 속도 5로 달립니다.
rabbit.hide(); // 흰 토끼 이/가 숨었습니다!
```

클래스 `Rabbit`을 사용해 만든 객체는 `rabbit.hide()` 같은 `Rabbit`에 정의된 메서드에도 접근할 수 있고, `rabbit.run()` 같은 `Animal`에 정의된 메서드에도 접근할 수 있다. 키워드 `extends`는 프로토타입을 기반으로 동작하며 `extends`는 `Rabbit.prototype.[[Prototype]]`을 `Animal.prototype`으로 설정한다. 그렇기 때문에 `Rabbit.prototype`에서 메서드를 찾지 못하면 `Animal.prototype`에서 메서드를 가져온다.

![Screen Shot 2021-07-06 at 3 18 54 PM](https://user-images.githubusercontent.com/79819941/124551698-8212be00-de6d-11eb-8ead-c2a5fc1e2953.png) 

엔진은 다음 절차를 따라 메서드 `rabbit.run`의 존재를 확인한다(그림을 아래부터 보자).

1. 객체 `rabbit`에 `run`이 있나 확인한다. `run`이 없다.
2. `rabbit`의 프로토타입인 `Rabbit.prototype`에 메서드가 있나 확인한다. `hide`는 있는데 `run`은 없다.
3. `extends`를 통해 관계가 만들어진 `Rabbit.prototype`의 프로토타입, `Animal.prototype`에 메서드가 있나 확인한다. 드디어 메서드 `run`을 찾았다.

[네이티브 프로토타입](https://ko.javascript.info/native-prototypes)에서 알아본 것처럼 자바스크립트의 내장 객체는 프로토타입을 기반으로 상속 관계를 맺는다. `Date.prototype.[[Prototype]]`이 `Object.prototype`인 것처럼 말이다. `Date` 객체에서 일반 객체 메서드를 사용할 수 있는 이유가 바로 여기에 있다.

**`extends` 뒤에 표현식이 올 수도 있다.**

클래스 문법은 `extends` 뒤에 표현식이 와도 처리해준다.

아래 예시처럼 `extends` 뒤에서 부모 클래스를 만들어주는 함수를 호출할 수 있다.

```javascript
function f(phrase) {
  return class {
    sayHi() { alert(phrase) }
  }
}

class User extends f("Hello") {}

new User().sayHi(); // Hello
```

여기서 `class User`는 `f("Hello")`의 반환 값을 상속받는다.

이 방법은 조건에 따라 다른 클래스를 상속받고 싶을 때 유용하다. 조건에 따라 다른 클래스를 반환하는 함수를 만들고, 함수 호출 결과를 상속받게 하면 된다.



#### 메서드 오버라이딩

이제 한발 더 나아가 메서드를 오버라이딩 해보자. 특별한 사항이 없으면 `class Rabbit`은 `class Animal`에 있는 메서드를 ‘그대로’ 상속받는다.

그런데 `Rabbit`에서 `stop()` 등의 메서드를 자체적으로 정의하면, 상속받은 메서드가 아닌 자체 메서드가 사용된다.

```javascript
class Rabbit extends Animal {
  stop() {
    // rabbit.stop()을 호출할 때
    // Animal의 stop()이 아닌, 이 메서드가 사용된다.
  }
}
```

개발을 하다 보면 부모 메서드 전체를 교체하지 않고, 부모 메서드를 토대로 일부 기능만 변경하고 싶을 때가 생긴다. 부모 메서드의 기능을 확장하고 싶을 때도 있다. 이럴 때 커스텀 메서드를 만들어 작업하게 되는데, 이미 커스텀 메서드를 만들었더라도 이 과정 전·후에 부모 메서드를 호출하고 싶을 때가 있다.

키워드 `super`는 이럴 때 사용한다.

- `super.method(...)`는 부모 클래스에 정의된 메서드, `method`를 호출한다.
- `super(...)`는 부모 생성자를 호출하는데, 자식 생성자 내부에서만 사용 할 수 있다.

이런 특징을 이용해 토끼가 멈추면 자동으로 숨도록 하는 코드를 만들어보자.

```javascript
class Animal {

  constructor(name) {
    this.speed = 0;
    this.name = name;
  }

  run(speed) {
    this.speed = speed;
    alert(`${this.name}가 속도 ${this.speed}로 달린다.`);
  }

  stop() {
    this.speed = 0;
    alert(`${this.name}가 멈췄다.`);
  }

}

class Rabbit extends Animal {
  hide() {
    alert(`${this.name}가 숨었다!`);
  }

  stop() {
    super.stop(); // 부모 클래스의 stop을 호출해 멈추고,
    this.hide(); // 숨는다.
  }
}

let rabbit = new Rabbit("흰 토끼");

rabbit.run(5); // 흰 토끼가 속도 5로 달린다.
rabbit.stop(); // 흰 토끼가 멈췄다. 흰 토끼가 숨었다!
```

`Rabbit`은 이제 실행 중간에 부모 클래스에 정의된 메서드 `super.stop()`을 호출하는 `stop`을 가지게 되었다.

**화살표 함수엔 `super`가 없다.**

[화살표 함수 다시 살펴보기](https://ko.javascript.info/arrow-functions)에서 살펴본 바와 같이, 화살표 함수는 `super`를 지원하지 않는다.

`super`에 접근하면 아래 예시와 같이 `super`를 외부 함수에서 가져온다.

```javascript
class Rabbit extends Animal {
  stop() {
    setTimeout(() => super.stop(), 1000); // 1초 후에 부모 stop을 호출한다.
  }
}
```

화살표 함수의 `super`는 `stop()`의 `super`와 같아서 위 예시는 의도한 대로 동작한다. 그렇지만 `setTimeout`안에서 ‘일반’ 함수를 사용했다면 에러가 발생했을 것이다.

```javascript
// Unexpected super
setTimeout(function() { super.stop() }, 1000);
```



#### 생성자 오버라이딩

생성자 오버라이딩은 좀 더 까다로운데, 지금까진 `Rabbit`에 자체 `constructor`가 없었다.

[명세서](https://tc39.github.io/ecma262/#sec-runtime-semantics-classdefinitionevaluation)에 따르면, 클래스가 다른 클래스를 상속받고 `constructor`가 없는 경우엔 아래처럼 ‘비어있는’ `constructor`가 만들어진다.

```javascript
class Rabbit extends Animal {
  // 자체 생성자가 없는 클래스를 상속받으면 자동으로 만들어짐
  constructor(...args) {
    super(...args);
  }
}
```

보시다시피 생성자는 기본적으로 부모 `constructor`를 호출한다. 이때 부모 `constructor`에도 인수를 모두 전달한다. 클래스에 자체 생성자가 없는 경우엔 이런 일이 모두 자동으로 일어난다.

이제 `Rabbit`에 커스텀 생성자를 추가해보고, 커스텀 생성자에서 `name`과 `earLength`를 지정해보자.

```javascript
class Animal {
  constructor(name) {
    this.speed = 0;
    this.name = name;
  }
  // ...
}

class Rabbit extends Animal {

  constructor(name, earLength) {
    this.speed = 0;
    this.name = name;
    this.earLength = earLength;
  }

  // ...
}

// 동작하지 않습니다!
let rabbit = new Rabbit("흰 토끼", 10); // ReferenceError: Must call super constructor in derived class before accessing 'this' or returning from derived constructor
```

에러가 발생하며, 토끼를 만들 수 없다. 무엇이 잘못된 걸까?



- **상속 클래스의 생성자에선 반드시 `super(...)`를 호출해야 하는데, `super(...)`를 호출하지 않아 에러가 발생했다. `super(...)`는 `this`를 사용하기 전에 반드시 호출해야 한다.**

그런데 왜 `super(...)`를 호출해야 하는 걸까?

당연히 이유가 있다. 상속 클래스의 생성자가 호출될 때 어떤 일이 일어나는지 알아보며 이유를 찾아보자.

자바스크립트는 '상속 클래스의 생성자 함수(derived constructor)'와 그렇지 않은 생성자 함수를 구분한다. 상속 클래스의 생성자 함수엔 특수 내부 프로퍼티인 `[[ConstructorKind]]:"derived"`가 이름표처럼 붙는다.

일반 클래스의 생성자 함수와 상속 클래스의 생성자 함수 간 차이는 `new`와 함께 드러난다.

- 일반 클래스가 `new`와 함께 실행되면, 빈 객체가 만들어지고 `this`에 이 객체를 할당한다.
- 반면, 상속 클래스의 생성자 함수가 실행되면, 일반 클래스에서 일어난 일이 일어나지 않는다. 상속 클래스의 생성자 함수는 빈 객체를 만들고 `this`에 이 객체를 할당하는 일을 부모 클래스의 생성자가 처리해주길 기대한다.

이런 차이 때문에 상속 클래스의 생성자에선 `super`를 호출해 부모 생성자를 실행해 주어야 한다. 그렇지 않으면 `this`가 될 객체가 만들어지지 않아 에러가 발생한다.

아래 예시와 같이 `this`를 사용하기 전에 `super()`를 호출하면 `Rabbit`의 생성자가 제대로 동작한다.

```javascript
class Animal {

  constructor(name) {
    this.speed = 0;
    this.name = name;
  }

  // ...
}

class Rabbit extends Animal {

  constructor(name, earLength) {
    super(name);
    this.earLength = earLength;
  }

  // ...
}

// 이제 에러 없이 동작한다.
let rabbit = new Rabbit("흰 토끼", 10);
alert(rabbit.name); // 흰 토끼
alert(rabbit.earLength); // 10
```



#### 클래스 필드 오버라이딩: 까다로운 내용

**사전 공지**

이 내용은 자바스크립트 이외의 언어에서 클래스를 사용해 본 경험이 있다는 전제하에 진행된다.

여기서 다룰 내용을 잘 습득하면 프로그래밍 언어에 대한 통찰력이 높아지고 드물지만, 버그를 만드는 프로그래밍 습관에 대해 알 수 있다.

오버라이딩은 메서드뿐만 아니라 클래스 필드를 대상으로도 적용할 수 있다.

부모 클래스의 생성자 안에 있는 오바라이딩한 필드에 접근하려고 할 때 자바스크립트는 다른 프로그래밍 언어와는 다르게 조금 까다롭지만 말이다.

```javascript
class Animal {
  name = 'animal'

  constructor() {
    alert(this.name); // (*)
  }
}

class Rabbit extends Animal {
  name = 'rabbit';
}

new Animal(); // animal
new Rabbit(); // animal
```

`Animal`을 상속받는 `Rabbit`에서 `name` 필드를 오버라이딩 했다.

`Rabbit`에는 따로 생성자가 정의되어 있지 않기 때문에 `Rabbit`을 사용해 인스턴스를 만들면 `Animal`의 생성자가 호출된다.

흥미로운 점은 `new Animal()`과 `new Rabbit()`을 실행할 때 두 경우 모두 `(*)`로 표시한 줄에 있는 `alert` 함수가 실행되면서 얼럿 창에 `animal`이 출력된다는 점이다.

이를 통해 우리는 **‘부모 생성자는 자식 클래스에서 오버라이딩한 값이 아닌, 부모 클래스 안의 필드 값을 사용한다’** 는 사실을 알 수 있다.

상속을 받고 필드 값을 오버라이딩했는데 새로운 값 대신 부모 클래스 안에 있는 기존 필드 값을 사용하다니 이상하다.

이해를 돕기 위해 이 상황을 메서드와 비교해 생각해보자.

아래 예시에선 필드 `this.name` 대신에 메서드 `this.showName()`을 사용했다.

```javascript
class Animal {
  showName() {  // this.name = 'animal' 대신 메서드 사용
    alert('animal');
  }

  constructor() {
    this.showName(); // alert(this.name); 대신 메서드 호출
  }
}

class Rabbit extends Animal {
  showName() {
    alert('rabbit');
  }
}

new Animal(); // animal
new Rabbit(); // rabbit
```

필드를 오버라이딩한 위쪽 예시와 결과가 다르다.

위와 같이 자식 클래스에서 부모 생성자를 호출하면 오버라이딩한 메서드가 실행되어야 한다. 이게 우리가 원하던 결과이기 때문이다.

그런데 클래스 필드는 자식 클래스에서 필드를 오버라이딩해도 부모 생성자가 오버라이딩한 필드 값을 사용하지 않는다. 부모 생성자는 항상 부모 클래스에 있는 필드의 값을 사용한다.

왜 이런 차이가 있을까?

이유는 필드 초기화 순서 때문이다. 클래스 필드는 다음과 같은 규칙에 따라 초기화 순서가 달라진다.

- 아무것도 상속받지 않는 베이스 클래스는 생성자 실행 이전에 초기화됨
- 부모 클래스가 있는 경우엔 `super()` 실행 직후에 초기화됨

위 예시에서 `Rabbit`은 하위 클래스이고 `constructor()`가 정의되어 있지 않다. 이런 경우 앞서 설명한 바와 같이 생성자는 비어있는데 그 안에 `super(...args)`만 있다고 보면 된다.

따라서 `new Rabbit()`을 실행하면 `super()`가 호출되고 그 결과 부모 생성자가 실행된다. 그런데 이때 하위 클래스 필드 초기화 순서에 따라 하위 클래스 `Rabbit`의 필드는 `super()` 실행 후에 초기화된다. 부모 생성자가 실행되는 시점에 `Rabbit`의 필드는 아직 존재하지 않다. 이런 이유로 필드를 오버라이딩 했을 때 `Animal`에 있는 필드가 사용된 것이다.

이렇게 자바스크립트는 오버라이딩시 필드와 메서드의 동작 방식이 미묘하게 다른데 다행히도 이런 문제는 오버라이딩한 필드를 부모 생성자에서 사용할 때만 발생한다. 이런 차이가 왜 발생하는지 모르면 결과를 해석할 수 없는 상황이 발생하기 때문에 별도의 공간을 만들어 필드 오버라이딩시 내부에서 벌어지는 일에 대해 자세히 알아보았다.

개발하다가 필드 오버라이딩이 문제가 되는 상황이 발생하면 필드 대신 메서드를 사용하거나 getter나 setter를 사용해 해결하면 된다.



> #### 요약
>
> 1. 클래스 확장하기
>
>    ```
>    class Child extends Parent
>    ```
>
>    - `Child.prototype.__proto__`가 `Parent.prototype`이 되므로 메서드 전체가 상속된다.
>
> 2. 생성자 오버라이딩:
>
>    - `this`를 사용하기 전에 `Child` 생성자 안에서 `super()`로 부모 생성자를 반드시 호출해야 한다.
>
> 3. 메서드 오버라이딩:
>
>    - `Child`에 정의된 메서드에서 `super.method()`를 사용해 `Parent`에 정의된 메서드를 사용할 수 있다.
>
> 4. super 키워드와 [[HomeObject]]
>
>    - 메서드는 내부 프로퍼티 `[[HomeObject]]`에 자신이 정의된 클래스와 객체를 기억해놓는다. `super`는 `[[HomeObject]]`를 사용해 부모 메서드를 찾는다.
>    - 따라서 `super`가 있는 메서드는 객체 간 복사 시 제대로 동작하지 않을 수 있다.
>
> 추가 사항:
>
> - 화살표 함수는 `this`나 `super`를 갖지 않으므로 주변 컨텍스트에 잘 들어맞는다.





## 9.3 정적 메서드와 정적 프로퍼티

`"prototype"`이 아닌 클래스 함수 자체에 메서드를 설정할 수도 있다. 이런 메서드를 *정적(static)* 메서드라고 부른다.

정적 메서드는 아래와 같이 클래스 안에서 `static` 키워드를 붙여 만들 수 있다.

```javascript
class User {
  static staticMethod() {
    alert(this === User);
  }
}

User.staticMethod(); // true
```

정적 메서드는 메서드를 프로퍼티 형태로 직접 할당하는 것과 동일한 일을 한다.

```javascript
class User { }

User.staticMethod = function() {
  alert(this === User);
};

User.staticMethod(); // true
```

`User.staticMethod()`가 호출될 때 `this`의 값은 클래스 생성자인 `User` 자체가 된다(점 앞 객체).

정적 메서드는 어떤 특정한 객체가 아닌 클래스에 속한 함수를 구현하고자 할 때 주로 사용된다.

객체 `Article`이 여러 개 있고 이들을 비교해줄 함수가 필요하다고 가정해 보자. 가장 먼저 아래와 같이 `Article.compare`를 추가하는 방법이 떠오를 것이다.

```javascript
class Article {
  constructor(title, date) {
    this.title = title;
    this.date = date;
  }

  static compare(articleA, articleB) {
    return articleA.date - articleB.date;
  }
}

// 사용법
let articles = [
  new Article("HTML", new Date(2019, 1, 1)),
  new Article("CSS", new Date(2019, 0, 1)),
  new Article("JavaScript", new Date(2019, 11, 1))
];

articles.sort(Article.compare);

alert( articles[0].title ); // CSS
```

여기서 `Article.compare`는 article(글)을 비교해주는 수단으로, 글 전체를 ‘위에서’ 바라보며 비교를 수행한다. `Article.compare`이 글 하나의 메서드가 아닌 클래스의 메서드여야 하는 이유가 여기에 있다.

이번에 살펴볼 예시는 ‘팩토리’ 메서드를 구현한 코드이다. 다양한 방법을 사용해 조건에 맞는 article 인스턴스를 만들어야 한다고 가정해 보자.

1. 매개변수(`title`, `date` 등)를 이용해 관련 정보가 담긴 article 생성
2. 오늘 날짜를 기반으로 비어있는 article 생성
3. 기타 등등

첫 번째 방법은 생성자를 사용해 구현할 수 있다. 두 번째 방법은 클래스에 정적 메서드를 만들어 구현할 수 있다.

아래 `Article.createTodays()`같이 말이다.

```javascript
class Article {
  constructor(title, date) {
    this.title = title;
    this.date = date;
  }

  static createTodays() {
    // this는 Article입니다.
    return new this("Today's digest", new Date());
  }
}

let article = Article.createTodays();

alert( article.title ); // Today's digest
```

이제 Today’s digest라는 글이 필요할 때마다 `Article.createTodays()`를 호출하면 ㅑ된다. 여기서도 마찬가지로 `Article.createTodays()`는 article의 메서드가 아닌 전체 클래스의 메서드이다.

정적 메서드는 아래 예시와 같이 항목 검색, 저장, 삭제 등을 수행해주는 데이터베이스 관련 클래스에도 사용된다.

```javascript
// Article은 article을 관리해주는 특별 클래스라고 가정하자.
// article 삭제에 쓰이는 정적 메서드
Article.remove({id: 12345});
```



#### 정적 프로퍼티

**최근에 추가됨**

스펙에 추가된 지 얼마 안 된 문법이며, 예시는 Chrome에서만 동작할 수 있다.

정적 프로퍼티도 물론 만들 수 있다. 정적 프로퍼티는 일반 클래스 프로퍼티와 유사하게 생겼는데 앞에 `static`이 붙는다는 점만 다르다.

```javascript
class Article {
  static publisher = "Ilya Kantor";
}

alert( Article.publisher ); // Ilya Kantor
```

위 예시는 `Article`에 프로퍼티를 직접 할당한 것과 동일하게 동작한다.

```javascript
Article.publisher = "Ilya Kantor";
```



#### 정적 프로퍼티와 메서드 상속

정적 프로퍼티와 메서드는 상속된다.

아래 예시에서 `Animal.compare`와 `Animal.planet`은 상속되어서 각각 `Rabbit.compare`와 `Rabbit.planet`에서 접근할 수 있다.

```javascript
class Animal {
  static planet = "지구";

  constructor(name, speed) {
    this.speed = speed;
    this.name = name;
  }

  run(speed = 0) {
    this.speed += speed;
    alert(`${this.name}가 속도 ${this.speed}로 달립니다.`);
  }

  static compare(animalA, animalB) {
    return animalA.speed - animalB.speed;
  }

}

// Animal을 상속받음
class Rabbit extends Animal {
  hide() {
    alert(`${this.name}가 숨었습니다!`);
  }
}

let rabbits = [
  new Rabbit("흰 토끼", 10),
  new Rabbit("검은 토끼", 5)
];

rabbits.sort(Rabbit.compare);

rabbits[0].run(); // 검은 토끼가 속도 5로 달립니다.

alert(Rabbit.planet); // 지구
```

이제 `Rabbit.compare`을 호출하면 `Animal.compare`가 호출된다.

이게 가능한 이유는 프로토타입 때문이다. 이미 예상하셨겠지만, `extends` 키워드는 `Rabbit`의 `[[Prototype]]`이 `Animal`을 참조하도록 해준다.

![Screen Shot 2021-07-10 at 10 55 55 AM](https://user-images.githubusercontent.com/79819941/125148485-855bc180-e16d-11eb-8375-2b574043445c.png) 

따라서 `Rabbit extends Animal`은 두 개의 `[[Prototype]]` 참조를 만들어 낸다.

1. 함수 `Rabbit`은 프로토타입을 통해 함수 `Animal`을 상속받는다.
2. `Rabbit.prototype`은 프로토타입을 통해 `Animal.prototype`을 상속받는다.

이런 과정이 있기 때문에 일반 메서드 상속과 정적 메서드 상속이 가능하다.

코드로 직접 확인해보자.

```javascript
class Animal {}
class Rabbit extends Animal {}

// 정적 메서드
alert(Rabbit.__proto__ === Animal); // true

// 일반 메서드
alert(Rabbit.prototype.__proto__ === Animal.prototype); // true
```



> #### 요약
>
> 정적 메서드는 특정 클래스 인스턴스가 아닌 클래스 '전체’에 필요한 기능을 만들 때 사용할 수 있다.
>
> 인스턴스끼리 비교해주는 메서드 `Article.compare(article1, article2)`이나 팩토리 메서드 `Article.createTodays()`를 만들 때 정적 메서드가 쓰인다.
>
> 정적 메서드는 클래스 선언부 안에 위치하고 앞에 `static`이라는 키워드가 붙는다.
>
> 정적 프로퍼티는 데이터를 클래스 수준에 저장하고 싶을 때 사용한다. 정적 프로퍼티 역시 개별 인스턴스에 묶이지 않는다.
>
> 문법:
>
> ```javascript
> class MyClass {
>   static property = ...;
> 
>   static method() {
>     ...
>   }
> }
> ```
>
> `static`을 사용한 선언은 기술적으론 클래스 자체에 직접 할당하는 것과 동일한다.
>
> ```javascript
> MyClass.property = ...
> MyClass.method = ...
> ```
>
> 정적 프로퍼티와 정적 메서드는 상속이 가능한다.
>
> `class B extends A`는 클래스 `B`의 프로토타입이 클래스 `A`를 가리키게 한다(`B.[[Prototype]] = A`). 따라서 `B`에서 원하는 프로퍼티나 메서드를 찾지 못하면 `A`로 검색이 이어진다.

