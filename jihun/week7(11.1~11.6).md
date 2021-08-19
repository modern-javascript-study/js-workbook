# 콜백

자바스크립트 호스트 환경이 제공하는 여러 함수를 사용하면 *비동기(asynchronous)* 동작을 스케줄링을 하여, 원하는 때에 동작이 시작하도록 할 수 있는데 바로 여기서 `setTimeout`은 스케줄링에 사용되는 가장 대표적인 함수이다.

실무에서 맞닥뜨리는 비동기 동작은 아주 다양한데, 스크립트나 모듈을 로딩하는 것 또한 비동기 동작이다.(이 예시는 뒤에서 구체적으로 다룰 예정) `src`에 있는 스크립트를 읽어오는 함수 `loadScript(src)`를 예시로 비동기 동작 처리가 어떻게 일어나는지 살펴보자.

```javascript
function loadScript(src) {
  // <script> 태그를 만들고 페이지에 태그를 추가한다.
  // 태그가 페이지에 추가되면 src에 있는 스크립트를 로딩하고 실행한다.
  let script = document.createElement('script');
  script.src = src;
  document.head.append(script);
}
```

함수 `loadScript(src)`는 `<script src="…">`를 동적으로 만들고 이를 문서에 추가한다. 브라우저는 자동으로 태그에 있는 스크립트를 불러오고, 로딩이 완료되면 스크립트를 실행한다.

`loadScript(src)` 사용법은 다음과 같다.

```javascript
// 해당 경로에 위치한 스크립트를 불러오고 실행함
loadScript('/my/script.js');
```

그런데 이 때 스크립트는 ‘비동기적으로’ 실행된다. 로딩은 지금 당장 시작되더라도 실행은 함수가 끝난 후에야 되기 때문에 `loadScript(…)` 아래에 있는 코드들은 스크립트 로딩이 종료되는 걸 기다리지 않는다.

```javascript
loadScript('/my/script.js');
// loadScript 아래의 코드는
// 스크립트 로딩이 끝날 때까지 기다리지 않는다.
// ...
```

스크립트 로딩이 끝나자마자 이 스크립트를 사용해 무언가를 해야만 한다고 가정해 보자. 스크립트 안에 다양한 함수가 정의되어 있고, 우리는 이 함수를 실행하길 원하는 상황이다. 그런데 `loadScript(...)`를 호출하자마자 내부 함수를 호출하면 원하는 대로 작동하지 않는다.

```javascript
loadScript('/my/script.js'); // script.js엔 "function newFunction() {…}"이 있다.

newFunction(); // 함수가 존재하지 않는다는 에러가 발생한다!
```

에러는 브라우저가 스크립트를 읽어올 수 있는 시간을 충분히 확보하지 못했기 때문에 발생한다. 그런데 현재로서는 함수 `loadScript`에서 스크립트 로딩이 완료되었는지 알 방법이 없다. 언젠간 스크립트가 로드되고 실행도 되겠지만, 그게 다며, 원하는 대로 스크립트 안의 함수나 변수를 사용하려면 스크립트 로딩이 끝났는지 여부를 알 수 있어야 한다.

`loadScript`의 두 번째 인수로 스크립트 로딩이 끝난 후 실행될 함수인 `콜백(callback)` 함수를 추가해 보자(콜백 함수는 나중에 호출할 함수를 의미한다. – 옮긴이).

```javascript
function loadScript(src, callback) {
  let script = document.createElement('script');
  script.src = src;

  script.onload = () => callback(script);

  document.head.append(script);
}
```

새롭게 불러온 스크립트에 있는 함수를 콜백 함수 안에서 호출하면 원하는 대로 외부 스크립트 안의 함수를 사용할 수 있다.

```javascript
loadScript('/my/script.js', function() {
  // 콜백 함수는 스크립트 로드가 끝나면 실행된다.
  newFunction(); // 이제 함수 호출이 제대로 동작한다.
  ...
});
```

이렇게 두 번째 인수로 전달된 함수(대개 익명 함수)는 원하는 동작(위 예제에선 외부 스크립트를 불러오는 것 – 옮긴이)이 완료되었을 때 실행된다.

아래는 실제 존재하는 스크립트를 이용해 만든 예시이다.

```javascript
function loadScript(src, callback) {
  let script = document.createElement('script');
  script.src = src;
  script.onload = () => callback(script);
  document.head.append(script);
}

loadScript('https://cdnjs.cloudflare.com/ajax/libs/lodash.js/3.2.0/lodash.js', script => {
  alert(`${script.src}가 로드되었습니다.`);
  alert( _ ); // 스크립트에 정의된 함수
});
```

이런 방식을 ‘콜백 기반(callback-based)’ 비동기 프로그래밍이라고 한다. 무언가를 비동기적으로 수행하는 함수는 함수 내 동작이 모두 처리된 후 실행되어야 하는 함수가 들어갈 `콜백`을 인수로 반드시 제공해야 한다.

위 예시에선 `loadScript`의 인수로 콜백을 제공해 주었는데, 이렇게 콜백을 사용한 방식은 비동기 프로그래밍의 일반적인 접근법이다.

#### 콜백 속 콜백

스크립트가 두 개 있는 경우, 어떻게 하면 두 스크립트를 순차적으로 불러올 수 있을까? 두 번째 스크립트 로딩은 첫 번째 스크립트의 로딩이 끝난 이후가 되길 원한다면 말이다.

가장 자연스러운 해결 방법은 아래와 같이 콜백 함수 안에서 두 번째 `loadScript`를 호출하는 것이다.

```javascript
loadScript('/my/script.js', function(script) {

  alert(`${script.src}을 로딩했습니다. 이젠, 다음 스크립트를 로딩합시다.`);

  loadScript('/my/script2.js', function(script) {
    alert(`두 번째 스크립트를 성공적으로 로딩했습니다.`);
  });

});
```

이렇게 중첩 콜백을 만들면 바깥에 위치한 `loadScript`가 완료된 후, 안쪽 `loadScript`가 실행된다.

그런데 여기에 더하여 스크립트를 하나 더 불러오고 싶다면 어떻게 해야 할까?

```javascript
loadScript('/my/script.js', function(script) {

  loadScript('/my/script2.js', function(script) {

    loadScript('/my/script3.js', function(script) {
      // 세 스크립트 로딩이 끝난 후 실행됨
    });

  })

});
```

위와 같이 모든 새로운 동작이 콜백 안에 위치하게 작성하면 된다. 그런데 이렇게 콜백 안에 콜백을 넣는 것은 수행하려는 동작이 단 몇 개뿐이라면 괜찮지만, 동작이 많은 경우엔 좋지 않다. 다른 방식으로 코드를 작성하는 방법은 곧 알아보도록 한다.