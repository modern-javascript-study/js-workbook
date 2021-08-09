## 9.4 private, protected 프로퍼티와 메서드

객체 지향 프로그래밍에서 가장 중요한 원리 중 하나는 '내부 인터페이스와 외부 인터페이스를 구분 짓는 것’인데, 단순히 'hello word’를 출력하는 것이 아닌 복잡한 애플리케이션을 구현하려면, 내부 인터페이스와 외부 인터페이스를 구분하는 방법을 ‘반드시’ 알고 있어야 한다.

잠시 개발을 벗어나 현실 세계로 눈을 돌려, 내부 인터페이스와 외부 인터페이스 구분이 무엇을 의미하는지 알아보자.

일상생활에서 접하게 되는 기계들은 꽤 복잡한 구조로 되어 있지만 내부인터페이스와 외부 인터페이스가 구분되어있기 때문에 문제없이 기계를 사용할 수 있다.



#### 실생활 예제

커피 머신을 예로 들어보자. 외형은 심플하다. 버튼 하나, 화면 하나, 구멍 몇 개 등이 있다.

![img](https://ko.javascript.info/article/private-protected-properties-methods/coffee.jpg) 

하지만 내부는 이렇게 생겼다고 한다(수리용 매뉴얼에서 가져온 사진).

![img](https://ko.javascript.info/article/private-protected-properties-methods/coffee-inside.jpg) 

뭔가 디테일한 것들이 아주 많지만 이 모든 것을 알지 못해도 커피 머신을 사용하는 데 지장이 없다.

커피 머신은 꽤 믿음직한 기계이다. 초기불량이 아닌 이상 보통 수년 간 사용할 수 있고, 중간에 고장이 나도 수리를 받으면 된다. 외형은 단순하지만 커피 머신을 신뢰할 수 있는 이유는 모든 세부 요소들이 기계 내부에 잘 정리되어 *숨겨져* 있기 때문이다.

커피 머신에서 보호 커버를 제거하면 사용법이 훨씬 복잡해지고 위험한 상황이 생길 수 있다. 어디를 눌러야 할지 모르고 감전이 될 수도 있기 때문이다. 앞으로 학습하겠지만, 프로그래밍에서 객체는 커피 머신과 같다.

프로그래밍에서는 보호 커버를 사용하는 대신 특별한 문법과 컨벤션을 사용해 안쪽 세부 사항을 숨긴다는 점이 다르다.



#### 내부 인터페이스와 외부 인터페이스

객체 지향 프로그래밍에서 프로퍼티와 메서드는 두 그룹으로 분류된다.

- *내부 인터페이스(internal interface)* – 동일한 클래스 내의 다른 메서드에선 접근할 수 있지만, 클래스 밖에선 접근할 수 없는 프로퍼티와 메서드
- *외부 인터페이스(external interface)* – 클래스 밖에서도 접근 가능한 프로퍼티와 메서드

커피 머신으로 비유하자면 기계 안쪽에 숨어있는 뜨거운 물이 지나가는 관이나 발열 장치 등이 내부 인터페이스가 될 수 있다.

내부 인터페이스의 세부사항들은 서로의 정보를 이용하여 객체를 동작시킨다. 발열 장치에 부착된 관을 통해 뜨거운 물이 이동하는 것처럼 말이다.그런데 커피 머신은 보호 커버에 둘러싸여 있기 때문에 보호 커버를 벗기지 않고는 커피머신 외부에서 내부로 접근할 수 없다. 밖에선 세부 요소를 알 수 없고, 접근도 불가능합니다. 내부 인터페이스의 기능은 외부 인터페이스를 통해야만 사용할 수 있다. 

이런 특징 때문에 외부 인터페이스만 알아도 객체를 가지고 무언가를 할 수 있다. 객체 안이 어떻게 동작하는지 알지 못해도 괜찮다는 점은 큰 장점으로 작용한다.

지금까진 내부 인터페이스와 외부 인터페이스의 개관에 대해 설명했다.



자바스크립트에는 아래와 같은 두 가지 타입의 객체 필드(프로퍼티와 메서드)가 있다.

- public: 어디서든지 접근할 수 있으며 외부 인터페이스를 구성한다. 지금까지 다룬 프로퍼티와 메서드는 모두 public이다.
- private: 클래스 내부에서만 접근할 수 있으며 내부 인터페이스를 구성할 때 쓰인다.

자바스크립트 이외의 다수 언어에서 클래스 자신과 자손 클래스에서만 접근을 허용하는 ‘protected’ 필드를 지원한다. protected 필드는 private과 비슷하지만, 자손 클래스에서도 접근이 가능하다는 점이 다르다. protected 필드도 내부 인터페이스를 만들 때 유용하다. 자손 클래스의 필드에 접근해야 하는 경우가 많기 때문에, protected 필드는 private 필드보다 조금 더 광범위하게 사용된다.

자바스크립트는 protected 필드를 지원하지 않지만, protected를 사용하면 편리한 점이 많기 때문에 이를 모방해서 사용하는 경우가 많다.

이제 지금까지 배운 프로퍼티 타입을 사용해 커피머신을 만들어면, 실제 커피 머신은 아주 복잡하지만, 여기선 간결성을 위해 간이 모델을 만들어 보도록 하자(원한다면 구현은 가능하다).



#### 프로퍼티 보호하기

먼저, 간단한 커피 머신 클래스를 만들어보자.

```javascript
class CoffeeMachine {
  waterAmount = 0; // 물통에 차 있는 물의 양

  constructor(power) {
    this.power = power;
    alert( `전력량이 ${power}인 커피머신을 만듭니다.` );
  }

}

// 커피 머신 생성
let coffeeMachine = new CoffeeMachine(100);

// 물 추가
coffeeMachine.waterAmount = 200;
```

현재 프로퍼티 `waterAmount`와 `power`는 public이다. 손쉽게 `waterAmount`와 `power`를 읽고 원하는 값으로 변경하기 쉬운 상태이다.

이제 `waterAmount`를 protected로 바꿔서 `waterAmount`를 통제해 보도록 하자. 예시로 `waterAmount`를 0 미만의 값으로는 설정하지 못하도록 만들어 보자구.

**protected 프로퍼티 명 앞엔 밑줄 `_`이 붙는다.**

자바스크립트에서 강제한 사항은 아니지만, 밑줄은 프로그래머들 사이에서 외부 접근이 불가능한 프로퍼티나 메서드를 나타낼 때 쓴다.

`waterAmount`에 밑줄을 붙여 protected 프로퍼티로 만들어주자.

```javascript
class CoffeeMachine {
  _waterAmount = 0;

  set waterAmount(value) {
    if (value < 0) throw new Error("물의 양은 음수가 될 수 없습니다.");
    this._waterAmount = value;
  }

  get waterAmount() {
    return this._waterAmount;
  }

  constructor(power) {
    this._power = power;
  }

}

// 커피 머신 생성
let coffeeMachine = new CoffeeMachine(100);

// 물 추가
coffeeMachine.waterAmount = -10; // Error: 물의 양은 음수가 될 수 없다.
```

이제 물의 양을 0 미만으로 설정하면 실패한다.



#### 읽기 전용 프로퍼티

`power` 프로퍼티를 읽기만 가능하도록 만들어보면, 프로퍼티를 생성할 때만 값을 할당할 수 있고, 그 이후에는 값을 절대 수정하지 말아야 하는 경우가 종종 있는데, 이럴 때 읽기 전용 프로퍼티를 활용할 수 있다.

커피 머신의 경우에는 전력이 이에 해당한다.

읽기 전용 프로퍼티를 만들려면 setter(설정자)는 만들지 않고 getter(획득자)만 만들어야 한다.

```javascript
class CoffeeMachine {
  // ...

  constructor(power) {
    this._power = power;
  }

  get power() {
    return this._power;
  }

}

// 커피 머신 생성
let coffeeMachine = new CoffeeMachine(100);

alert(`전력량이 ${coffeeMachine.power}인 커피머신을 만듭니다.`); // 전력량이 100인 커피머신을 만듭니다.

coffeeMachine.power = 25; // Error (setter 없음)
```

**getter와 setter 함수**

위에서는 get, set 문법을 사용해서 getter와 setter 함수를 만들었지만 대부분은 아래와 같이 `get.../set...` 형식의 함수가 선호된다.

```javascript
class CoffeeMachine {
  _waterAmount = 0;

  setWaterAmount(value) {
    if (value < 0) throw new Error("물의 양은 음수가 될 수 없습니다.");
    this._waterAmount = value;
  }

  getWaterAmount() {
    return this._waterAmount;
  }
}

new CoffeeMachine().setWaterAmount(100);
```

다소 길어보이긴 하지만, 이렇게 함수를 선언하면 다수의 인자를 받을 수 있기 때문에 좀 더 유연하다.

반면 get, set 문법을 사용하면 코드가 짧아진다는 장점이 있다. 어떤걸 사용해야 한다는 규칙은 없으므로 원하는 방식을 선택해서 사용하자.

**protected 필드는 상속된다.**

`class MegaMachine extends CoffeeMachine`로 클래스를 상속받으면, 새로운 클래스의 메서드에서 `this._waterAmount`나 `this._power`를 사용해 프로퍼티에 접근할 수 있다.

이렇게 protected 필드는 아래에서 보게 될 private 필드와 달리, 자연스러운 상속이 가능하다.



#### private 프로퍼티

**최근에 추가됨**

스펙에 추가된 지 얼마 안 된 문법이다. 이 문법을 지원하지 않거나 부분적으로만 지원하는 엔진을 사용 중이라면 폴리필을 구현해야 한다.

private 프로퍼티와 메서드는 제안(proposal) 목록에 등재된 문법으로, 명세서에 등재되기 직전 상태이고, private 프로퍼티와 메서드는 `#`으로 시작합니다. `#`이 붙으면 클래스 안에서만 접근할 수 있다.

물 용량 한도를 나타내는 private 프로퍼티 `#waterLimit`과 남아있는 물의 양을 확인해주는 private 메서드 `#checkWater`를 구현해보자.

```javascript
class CoffeeMachine {
  #waterLimit = 200;

  #checkWater(value) {
    if (value < 0) throw new Error("물의 양은 음수가 될 수 없습니다.");
    if (value > this.#waterLimit) throw new Error("물이 용량을 초과합니다.");
  }

}

let coffeeMachine = new CoffeeMachine();

// 클래스 외부에서 private에 접근할 수 없음
coffeeMachine.#checkWater(); // Error
coffeeMachine.#waterLimit = 1000; // Error
```

`#`은 자바스크립트에서 지원하는 문법으로, private 필드를 의미한다. private 필드는 클래스 외부나 자손 클래스에서 접근할 수 없다. private 필드는 public 필드와 상충하지 않는다. private 프로퍼티 `#waterAmount`와 public 프로퍼티 `waterAmount`를 동시에 가질 수 있다.

`#waterAmount`의 접근자 `waterAmount`를 만들어보자.

```javascript
class CoffeeMachine {

  #waterAmount = 0;

  get waterAmount() {
    return this.#waterAmount;
  }

  set waterAmount(value) {
    if (value < 0) throw new Error("물의 양은 음수가 될 수 없습니다.");
    this.#waterAmount = value;
  }
}

let machine = new CoffeeMachine();

machine.waterAmount = 100;
alert(machine.#waterAmount); // Error
```

protected 필드와 달리, private 필드는 언어 자체에 의해 강제된다는 점이 장점이다.

그런데 `CoffeeMachine`을 상속받는 클래스에선 `#waterAmount`에 직접 접근할 수 없다. `#waterAmount`에 접근하려면 `waterAmount`의 getter와 setter를 통해야 한다.

```javascript
class MegaCoffeeMachine extends CoffeeMachine {
  method() {
    alert( this.#waterAmount ); // Error: CoffeeMachine을 통해서만 접근할 수 있다.
  }
}
```

다양한 시나리오에서 이런 제약사항은 너무 엄격하다. `CoffeeMachine`을 상속받는 클래스에선 `CoffeeMachine`의 내부에 접근해야 하는 정당한 사유가 있을 수 있기 때문이다. 언어 차원에서 protected 필드를 지원하지 않아도 더 자주 쓰이는 이유가 바로 여기에 있다.

**private 필드는 this[name]로 사용할 수 없다.**

private 필드는 특별하다.

알다시피, 보통은 `this[name]`을 사용해 필드에 접근할 수 있다.

```javascript
class User {
  ...
  sayHi() {
    let fieldName = "name";
    alert(`Hello, ${this[fieldName]}`);
  }
}
```

하지만 private 필드는 `this[name]`으로 접근할 수 없다. 이런 문법적 제약은 필드의 보안을 강화하기 위해 만들어졌다.





> #### 요약
>
> 객체 지향 프로그래밍에선 내부 인터페이스와 외부 인터페이스를 구분하는 것을 [캡슐화(encapsulation)]라는 용어를 사용해 설명한다.
>
> 좋은 예시를 통해 캡슐화는 이점은 다음과 같다.
>
> - 사용자가 자신의 발등을 찍지 않도록 보호
>
>   커피 머신를 함께 사용하는 개발팀이 있다고 상상해보자. "Best CoffeeMachine"이라는 회사에서 만든 이 커피 머신은 현재 잘 작동하고 있지만, 보호 커버가 없어서 내부 인터페이스가 노출되어있는 상황이다. 교양있는 팀원들은 모두 설계 의도에 맞게 커피 머신을 사용한다. 그런데 어느 날 John이라는 개발자가 자신의 능력을 과신하며 커피 머신 내부를 살짝 만지게 되고 이틀 후, 커피 머신은 고장이 나버렸다 .커피 머신이 고장 난 건 John의 잘못이라기보다는, 보호 커버를 없애고 John이 마음대로 조작하도록 내버려 둔 사람의 잘못이다. 프로그래밍에서도 마찬가지이다. 외부에서 의도치 않게 클래스를 조작하게 되면 그 결과는 예측할 수 없게 된다.
>
> - 지원 가능
>
>   실제 개발 과정에서 일어나는 상황은 커피 머신 사례보다 훨씬 복잡하다. 커피 머신은 한번 구매하면 끝이지만 실제 코드는 유지보수가 끊임없이 일어나기 때문이다.**내부 인터페이스를 엄격하게 구분하면, 클래스 개발자들은 사용자에게 알리지 않고도 자유롭게 내부 프로퍼티와 메서드들을 수정할 수 있다.**내부 인터페이스가 엄격히 구분된 클래스를 만지고 있다면, 그 어떤 외부 코드도 내부 private 메서드에 의존하고 있지 않기 때문에 private 메서드의 이름을 안전하게 바꿀 수 있고, 매개변수를 변경하거나 없앨 수도 있다는 것을 알아 두면 되는 것이다. 사용자 입장에선 새로운 버전이 출시되면서 내부 정비가 전면적으로 이뤄졌더라도 외부 인터페이스만 똑같다면 업그레이드가 용이하다는 장점이 있다.
>
> - 복잡성 은닉
>
>   사람들은 간단한 것을 좋아한다. 내부는 간단치 않더라도 최소한 외형은 간단해야 한다.프로그래머들도 예외는 아니다.**구현 세부 사항이 숨겨져 있으면 간단하고 편리해지고 외부 인터페이스에 대한 설명도 문서화하기 쉬워진다.**
>
> 내부 인터페이스를 숨기려면 protected나 private 프로퍼티를 사용하면 된다.
>
> - protected 필드는 `_`로 시작한다. `_`은 자바스크립트에서 지원하는 문법은 아니지만, protected 필드를 나타낼 때 관습처럼 사용된다. 개발자는 protected 프로퍼티가 정의된 클래스와 해당 클래스를 상속받는 클래스에서만 `_`가 붙은 필드에 접근해야 한다.
> - private 필드는 `#`로 시작하며, 자바스크립트에서 지원하는 문법이다. `#`로 시작하는 필드는 해당 필드가 정의된 클래스 내부에서만 접근 가능하다.
>
> 모든 브라우저에서 private 필드를 지원하진 않지만 폴리필을 구현하여 사용할 수 있다.



## 9.5 내장 클래스 확장하기

배열, 맵 같은 내장 클래스도 확장 가능하다.  아래 예시에서 `PowerArray`는 기본 `Array`를 상속받아 만들었다.

```javascript
// 메서드 하나를 추가한다(더 많이 추가하는 것도 가능).
class PowerArray extends Array {
  isEmpty() {
    return this.length === 0;
  }
}

let arr = new PowerArray(1, 2, 5, 10, 50);
alert(arr.isEmpty()); // false

let filteredArr = arr.filter(item => item >= 10);
alert(filteredArr); // 10, 50
alert(filteredArr.isEmpty()); // false
```

뭔가 흥미로운 점이 하나 보이는데, `filter`, `map` 등의 내장 메서드가 상속받은 클래스인 `PowerArray`의 인스턴스(객체)를 반환한다. 이 객체를 구현할 땐 내부에서 객체의 `constructor` 프로퍼티를 사용한다.

따라서 아래와 같은 관계를 갖는다.

```javascript
arr.constructor === PowerArray
```

`arr.filter()`가 호출될 때, 내부에선 기본 `Array`가 아닌 `arr.constructor`를 기반으로 새로운 배열이 만들어지고 여기에 필터 후 결과가 담긴다. 이렇게 되면 `PowerArray`에 구현된 메서드를 사용할 수 있다는 장점이 생긴다.

물론 동작 방식을 변경할 수 있다.

특수 정적 getter인 `Symbol.species`를 클래스에 추가할 수 있는데, `Symbol.species`가 있으면 `map`, `filter` 등의 메서드를 호출할 때 만들어지는 개체의 생성자를 지정할 수 있는데 원하는 생성자를 반환하기만 하면 되기 때문이다.

`map`이나 `filter`같은 내장 메서드가 일반 배열을 반환하도록 하려면 아래 예시처럼 `Symbol.species`가 `Array`를 반환하도록 해주면 된다.

```javascript
class PowerArray extends Array {
  isEmpty() {
    return this.length === 0;
  }

  // 내장 메서드는 반환 값에 명시된 클래스를 생성자로 사용한다.
  static get [Symbol.species]() {
    return Array;
  }
}

let arr = new PowerArray(1, 2, 5, 10, 50);
alert(arr.isEmpty()); // false

// filter는 arr.constructor[Symbol.species]를 생성자로 사용해 새로운 배열을 만든다.
let filteredArr = arr.filter(item => item >= 10);

// filteredArr는 PowerArray가 아닌 Array의 인스턴스이다.
alert(filteredArr.isEmpty()); // Error: filteredArr.isEmpty is not a function
```

보시다시피 이제 `.filter`가 `Array`를 반환한다. 따라서 더는 확장 기능이 전달되지 않는다.

**다른 컬렉션도 유사하게 동작한다.**

`Map`, `Set` 같은 컬렉션도 위와 같이 동작하고 이 컬렉션들도 `Symbol.species`를 사용한다.



#### 내장 객체와 정적 메서드 상속

내장 객체는 `Object.keys`, `Array.isArray` 등의 자체 정적 메서드를 갖는다.

앞서 학습한 바와 같이 네이티브 클래스들은 서로 상속 관계를 맺는데 `Array`는 `Object`를 상속받는다.

일반적으론 한 클래스가 다른 클래스를 상속받으면 정적 메서드와 그렇지 않은 메서드 모두를 상속받는다. 그런데 내장 클래스는 다르다. 내장클래스는 정적 메서드를 상속받지 못한다.

예를 들어보면, `Array`와 `Date`는 모두 `Object`를 상속받기 때문에 두 클래스의 인스턴스에선 `Object.prototype`에 구현된 메서드를 사용할 수 있다. 그런데 `Array.[[Prototype]]`와 `Date.[[Prototype]]`은 `Object`를 참조하지 않기 때문에 `Array.keys()`나 `Date.keys()`같은 정적 메서드를 인스턴스에서 사용할 수 없다.





# 9.6 'instanceof'로 클래스 확인하기

`instanceof` 연산자를 사용하면 객체가 특정 클래스에 속하는지 아닌지를 확인할 수 있는데, `instanceof`는 상속 관계도 확인해준다. 확인 기능은 다양한 곳에서 쓰이는데, 이번 챕터에선 `instanceof`를 사용해 인수의 타입에 따라 이를 다르게 처리하는 *다형적인(polymorphic)* 함수를 만드는데 사용해보자.



#### instanceof 연산자

문법:

```javascript
obj instanceof Class
```

`obj`가 `Class`에 속하거나 `Class`를 상속받는 클래스에 속하면 `true`가 반환된다.

예시:

```javascript
class Rabbit {}
let rabbit = new Rabbit();

// rabbit이 클래스 Rabbit의 객체냐?
alert( rabbit instanceof Rabbit ); // true
```

`instanceof`는 생성자 함수에서도 사용할 수 있다.

```javascript
// 클래스가 아닌 생성자 함수
function Rabbit() {}

alert( new Rabbit() instanceof Rabbit ); // true
```

`Array` 같은 내장 클래스에도 사용할 수 있다.

```javascript
let arr = [1, 2, 3];
alert( arr instanceof Array ); // true
alert( arr instanceof Object ); // true
```

위 예시에서 `arr`은 클래스 `Object`에도 속한다는 점에 주목하자. `Array`는 프로토타입 기반으로 `Object`를 상속받는다.

`instanceof` 연산자는 보통, 프로토타입 체인을 거슬러 올라가며 인스턴스 여부나 상속 여부를 확인한다. 그런데 정적 메서드 `Symbol.hasInstance`을 사용하면 직접 확인 로직을 설정할 수도 있다.

`obj instanceof Class`은 대략 아래와 같은 알고리즘으로 동작한다.

1. 클래스에 정적 메서드 `Symbol.hasInstance`가 구현되어 있으면, `obj instanceof Class`문이 실행될 때, `Class[Symbol.hasInstance](obj)`가 호출된다. 호출 결과는 `true`나 `false`이어야 한다. 이런 규칙을 기반으로 `instanceof`의 동작을 커스터마이징 할 수 있다.

   예시:

   ```javascript
   // canEat 프로퍼티가 있으면 animal이라고 판단할 수 있도록
   // instanceOf의 로직을 직접 설정한다.
   class Animal {
     static [Symbol.hasInstance](obj) {
       if (obj.canEat) return true;
     }
   }
   
   let obj = { canEat: true };
   
   alert(obj instanceof Animal); // true, Animal[Symbol.hasInstance](obj)가 호출됨
   ```

2. 그런데, 대부분의 클래스엔 `Symbol.hasInstance`가 구현되어있지 않다. 이럴 땐 일반적인 로직이 사용된다. `obj instanceOf Class`는 `Class.prototype`이 `obj` 프로토타입 체인 상의 프로토타입 중 하나와 일치하는지 확인하며,

   비교는 차례 차례 진행된다.

   ```javascript
   obj.__proto__ === Class.prototype?
   obj.__proto__.__proto__ === Class.prototype?
   obj.__proto__.__proto__.__proto__ === Class.prototype?
   ...
   // 이 중 하나라도 true라면 true를 반환한다.
   // 그렇지 않고 체인의 끝에 도달하면 false를 반환한다.
   ```

   위 예시에서 `rabbit.__proto__ === Rabbit.prototype`가 `true`이기 때문에 `instanceof`는 `true`를 반환한다.

   상속받은 클래스를 사용하는 경우엔 두 번째 단계에서 일치 여부가 확인된다.

   ```javascript
   class Animal {}
   class Rabbit extends Animal {}
   
   let rabbit = new Rabbit();
   alert(rabbit instanceof Animal); // true
   
   // rabbit.__proto__ === Rabbit.prototype
   // rabbit.__proto__.__proto__ === Animal.prototype (일치!)
   ```



아래 그림은 `rabbit instanceof Animal`을 실행했을 때 `Animal.prototype`과 비교되는 대상들을 보여준다.

![Screen Shot 2021-07-26 at 4 26 50 PM](https://user-images.githubusercontent.com/79819941/126949571-4aebacb0-1311-4c5b-b848-6c6ed3ddd7fa.png) 

한편, `objA`가 `objB`의 프로토타입 체인 상 어딘가에 있으면 `true`를 반환해주는 메서드, [objA.isPrototypeOf(objB)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/object/isPrototypeOf)도 있다. `obj instanceof Class`는 `Class.prototype.isPrototypeOf(obj)`와 동일하다.

`isPrototypeOf`는 `Class` 생성자를 제외하고 포함 여부를 검사하는 점이 조금 특이한데, 검사 시, 프로토타입 체인과 `Class.prototype`만 고려한다.

`isPrototypeOf`의 이런 특징은 객체 생성 후 `prototype` 프로퍼티가 변경되는 경우 아래처럼 특이한 결과를 초래하기도 한다.

예시:

```javascript
function Rabbit() {}
let rabbit = new Rabbit();

// 프로토타입이 변경됨
Rabbit.prototype = {};

// 더 이상 Rabbit이 아니다!
alert( rabbit instanceof Rabbit ); // false
```



#### 보너스: 타입 확인을 위한 Object.prototype.toString

일반 객체를 문자열로 변화하면 `[object Object]`가 된다는 것을 알고 있다.

```javascript
let obj = {};

alert(obj); // [object Object]
alert(obj.toString()); // 같은 결과가 출력됨
```

이렇게 `[object Object]`가 되는 이유는 `toString`의 구현방식 때문이다. 그런데 `toString`엔 `toString`을 더 강력하게 만들어주는 기능이 숨겨져 있습니다. `toString`의 숨겨진 기능을 사용하면 확장 `typeof`, `instanceof`의 대안을 만들 수 있다.

[명세서](https://tc39.github.io/ecma262/#sec-object.prototype.tostring)에 따르면, 객체에서 내장 `toString`을 추출하는 게 가능합니다. 이렇게 추출한 메서드는 모든 값을 대상으로 실행할 수 있다. 호출 결과는 값에 따라 달라진다.

- 숫자형 – `[object Number]`
- 불린형 – `[object Boolean]`
- `null` – `[object Null]`
- `undefined` – `[object Undefined]`
- 배열 – `[object Array]`
- 그외 – 커스터마이징 가능

예시:

```javascript
// 편의를 위해 toString 메서드를 변수에 복사함
let objectToString = Object.prototype.toString;

// 아래 변수의 타입은 무엇일까?
let arr = [];

alert( objectToString.call(arr) ); // [object Array]
```

[call/apply와 데코레이터, 포워딩](https://ko.javascript.info/call-apply-decorators) 챕터에서 설명한 [call](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/function/call)을 사용해 컨텍스트를 `this=arr`로 설정하고 함수 `objectToString`를 실행하였다.

`toString` 알고리즘은 내부적으로 `this`를 검사하고 상응하는 결과를 반환한다.

```javascript
let s = Object.prototype.toString;

alert( s.call(123) ); // [object Number]
alert( s.call(null) ); // [object Null]
alert( s.call(alert) ); // [object Function]
```



#### Symbol.toStringTag

특수 객체 프로퍼티 `Symbol.toStringTag`를 사용하면 `toString`의 동작을 커스터마이징 할 수 있다.

예시:

```javascript
let user = {
  [Symbol.toStringTag]: "User"
};

alert( {}.toString.call(user) ); // [object User]
```

대부분의 호스트 환경은 자체 객체에 이와 유사한 프로퍼티를 구현해 놓고 있다. 브라우저 관련 예시 몇 가지를 살펴보자.

```javascript
// 특정 호스트 환경의 객체와 클래스에 구현된 toStringTag
alert( window[Symbol.toStringTag]); // Window
alert( XMLHttpRequest.prototype[Symbol.toStringTag] ); // XMLHttpRequest

alert( {}.toString.call(window) ); // [object Window]
alert( {}.toString.call(new XMLHttpRequest()) ); // [object XMLHttpRequest]
```

실행 결과에서 보듯이 호스트 환경 고유 객체의 `Symbol.toStringTag` 값은 `[object ...]`로 쌓여진 값과 동일하다.

이처럼 ‘typeof’ 연산자의 강력한 변형들(`toString`과 `toStringTag` – 옮긴이)은 원시 자료형뿐만 아니라 내장 객체에도 사용할 수 있다. 그리고 커스터마이징까지 가능하다.

내장 객체의 타입 확인을 넘어서 타입을 문자열 형태로 받고 싶다면 `instanceof` 대신, `{}.toString.call`을 사용할 수 있다.



> #### 요약
>
> 지금까지 배운 타입 확인 메서드를 요약하면 다음과 같다.
>
> |               | 동작 대상                                           | 반환값       |
> | :------------ | :-------------------------------------------------- | :----------- |
> | `typeof`      | 원시형                                              | 문자열       |
> | `{}.toString` | 원시형, 내장 객체, `Symbol.toStringTag`가 있는 객체 | 문자열       |
> | `instanceof`  | 객체                                                | true나 false |
>
> 예시에서 보았듯이 `{}.toString`은 `typeof`보다 ‘기능이 더’ 많다.
>
> `instanceof` 연산자는 계층 구조를 가진 클래스를 다룰 때나 클래스의 상속 여부를 확인하고자 할 때 그 진가를 발휘한다.

# 9.7 믹스인

자바스크립트는 단일상속만을 허용하는 언어이다. 객체엔 단 하나의 `[[Prototype]]`만 있을 수 있고, 클래스는 클래스 하나만 상속받을 수 있다. 그런데 가끔 이런 제약이 한계처럼 느껴질 때가 있는데 예시를 들어보면, 클래스 `StreetSweeper`(도시의 거리를 청소하는 차량 – 옮긴이)와 클래스 `Bicycle`이 있는데, 이 둘을 섞어 `StreetSweepingBicycle`를 만들고 싶다고 해보자.

또는 클래스 `User`와 이벤트를 생성해주는 코드가 담긴 클래스 `EventEmitter`가 있는데, `EventEmitter`의 기능을 `User`에 추가해 사용자가 이벤트를 내뿜을 수 있게(emit) 해주고 싶다고 해보자. 이럴 때 믹스인이라 불리는 개념을 사용하면 도움이 된다.

Wikipedia에선 [믹스인(mixin)](https://en.wikipedia.org/wiki/Mixin)을 다른 클래스를 상속받을 필요 없이 이들 클래스에 구현되어있는 메서드를 담고 있는 클래스라고 정의한다. 다시 말해서 *믹스인*은 특정 행동을 실행해주는 메서드를 제공하는데 단독으로 쓰이지 않고 다른 클래스에 행동을 더해주는 용도로 사용된다.

## 믹스인 예시

자바스크립트에서 믹스인을 구현할 수 있는 가장 쉬운 방법은 유용한 메서드 여러 개가 담긴 객체를 하나 만드는 것이다. 이렇게 하면 다수의 메서드를 원하는 클래스의 프로토타입에 쉽게 병합할 수 있는데, 아래 예시의 믹스인 `sayHiMixin`은 `User`에게 '언어 능력’을 부여해준다.

```javascript
// 믹스인
let sayHiMixin = {
  sayHi() {
    alert(`Hello ${this.name}`);
  },
  sayBye() {
    alert(`Bye ${this.name}`);
  }
};

// 사용법:
class User {
  constructor(name) {
    this.name = name;
  }
}

// 메서드 복사
Object.assign(User.prototype, sayHiMixin);

// 이제 User가 인사를 할 수 있다.
new User("Dude").sayHi(); // Hello Dude!
```

상속 없이 메서드만 간단히 복사했다. 믹스인을 활용하면 `User`가 아래 예시처럼 다른 클래스를 상속받는 동시에, 믹스인에 구현된 추가 메서드도 사용할 수 있다.

```javascript
class User extends Person {
  // ...
}

Object.assign(User.prototype, sayHiMixin);
```

믹스인 안에서 믹스인 상속을 사용하는 것도 가능한데, 아래 예시에서 `sayHiMixin`은 `sayMixin`을 상속받는다.

```javascript
let sayMixin = {
  say(phrase) {
    alert(phrase);
  }
};

let sayHiMixin = {
  __proto__: sayMixin, // (Object.create를 사용해 프로토타입을 설정할 수도 있다.)

  sayHi() {
    // 부모 메서드 호출
    super.say(`Hello ${this.name}`); // (*)
  },
  sayBye() {
    super.say(`Bye ${this.name}`); // (*)
  }
};

class User {
  constructor(name) {
    this.name = name;
  }
}

// 메서드 복사
Object.assign(User.prototype, sayHiMixin);

// 이제 User가 인사를 할 수 있다.
new User("Dude").sayHi(); // Hello Dude!
```

`sayHiMixin`에서 부모 메서드 `super.say()`를 호출하면(`(*)`로 표시한 줄) 클래스가 아닌 `sayHiMixin`의 프로토타입에서 메서드를 찾는다는 점에 주목하자.

<img width="538" alt="Screen Shot 2021-08-03 at 9 59 56 PM" src="https://user-images.githubusercontent.com/79819941/128019742-6e81cdcb-080b-46fb-98e3-3b159aff2356.png"> 

이는 `sayHi`와 `sayBye`가 생성된 곳이 `sayHiMixin`이기 때문이다. 따라서 메서드를 복사했더라도, 이 메서드들의 내부 프로퍼티인 `[[HomeObject]]`는 위 그림처럼 `sayHiMixin`을 참조하게 된다. 메서드의 `super`가 `[[HomeObject]].[[Prototype]]`내에서 부모 메서드를 찾기 때문에, 메서드는 `User.[[Prototype]]`이 아닌 `sayHiMixin.[[Prototype]]`을 검색한다.



## 이벤트 믹스인

실제로 사용할 수 있는 믹스인을 만들어 보면,  상당수 브라우저 객체는 이벤트를 생성이라는 중요한 기능을 가지고 있다. 이벤트는 정보를 필요로 하는 곳에 ‘정보를 널리 알리는(broadcast)’ 훌륭한 수단인데, 아래 예시에선 클래스나 객체에 이벤트 관련 함수를 쉽게 추가할 수 있도록 해주는 믹스인을 만들어 보자.

- 믹스인은 뭔가 중요한 일이 발생했을 때 ‘이벤트를 생성하는’ 메서드, `.trigger(name, [...data])`를 제공하며, 인수 `name`은 이벤트 이름이고, 뒤따르는 선택 인수는 이벤트 데이터 정보를 담는다.
- 메서드 `.on(name, handler)`은 `name`에 해당하는 이벤트에 리스너로 `handler` 함수를 추가한다. `.on()`은 이벤트(`name`)가 트리거 될 때 호출되고, `.trigger` 호출에서 인수를 얻는다.
- 메서드 `.off(name, handler)`는 `handler` 리스너를 제거한다.

믹스인을 추가하면, 사용자가 로그인할 때 객체 `user`가 `"login"`이라는 이벤트를 생성할 수 있게 된다. 또 다른 객체 `calendar`는 `user`가 생성한 이벤트인 `"login"`을 듣고 사용자에 맞는 달력을 보여줄 수 있을 것이다.

메뉴의 항목을 선택했을 때 객체 `menu`가 `"select"`라는 이벤트를 생성하고, 다른 객체는 `"select"`에 반응하는 이벤트 핸들러를 할당할 수도 있을 것이다. 이벤트 믹스인은 이런 용도로 활용 가능하다.

이벤트 믹스인을 구현해보자.

```javascript
let eventMixin = {
  /**
   *  이벤트 구독
   *  사용패턴: menu.on('select', function(item) { ... }
  */
  on(eventName, handler) {
    if (!this._eventHandlers) this._eventHandlers = {};
    if (!this._eventHandlers[eventName]) {
      this._eventHandlers[eventName] = [];
    }
    this._eventHandlers[eventName].push(handler);
  },

  /**
   *  구독 취소
   *  사용패턴: menu.off('select', handler)
   */
  off(eventName, handler) {
    let handlers = this._eventHandlers?.[eventName];
    if (!handlers) return;
    for (let i = 0; i < handlers.length; i++) {
      if (handlers[i] === handler) {
        handlers.splice(i--, 1);
      }
    }
  },

  /**
   *  주어진 이름과 데이터를 기반으로 이벤트 생성
   *  사용패턴: this.trigger('select', data1, data2);
   */
  trigger(eventName, ...args) {
    if (!this._eventHandlers?.[eventName]) {
      return; // no handlers for that event name
    }

    // 핸들러 호출
    this._eventHandlers[eventName].forEach(handler => handler.apply(this, args));
  }
};
```

- `.on(eventName, handler)` – `eventName`에 해당하는 이벤트가 발생하면 실행시킬 함수 `handler`를 할당한다. 한 이벤트에 대응하는 핸들러가 여러 개 있을 때, 프로퍼티 `_eventHandlers`는 핸들러가 담긴 배열을 저장합니다. 여기선 핸들러가 배열에 추가만 된다.
- `.off(eventName, handler)` – 핸들러 리스트에서 `handler`를 제거한다.
- `.trigger(eventName, ...args)` – 이벤트를 생성한다. `_eventHandlers[eventName]`에 있는 모든 핸들러가 `...args`와 함께 호출된다.

사용법:

```javascript
// 클래스 생성
class Menu {
  choose(value) {
    this.trigger("select", value);
  }
}
// 이벤트 관련 메서드가 구현된 믹스인 추가
Object.assign(Menu.prototype, eventMixin);

let menu = new Menu();

// 메뉴 항목을 선택할 때 호출될 핸들러 추가
menu.on("select", value => alert(`선택된 값: ${value}`));

// 이벤트가 트리거 되면 핸들러가 실행되어 얼럿창이 뜸
// 얼럿창 메시지: Value selected: 123
menu.choose("123");
```

이제 `menu.on(...)`을 사용해 메뉴 선택이라는 정보를 들을 수 있게 되었고, 이에 반응하는 코드를 추가할 수 있게 되었다.

그리고 믹스인 `eventMixin`을 사용하면 이런 동작을 상속 체이닝에 끼어들지 않고도 원하는 클래스에 모두에 추가할 수 있다.

## 

> #### 요약
>
> *믹스인* 은 객체 지향 언어에서 범용적으로 쓰이는 용어로, 다른 클래스들의 메서드 조합을 포함하는 클래스를 의미한다.
>
> 몇몇 언어는 다중상속을 허용한다. 자바스크립트는 다중상속을 지원하지 않는데, 믹스인을 사용하면 메서드를 복사해 프로토타입에 구현할 수 있다.
>
> 이벤트 믹스인 예시에서 본 것처럼, 믹스인은 이벤트 핸들링 등의 행동을 추가하여 클래스를 확장하는 용도로 사용할 수 있다.
>
> mixin이 실수로 기존 클래스 메서드를 덮어쓰면 충돌이 발생할 수 있다. 따라서 mixin을 만들 땐 충돌이 발생하지 않도록 메서드 이름을 신중하게 정해야 한다.

# 10.1 'try..catch'와 에러 핸들링

아무리 프로그래밍에 능한 사람이더라도 에러가 있는 스크립트를 작성할 수 있다. 원인은 아마도 실수, 예상치 못한 사용자 입력, 잘못된 서버 응답 등의 수천만 가지 이유 때문일 것이다. 에러가 발생하면 스크립트는 ‘죽고’(즉시 중단되고), 콘솔에 에러가 출력된다.

그러나 `try..catch` 문법을 사용하면 스크립트가 죽는 걸 방지하고, 에러를 ‘잡아서(catch)’ 더 합당한 무언가를 할 수 있게 된다.



## ‘try…catch’ 문법

‘try…catch’ 문법은 'try’와 'catch’라는 두 개의 주요 블록으로 구성된다.

```javascript
try {

  // 코드...

} catch (err) {

  // 에러 핸들링

}
```

try…catch 동작 알고리즘은 다음과 같다.

1. 먼저, `try {...}` 안의 코드가 실행된다.
2. 에러가 없다면, `try` 안의 마지막 줄까지 실행되고, `catch` 블록은 건너뛴다.
3. 에러가 있다면, `try` 안 코드의 실행이 중단되고, `catch(err)` 블록으로 제어 흐름이 넘어간다. 변수 `err`(아무 이름이나 사용 가능)는 무슨 일이 일어났는지에 대한 설명이 담긴 에러 객체를 포함한다.

<img width="679" alt="Screen Shot 2021-08-06 at 10 16 27 PM" src="https://user-images.githubusercontent.com/79819941/128515996-4a1ff611-d0aa-4098-83a1-389fa03358ef.png"> 

이렇게 `try {…}` 블록 안에서 에러가 발생해도 `catch`에서 에러를 처리하기 때문에 스크립트는 죽지 않는다.

예시를 살펴보자

- 에러가 없는 예시: `(1)`과 `(2)`를 `alert` 창에 보여줌

  ```javascript
  try {
  
    alert('try 블록 시작');  // (1) <--
  
    // ...에러가 없다.
  
    alert('try 블록 끝');   // (2) <--
  
  } catch(err) {
  
    alert('에러가 없으므로, catch는 무시됩니다.'); // (3)
  
  }
  ```

- 에러가 있는 예시: `(1)`과 `(3)`을 보여준다.

  ```javascript
  try {
  
    alert('try 블록 시작');  // (1) <--
  
    lalala; // 에러, 변수가 정의되지 않음!
  
    alert('try 블록 끝(절대 도달하지 않음)');  // (2)
  
  } catch(err) {
  
    alert(`에러가 발생했습니다!`); // (3) <--
  
  }
  ```

**`try..catch`는 오직 런타임 에러에만 동작한다.**

`try..catch`는 실행 가능한(runnable) 코드에만 동작한다. 실행 가능한 코드는 유효한 자바스크립트 코드를 의미한다.

중괄호 짝이 안 맞는 것처럼 코드가 문법적으로 잘못된 경우엔 `try..catch`가 동작하지 않는다.

```javascript
try {
  {{{{{{{{{{{{
} catch(e) {
  alert("유효하지 않은 코드이기 때문에, 자바스크립트 엔진은 이 코드를 이해할 수 없다.");
}
```

자바스크립트 엔진은 코드를 읽고 난 후 코드를 실행한다. 코드를 읽는 중에 발생하는 에러는 'parse-time 에러’라고 부르는데, 엔진은 이 코드를 이해할 수 없기 때문에 parse-time 에러는 코드 안에서 복구가 불가능하다.

`try..catch`는 유효한 코드에서 발생하는 에러만 처리할 수 있다. 이런 에러를 ‘런타임 에러(runtime error)’ 혹은 '예외(exception)'라고 부른다.

**`try..catch`는 동기적으로 동작한다.**

setTimeout처럼 ‘스케줄 된(scheduled)’ 코드에서 발생한 예외는 `try..catch`에서 잡아낼 수 없다.

```javascript
try {
  setTimeout(function() {
    noSuchVariable; // 스크립트는 여기서 죽는다.
  }, 1000);
} catch (e) {
  alert( "작동 멈춤" );
}
```

`setTimeout`에 넘겨진 익명 함수는 엔진이 `try..catch`를 떠난 다음에서야 실행되기 때문이다.

스케줄 된 함수 내부의 예외를 잡으려면, `try..catch`를 반드시 함수 내부에 구현해야 한다.

```javascript
setTimeout(function() {
  try {
    noSuchVariable; // 이제 try..catch에서 에러를 핸들링 할 수 있다!
  } catch {
    alert( "에러를 잡았습니다!" );
  }
}, 1000);
```

## 에러 객체

에러가 발생하면 자바스크립트는 에러 상세내용이 담긴 객체를 생성합니다. 그 후, `catch` 블록에 이 객체를 인수로 전달한다.

```javascript
try {
  // ...
} catch(err) { // <-- '에러 객체', err 대신 다른 이름으로도 쓸 수 있음
  // ...
}
```

내장 에러 전체와 에러 객체는 두 가지 주요 프로퍼티를 가진다.

- `name`

  에러 이름. 정의되지 않은 변수 때문에 발생한 에러라면 `"ReferenceError"`가 이름이 된다.

- `message`

  에러 상세 내용을 담고 있는 문자 메시지

표준은 아니지만, `name`과 `message` 이외에 대부분의 호스트 환경에서 지원하는 프로퍼티도 있다. `stack`은 가장 널리 사용되는 비표준 프로퍼티 중 하나이다.

- `stack`

  현재 호출 스택. 에러를 유발한 중첩 호출들의 순서 정보를 가진 문자열로 디버깅 목적으로 사용된다.

예시:

```javascript
try {
  lalala; // 에러, 변수가 정의되지 않음!
} catch(err) {
  alert(err.name); // ReferenceError
  alert(err.message); // lalala is not defined
  alert(err.stack); // ReferenceError: lalala is not defined at ... (호출 스택)

  // 에러 전체를 보여줄 수도 있다.
  // 이때, 에러 객체는 "name: message" 형태의 문자열로 변환된다.
  alert(err); // ReferenceError: lalala is not defined
}
```

## 선택적 ‘catch’ 바인딩

**최근에 추가됨**

스펙에 추가된 지 얼마 안 된 문법입니다. 구식 브라우저는 폴리필이 필요하다.

에러에 대한 자세한 정보가 필요하지 않으면, `catch`에서 이를 생략할 수 있다.

```javascript
try {
  // ...
} catch { // <-- (err) 없이 쓸 수 있음
  // ...
}
```

## ‘try…catch’ 사용하기

`try..catch`가 실무에서 어떻게 사용되는지 알아보자.

앞서 JSON으로 인코딩된 값을 읽을 수 있도록 해주는 [JSON.parse(str)](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse) 메서드에 대해 배운 바 있다.

이 메서드는 주로 서버 등에서 네트워크를 통해 전달받은 데이터를 디코딩하는 데 사용하며, 전달받은 데이터에 `JSON.parse`를 호출하는 식으로 사용된다.

```javascript
let json = '{"name":"John", "age": 30}'; // 서버로부터 전달받은 데이터

let user = JSON.parse(json); // 전달받은 문자열을 자바스크립트 객체로 변환

// 문자열 형태로 전달받은 user가 프로퍼티를 가진 객체가 됨
alert( user.name ); // John
alert( user.age );  // 30
```

JSON에 관한 자세한 정보는 [JSON과 메서드](https://ko.javascript.info/json) 챕터에서 읽어보자.

**잘못된 형식의 `json`이 들어온 경우, `JSON.parse`는 에러를 만들기 때문에 스크립트가 ‘죽는다’.**

서버에서 전달받은 데이터가 잘못되어 스크립트가 죽는 경우, 사용자는 개발자 콘솔을 열지 않는 이상 절대 원인을 알 수 없다. 그런데 사람들은 메시지 등을 통해 에러의 원인을 알지 못한 채 무언가가 '그냥 죽는 것’을 정말 싫어한다.

`try..catch`를 사용해 이를 처리해 보자.

```javascript
let json = "{ bad json }";

try {

  let user = JSON.parse(json); // <-- 여기서 에러가 발생하므로
  alert( user.name ); // 이 코드는 동작하지 않는다.

} catch (e) {
  // 에러가 발생하면 제어 흐름이 catch 문으로 넘어온다.
  alert( "데이터에 에러가 있어 재요청을 시도합니다." );
  alert( e.name );
  alert( e.message );
}
```

위 예시에선 에러가 발생했다는 걸 보여주기 위해 간단히 예외처리했지만, `catch` 블록 안에서 새로운 네트워크 요청 보내기, 사용자에게 대안 제안하기, 로깅 장치에 에러 정보 보내기 등과 같은 구체적인 일을 할 수 있습니다. 스크립트가 죽도록 놔두는 것보다 훨씬 나은 대응이다.

## 직접 에러를 만들어서 던지기

`json`이 문법적으로 잘못되진 않았지만, 스크립트 내에서 사용 중인 필수 프로퍼티 `name`을 가지고 있지 않다면 무슨 일이 생길까?

```javascript
let json = '{ "age": 30 }'; // 불완전한 데이터

try {

  let user = JSON.parse(json); // <-- 에러 없음
  alert( user.name ); // 이름이 없다!

} catch (e) {
  alert( "실행되지 않습니다." );
}
```

위 예시에서 `JSON.parse`는 정상적으로 실행되었지만 `name`이 없는 건 에러를 유발하는 상황이다.

이제 `throw` 연산자를 사용해 에러 처리를 통합해보자.

### ‘throw’ 연산자

`throw` 연산자는 에러를 생성하며, 문법은 다음과 같다.

```javascript
throw <error object>
```

이론적으로는 숫자, 문자열 같은 원시형 자료를 포함한 어떤 것이든 에러 객체(error object)로 사용할 수 있다. 하지만 내장 에러와의 호환을 위해 되도록 에러 객체에 `name`과 `message` 프로퍼티를 넣어주는 것을 권장한다.

자바스크립트는 `Error`, `SyntaxError`, `ReferenceError`, `TypeError`등의 표준 에러 객체 관련 생성자를 지원한다. 이 생성자들을 이용해 에러 객체를 만들 수도 있다.

```javascript
let error = new Error(message);
// or
let error = new SyntaxError(message);
let error = new ReferenceError(message);
// ...
```

일반 객체가 아닌 내장 생성자를 사용해 만든 내장 에러 객체의 `name` 프로퍼티는 생성자 이름과 동일한 값을 갖습니다. 프로퍼티 `message`의 값은 인수에서 가져온다.

```javascript
let error = new Error("이상한 일이 발생했습니다. o_O");

alert(error.name); // Error
alert(error.message); // 이상한 일이 발생했습니다. o_O
```

잘못된 데이터를 받았을 때, `JSON.parse`가 어떤 종류의 에러를 만들어내는지 아래 코드를 통해 살펴보자.

```javascript
try {
  JSON.parse("{ 잘못된 형식의 json o_O }");
} catch(e) {
  alert(e.name); // SyntaxError
  alert(e.message); // Unexpected token b in JSON at position 2
}
```

`SyntaxError`가 발생한다. 사용자를 나타내는 객체에 `name` 프로퍼티는 반드시 있어야 하므로, 이제 `name`이 없으면 에러가 발생한 것으로 간주하고 예외 처리를  `throw` 연산자를 사용해 에러를 던져보자.

```javascript
let json = '{ "age": 30 }'; // 불완전한 데이터

try {

  let user = JSON.parse(json); // <-- 에러 없음

  if (!user.name) {
    throw new SyntaxError("불완전한 데이터: 이름 없음"); // (*)
  }

  alert( user.name );

} catch(e) {
  alert( "JSON Error: " + e.message ); // JSON Error: 불완전한 데이터: 이름 없음
}
```

`(*)`로 표시한 줄에서 `throw` 연산자는 `message`를 이용해 `SyntaxError`를 생성한다. 에러 생성 방식은 자바스크립트가 자체적으로 에러를 생성하는 방식과 동일하다. 에러가 발생했으므로 `try`의 실행은 즉시 중단되고 제어 흐름이 `catch`로 넘어간 것을 얼럿 창을 통해 확인할 수 있다.

이제 `JSON.parse`에서 에러가 발생한 경우를 포함해서 모든 에러를 `catch` 블록 안에서 처리할 수 있게 되었다.

## 에러 다시 던지기

위 예시에선 불완전한 데이터를 `try..catch`로 처리하였다. 그런데 *또 다른 예기치 않은 에러*가 `try {...}` 블록 안에서 발생 할 수도 있다. 정의되지 않은 변수 사용 등의 프로그래밍 에러가 발생할 가능성은 항상 있기 때문이다.

예시:

```javascript
let json = '{ "age": 30 }'; // 불완전한 데이터

try {
  user = JSON.parse(json); // <-- user 앞에 let을 붙이는 걸 잊었다.

  // ...
} catch(err) {
  alert("JSON Error: " + err); // JSON Error: ReferenceError: user is not defined
  // (실제론 JSON Error가 아니다.)
}
```

에러는 어떤 상황에서도 발생할 수 있다! 몇십 년간 몇백만 명이 사용한 오픈소스 유틸리티에서도 끔찍한 해킹으로 이어질 수 있는 엄청난 버그가 발견된다.

위에선 '불완전한 데이터’를 다루려는 목적으로 `try..catch`를 썼는데 `catch`는 원래 `try` 블록에서 발생한 *모든* 에러를 잡으려는 목적으로 만들어졌다. 그런데 위 예시에서 `catch`는 예상치 못한 에러를 잡아내 주긴 했지만, 에러 종류와 관계없이 `"JSON Error"` 메시지를 보여준다. 이렇게 에러 종류와 관계없이 동일한 방식으로 에러를 처리하는 것은 디버깅을 어렵게 만들기 때문에 좋지 않다.

이런 문제를 피하고자 ‘다시 던지기(rethrowing)’ 기술을 사용합니다. 규칙은 간단하다.

**catch는 알고 있는 에러만 처리하고 나머지는 ‘다시 던져야’ 한다.**

‘다시 던지기’ 기술을 더 자세히 살펴보면,

1. catch가 모든 에러를 받는다.
2. `catch(err) {...}` 블록 안에서 에러 객체 `err`를 분석한다.
3. 에러 처리 방법을 알지 못하면 `throw err`를 한다.

보통 에러 타입을 `instanceof` 명령어로 체크한다.

```javascript
try {
  user = { /*...*/ };
} catch(err) {
  if (err instanceof ReferenceError) {
    alert('ReferenceError'); //  정의되지 않은 변수에 접근하여 'ReferenceError' 발생
  }
}
```

`err.name` 프로퍼티로 에러 클래스 이름을 알 수도 있다. 기본형 에러는 모두 `err.name` 프로퍼티를 가진다. 또는 `err.constructor.name`를 사용할 수도 있다.

에러를 다시 던져서 `catch` 블록에선 `SyntaxError`만 처리되도록 해보자.

```javascript
let json = '{ "age": 30 }'; // 불완전한 데이터
try {

  let user = JSON.parse(json);

  if (!user.name) {
    throw new SyntaxError("불완전한 데이터: 이름 없음");
  }

  blabla(); // 예상치 못한 에러

  alert( user.name );

} catch(e) {

  if (e instanceof SyntaxError) {
    alert( "JSON Error: " + e.message );
  } else {
    throw e; // 에러 다시 던지기 (*)
  }

}
```

`catch` 블록 안의 `(*)`로 표시한 줄에서 다시 던져진(rethrow) 에러는 `try..catch` ‘밖으로 던져진다’. 이때 바깥에 `try..catch`가 있다면 여기서 에러를 잡습니다. 아니라면 스크립트는 문제가 발생될 것이다.

이렇게 하면 `catch` 블록에선 어떻게 다룰지 알고 있는 에러만 처리하고, 알 수 없는 에러는 ‘건너뛸 수’ 있다.

이제 `try..catch`를 하나 더 만들어, 다시 던져진 예상치 못한 에러를 처리해 보면,

```javascript
function readData() {
  let json = '{ "age": 30 }';

  try {
    // ...
    blabla(); // 에러!
  } catch (e) {
    // ...
    if (!(e instanceof SyntaxError)) {
      throw e; // 알 수 없는 에러 다시 던지기
    }
  }
}

try {
  readData();
} catch (e) {
  alert( "External catch got: " + e ); // 에러를 잡음
}
```

`readData`는 `SyntaxError`만 처리할 수 있지만, 함수 바깥의 `try..catch`에서는 예상치 못한 에러도 처리할 수 있게 되었다.

## try…catch…finally

`try..catch`는 `finally`라는 코드 절을 하나 더 가질 수 있는데 `finally`안의 코드는 다음과 같은 상황에서 실행된다.

- 에러가 없는 경우: `try` 실행이 끝난 후
- 에러가 있는 경우: `catch` 실행이 끝난 후

`finally`를 사용하면 `try..catch`를 다음과 같이 확장할 수 있다.

```javascript
try {
   ... 코드를 실행 ...
} catch(e) {
   ... 에러 핸들링 ...
} finally {
   ... 항상 실행 ...
}
```

아래 코드를 실행해보면

```javascript
try {
  alert( 'try 블록 시작' );
  if (confirm('에러를 만드시겠습니까?')) 이상한_코드();
} catch (e) {
  alert( 'catch' );
} finally {
  alert( 'finally' );
}
```

위 코드는 두 가지 경로로 실행된다.

1. "에러를 만드시겠습니까?"에 'OK’로 답한 경우: `try -> catch -> finally`
2. 'No’로 답한 경우: `try -> finally`

`finally` 절은 무언가를 실행하고, 실행 결과에 상관없이 실행을 완료하고 싶을 경우 사용된다.
