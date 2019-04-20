
# The "new Function" syntax
# "새로운 함수" 문법

There's one more way to create a function. It's rarely used, but sometimes there's no alternative.
함수를 생성하는데 한가지 방법이 더 있습니다. 제한적으로 사용되지만 가끔 이 방법외에는 대안이 없을때도 있죠.

## Syntax
## 문법

The syntax for creating a function:
함수를 만드는 문법은 이렇습니다.

```js
let func = new Function ([arg1[, arg2[, ...argN]],] functionBody)
```

In other words, function parameters (or, more precisely, names for them) go first, and the body is last. All arguments are strings.
다른말로 하면, 함수의 매개변수(또는 정확히는 그것들의 이름)이 먼저 오고, 본문이 마지막에 따라옵니다. 모든 인수는 문자열입니다.

It's easier to understand by looking at an example. Here's a function with two arguments:
더 쉽게 이해하기 위해서 함수와 두개의 인수가 있는 예제를 보겠습니다. 

```js run
let sum = new Function('a', 'b', 'return a + b'); 

alert( sum(1, 2) ); // 3
```

If there are no arguments, then there's only a single argument, the function body:
만약에 인수들이 없다면 오직 하나의 함수 본문을 가진 인수만이 있습니다.

```js run
let sayHi = new Function('alert("Hello")');

sayHi(); // Hello
```

The major difference from other ways we've seen is that the function is created literally from a string, that is passed at run time.
가장 다른점은 함수가 문자그대로 쓰여지고 실행될때 그것이 넘겨진다는 것입니다.

All previous declarations required us, programmers, to write the function code in the script.
예전의 모든 선언문은 함수의 코드는 스크립트 안에서 쓰여졌습니다.

But `new Function` allows to turn any string into a function. For example, we can receive a new function from a server and then execute it:
그러나 `new Function` 이라는 문법은 어떠한 문자열을 함수로 바꾸어주는 역활을 합니다. 예를 들면, 새로운 함수를 서버로 받아서 그것을 실행해야 될때 처럼입니다.

```js
let str = ... receive the code from a server dynamically ...

let func = new Function(str);
func();
```

It is used in very specific cases, like when we receive code from a server, or to dynamically compile a function from a template. The need for that usually arises at advanced stages of development.
이것은 굉장히 특정한 상황에만 사용됩니다. 서버로부터 코드를 받아올때나 아니면 템블릿으로 부터 함수를 유동적으로 컴파일 할때 입니다. 이런것들이 필요할 때는 좀더 고급적인 개발단계가 필요할때 입니다.

## Closure
## 클로져(Closure)

Usually, a function remembers where it was born in the special property `[[Environment]]`. It references the Lexical Environment from where it's created.
보통은 함수가 어디에서 작성되었는지 특수한 프로퍼티은 `[[Environment]]`에 기억합니다. 그것은 생성된 렉시컬 환경을 참고합니다.

But when a function is created using `new Function`, its `[[Environment]]` references not the current Lexical Environment, but instead the global one.
그러나 `new Function`문을 사용해서 생성되면 `[[Environment]]`은 현재의 렉시컬 환경이 아닙니다. 대신에 전역(global)이 됩니다.

```js run

function getFunc() {
  let value = "test";

*!*
  let func = new Function('alert(value)');
*/!*

  return func;
}

getFunc()(); // 에러 값이 정의되지 않음
```

Compare it with the regular behavior:
일반적인 것과 비교해보세요.

```js run 
function getFunc() {
  let value = "test";

*!*
  let func = function() { alert(value); };
*/!*

  return func;
}

getFunc()(); // *!*"test"*/!*, from the Lexical Environment of getFunc
```

This special feature of `new Function` looks strange, but appears very useful in practice.
특수한 기능은 `new Function`은 이상해 보일 수 있습니다만 아주 유용할 때가 있습니다.

Imagine that we must create a function from a string. The code of that function is not known at the time of writing the script (that's why we don't use regular functions), but will be known in the process of execution. We may receive it from the server or from another source.
문자열로 부터 함수를 만들어야 되는 상황이 있어야된다고 생각해보면. 그 함수의 코드는 스크립트를 작성할때는 알 수 없습니다 (그래서 보통함수를 사용하지 않죠) 그러나, 실행되는 프로세스에서는 알 수 있습니다. 그것을 서버 또는 다른출처로 부터 받게 되겠지요.

Our new function needs to interact with the main script.
새로운 함수는 메인 스크립트와 상호작용해야할 것입니다.

Perhaps we want it to be able to access outer local variables?
혹시 그것이 외부의 지역변수에 접근해야 한다면 어떨까요?

The problem is that before JavaScript is published to production, it's compressed using a *minifier* -- a special program that shrinks code by removing extra comments, spaces and -- what's important, renames local variables into shorter ones.
문제는 자바스크립트가 운영에 반영되기 전에 있습니다. 자바스크립트는 *minifier*라는 특수한 프로그램에 의해 압축된다는 것입니다 -- 코드의 특수한 주적이나 빈칸 -- 중요한건 이것이 지역변수를 짧은것으로 바꾸게 됩니다.

For instance, if a function has `let userName`, minifier replaces it `let a` (or another letter if this one is occupied), and does it everywhere. That's usually a safe thing to do, because the variable is local, nothing outside the function can access it. 
And inside the function, minifier replaces every mention of it. Minifiers are smart, they analyze the code structure, so they don't break anything. They're not just a dumb find-and-replace.
예를 들면, 만약에 함수가 `let userName` 이라는걸 가지고 있다면 minifier 는 그것은 `let a`로 (또는 그것을 어딘가 사용하고 있다면 다른 문자로) 대체합니다. 그리고 이런일은 모든곳에서 일어납니다. 보통 이런 작업은 값들이 지역변수라 안전하지만 외부 함수에서는 여기에 접근할 수 없습니다.  
그리고 함수의 내부에서 minifier는 언급된 모든곳을 바꿉니다. Minifiers 는 똑똑하고 코드의 구조를 분석하기 때문에 어떠한곳도 망가뜨리진않습니다. 그냥 찾아서 바꾸는 작업을 하는건 아닙니다.

But, if `new Function` could access outer variables, then it would be unable to find `userName`, since this is passed in as a string *after* the code is minified.
그러나 만약세 `new Function` 구문이 외부 변수들에 접근한다면 `userName` 이라는 것은 찾을 수 없을것입니다. *이후의* 코드는 minified 되었기 때문이죠.

**Even if we could access outer lexical environment in `new Function`, we would have problems with minifiers.**
**`new Function`문법이 외부 렉시컬 환경에 접근할 수 있었을지라도 minifiers에 의한 문제는 생길 수 있습니다.**

The "special feature" of `new Function` saves us from mistakes.
`new Function`이란 특별한 기능이 이런 실수로부터 예방하죠.

And it enforces better code. If we need to pass something to a function created by `new Function`, we should pass it explicitly as an argument.
그리고 좀더 좋은 코드를 제공합니다. `new Function`이란 문법을 사용한 함수에 무언가를 넘기려고 한다면 명시적으로 인수(argument)로 넘기는것이 좋습니다.

Our "sum" function actually does that right:
아래의 "sum"함수는 그것을 잘 표현하고 있습니다.

```js run 
*!*
let sum = new Function('a', 'b', 'return a + b');
*/!*

let a = 1, b = 2;

*!*
// outer values are passed as arguments
alert( sum(a, b) ); // 3
*/!*
```

## Summary
## 요약

The syntax:
문법:

```js
let func = new Function(arg1, arg2, ..., body);
```

For historical reasons, arguments can also be given as a comma-separated list. 
역사적인 이유로 인수들은 콤바로 구별된 리스트가 될 수 있습니다.

These three mean the same:
다음 세가지는 똑같습니다. 

```js 
new Function('a', 'b', 'return a + b'); // 기본 문법
new Function('a,b', 'return a + b'); // 콤마로 구별된
new Function('a , b', 'return a + b'); // 콤마와 스페이스로 구별된
```

Functions created with `new Function`, have `[[Environment]]` referencing the global Lexical Environment, not the outer one. 
Hence, they cannot use outer variables. But that's actually good, because it saves us from errors. 
Passing parameters explicitly is a much better method architecturally and causes no problems with minifiers.
`new Function`으로 생성된 함수는 전역 렉시컬환경을 참조하는 `[[Environment]]`를 가지고 있습니다.
그러므로 그 함수는 외부 변수를 사용할 수 없습니만 그것은 사실 에러를 방지하므로 좋은 현상입니다.
명시적으로 매개변수로 넘기는것이 설계적으로 더 좋은 방법이고 minifiers와 문제가 생기지 않는 방법입니다.