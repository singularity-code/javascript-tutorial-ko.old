# 상호 작용: 알림창, 입력창, 확인창

튜토리얼의 이 파트는 브라우저(Browser) 환경 특유의 조정 없이 "있는 그대로"의 자바스크립트를 다루는 것을 목표로합니다.

그러나 우리는 여전히 브라우저(Browser)를 데모 환경으로 사용할 것이므로 최소한의 사용자 인터페이스 기능을 알아야 합니다. 이 챕터에서는 브라우저(Browser) 함수인 `alert`, `prompt`, `confirm`에 대해 알아 보겠습니다.

## 알림창(alert)

문법:

```js
alert(message);
```

이 메시지는 사용자가 확인("OK")을 누를 때까지 메시지를 표시하고 스크립트 실행을 일시 중지합니다.

예시:

```js run
alert("Hello");
```

메시지가 있는 미니 창을 *모달창*(modal window)이라고 합니다. 모달"modal"이란 단어는 방문자가 창을 다룰 때까지 페이지의 나머지 부분과 상호 작용하거나 다른 버튼을 누르는 등의 작업을 수행할 수 없음을 의미합니다. 이 경우 - 확인("OK")를 누를 때까지 입니다. 

## 입력창(prompt)

`prompt`함수는 두 개의 인수를 받습니다.

```js no-beautify
result = prompt(title, [default]);
```

이 함수는 텍스트 메시지, 방문자를 위한 입력 필드(input field)과 확인/취소(OK/CANCEL) 버튼이 있는 모달 창을 보여줍니다.

`title`
: 방문자에게 보여줄 텍스트입니다.

`default`
: 선택사항인 두 번째 매개 변수는 입력 필드의 초기 값입니다.

방문자는 프롬프트 입력 필드에 내용을 입력하고 확인(OK)을 누릅니다. 또는 취소(CANCEL)을 누르거나 `key:Esc`키를 눌러 입력을 취소할 수 있습니다.

`prompt`호출은 입력 필드에서 텍스트를 반환하거나 입력이 취소된 경우 `null`을 반환합니다.

예시:

```js run
let age = prompt('How old are you?', 100);

alert(`You are ${age} years old!`); // You are 100 years old!
```

````warn header="IE 에서는 항상 '기본값'을 제공하십시오."
두 번째 매개변수는 선택사항이지만, 제공하지 않으면 Internet Explorer는 `"undefined"`텍스트를 프롬프트에 삽입합니다.

확인하기 위해 Internet Explorer에서 이 코드를 실행하십시오.

```js run
let test = prompt("Test");
```

따라서 IE에서 보기 좋게 prompt()를 실행하려면, 항상 두 번째 인수를 제공하는 것이 좋습니다.

```js run
let test = prompt("Test", ''); // <-- for IE
```
````

## 확인창(confirm)

문법:

```js
result = confirm(question);
```

`confirm`함수는 질문(`question`)과 두 개의 버튼(OK/CANCEL)이 있는 모달 윈도우를 보여줍니다.

OK를 누르면 `true`이고 그렇지 않으면 `false`입니다.

예시:

```js run
let isBoss = confirm("Are you the boss?");

alert( isBoss ); // true if OK is pressed
```

## 요약

방문자와 상호작용할 수 있는 세 가지 브라우저 특유의 기능을 다뤘습니다.

`alert`
: 메시지를 보여줍니다.

`prompt`
: 사용자에게 텍스트를 입력하라는 메시지를 표시합니다. 이 함수는 텍스트를 반환하거나, 취소(CANCEL) 또는 `key:Esc`를 누르면 `null`을 반환합니다.

`confirm`
: 메시지를 보여주고 사용자가 확인("OK")또는 취소("CANCEL")을 누를 때까지 기다립니다. 이 함수는 확인(OK)에 대해 `true`를 반환하고 CANCEL/`key:Esc`에 대해 `false`를 반환합니다. 

이 모든 메서드는 모달입니다. 이 메서드들은 스크립트 실행을 일시 중지하고 방문자가 창을 닫을 때까지 나머지 페이지와 상호 작용할 수 없도록 합니다.

위의 메서드에는 공통된 두 가지 제한 사항이 있습니다.

1. 모달 윈도우의 정확한 위치는 브라우저에 의해 결정됩니다. 대개 중앙에 있습니다.
2. 창의 모양은 브라우저에 따라 달라집니다. 우리는 그것을 수정할 수 없습니다.

이 것은 간단함(simplicity)을 위해 치러야할 대가입니다. 더 좋은 창을 표시하고 방문자와 보다 풍부한 상호 작용을 보여주는 다른 방법이 있지만 "멋으로 덧붙이는 부가 기능"이 별로 중요하지 않은 경우, 이 메서드를 사용하는게 좋습니다. 
