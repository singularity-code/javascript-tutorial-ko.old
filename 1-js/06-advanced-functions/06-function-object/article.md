
# Function object, NFE
# 함수 객체, NFE

As we already know, functions in JavaScript are values.
자바스크립트에서 함수는 값들이라는 것을 알고 있습니다.

Every value in JavaScript has a type. What type is a function?
모든 자바스크립트 값들은 타입을 가지고 있습니다. 함수의 타입은 무엇일까요?

In JavaScript, functions are objects.
자바스크립트에서 함수는 객체입니다.

A good way to imagine functions is as callable "action objects". We can not only call them, but also treat them as objects: add/remove properties, pass by reference etc.
좋은 의미에서 함수들이 호출할 수 있는 "행동 객체들"이라고 상상해 보세요. 호출하기만 하는것이 아니라 그것을 객체처럼 다루어야합니다.

## The "name" property
## "name" 프로퍼티

Function objects contain a few useable properties.
함수 객체들은 몇가지 사용가능한 프로퍼티를 가지고 있습니다.

For instance, a function's name is accessible as the "name" property:
예를 들면 함수의 이름은 접근가능한 "name" 프로퍼티입니다.

```js run
function sayHi() {
  alert("Hi");
}

alert(sayHi.name); // sayHi
```

What's more funny, the name-assigning logic is smart. It also assigns the correct name to functions that are used in assignments:
재밌는건 이름을 할당하는 로직은 똑똑하다는것 입니다. assignments를 사용해서 함수의 이름을 할당해도 제대로 작동합니다.

```js run
let sayHi = function() {
  alert("Hi");
}

alert(sayHi.name); // sayHi (works!)
```

It also works if the assignment is done via a default value:
default value를 사용해도 마찬가지로 작동합니다.

```js run
function f(sayHi = function() {}) {
  alert(sayHi.name); // sayHi (works!)
}

f();
```

In the specification, this feature is called a "contextual name". If the function does not provide one, then in an assignment it is figured out from the context.
규격에 따르면, 이러한 기능을 "contextual name"이라고 합니다. 만약에 함수가 이것을 제공하지 않으면 할당할때 컨텍스트에서 가져옵니다.

Object methods have names too:
객체 메서드들도 이름들을 가지고 있습니다.

```js run
let user = {

  sayHi() {
    // ...
  },

  sayBye: function() {
    // ...
  }

}

alert(user.sayHi.name); // sayHi
alert(user.sayBye.name); // sayBye
```

There's no magic though. There are cases when there's no way to figure out the right name. In that case, the name property is empty, like here:
몇가지 경우에는 올바른 이름을 지정해 줄 수 없는 경우도 있습니다. 이런 몇몇의 경우에는 name 프로퍼티는 비어있습니다. 아래와 같이:

```js
// function created inside array
let arr = [function() {}];

alert( arr[0].name ); // <empty string>
// the engine has no way to set up the right name, so there is none
```

In practice, however, most functions do have a name.
실제로는 거의 모든 함수들은 name 을 가지고 있습니다.

## The "length" property
## "length" 프로퍼티

There is another built-in property "length" that returns the number of function parameters, for instance:
또 다른 내장 프로퍼티인 "length"는 함수의 매개변수의 개수를 반환합니다. 예를 들면

```js run
function f1(a) {}
function f2(a, b) {}
function many(a, b, ...more) {}

alert(f1.length); // 1
alert(f2.length); // 2
alert(many.length); // 2
```

Here we can see that rest parameters are not counted.
여기서 나머지 매개변수는 적용받지 않습니다.

The `length` property is sometimes used for introspection in functions that operate on other functions.
가끔 `length` 프로퍼티는 다른 함수들안에서 동작하는 함수들의 내부검사에 사용됩니다.

For instance, in the code below the `ask` function accepts a `question` to ask and an arbitrary number of `handler` functions to call.
예를 들면 아래의 `ask` 코드의 함수는 `question` 을 받아서 물어보고 arbitary number of `handler` 함수들을 호출합니다.

Once a user provides their answer, the function calls the handlers. We can pass two kinds of handlers:
사용자가 답을 제공했을때 함수는 핸들러들을 호출해서 두가지 종류의 핸들러로 전달합니다.

- A zero-argument function, which is only called when the user gives a positive answer.
- 오직 사용자가 올바른 답을 제시해야만 호출되는 인수가 없는 함수.
- A function with arguments, which is called in either case and returns an answer.
- 어떤 경우에도 답을 반환하는 매개변수가 있는 함수

The idea is that we have a simple, no-arguments handler syntax for positive cases (most frequent variant), but are able to provide universal handlers as well.
아이디어는 간단합니다. 인수가 없는 핸들러 구문은 긍적적인 경우에만(most frequent variant), 그러나 다목적인 핸들러도 제공하기 위함 입니다. 

To call `handlers` the right way, we examine the `length` property:
`handlers` 를 바르게 호출하기 위해서는 `length` 프로퍼티를 검사해야 합니다.

```js run
function ask(question, ...handlers) {
  let isYes = confirm(question);

  for(let handler of handlers) {
    if (handler.length == 0) {
      if (isYes) handler();
    } else {
      handler(isYes);
    }
  }

}

// for positive answer, both handlers are called
// for negative answer, only the second one
ask("Question?", () => alert('You said yes'), result => alert(result));
```

This is a particular case of so-called [polymorphism](https://en.wikipedia.org/wiki/Polymorphism_(computer_science)) -- treating arguments differently depending on their type or, 
in our case depending on the `length`. The idea does have a use in JavaScript libraries.
이런 특별한 경우를 [다형성(polymorphism)](https://en.wikipedia.org/wiki/Polymorphism_(computer_science)) 이라고 부릅니다 -- 인수를 타입이나 예제의 경우에는 `length`에 따라 다르게 다루는경우.
이러한 아이디어는 자바스크립트 라이브러리에서 사용됩니다.

## Custom properties

We can also add properties of our own.

Here we add the `counter` property to track the total calls count:

```js run
function sayHi() {
  alert("Hi");

  *!*
  // let's count how many times we run
  sayHi.counter++;
  */!*
}
sayHi.counter = 0; // initial value

sayHi(); // Hi
sayHi(); // Hi

alert( `Called ${sayHi.counter} times` ); // Called 2 times
```

```warn header="A property is not a variable"
A property assigned to a function like `sayHi.counter = 0` does *not* define a local variable `counter` inside it. In other words, a property `counter` and a variable `let counter` are two unrelated things.

We can treat a function as an object, store properties in it, but that has no effect on its execution. Variables never use function properties and vice versa. These are just parallel worlds.
```

Function properties can replace closures sometimes. For instance, we can rewrite the counter function example from the chapter <info:closure> to use a function property:

```js run
function makeCounter() {
  // instead of:
  // let count = 0

  function counter() {
    return counter.count++;
  };

  counter.count = 0;

  return counter;
}

let counter = makeCounter();
alert( counter() ); // 0
alert( counter() ); // 1
```

The `count` is now stored in the function directly, not in its outer Lexical Environment.

Is it better or worse than using a closure?

The main difference is that if the value of `count` lives in an outer variable, then external code is unable to access it. Only nested functions may modify it. And if it's bound to a function, then such a thing is possible:

```js run
function makeCounter() {

  function counter() {
    return counter.count++;
  };

  counter.count = 0;

  return counter;
}

let counter = makeCounter();

*!*
counter.count = 10;
alert( counter() ); // 10
*/!*
```

So the choice of implementation depends on our aims.

## Named Function Expression

Named Function Expression, or NFE, is a term for Function Expressions that have a name.

For instance, let's take an ordinary Function Expression:

```js
let sayHi = function(who) {
  alert(`Hello, ${who}`);
};
```

And add a name to it:

```js
let sayHi = function *!*func*/!*(who) {
  alert(`Hello, ${who}`);
};
```

Did we achieve anything here? What's the purpose of that additional `"func"` name?

First let's note, that we still have a Function Expression. Adding the name `"func"` after `function` did not make it a Function Declaration, because it is still created as a part of an assignment expression.

Adding such a name also did not break anything.

The function is still available as `sayHi()`:

```js run
let sayHi = function *!*func*/!*(who) {
  alert(`Hello, ${who}`);
};

sayHi("John"); // Hello, John
```

There are two special things about the name `func`:

1. It allows the function to reference itself internally.
2. It is not visible outside of the function.

For instance, the function `sayHi` below calls itself again with `"Guest"` if no `who` is provided:

```js run
let sayHi = function *!*func*/!*(who) {
  if (who) {
    alert(`Hello, ${who}`);
  } else {
*!*
    func("Guest"); // use func to re-call itself
*/!*
  }
};

sayHi(); // Hello, Guest

// But this won't work:
func(); // Error, func is not defined (not visible outside of the function)
```

Why do we use `func`? Maybe just use `sayHi` for the nested call?


Actually, in most cases we can:

```js
let sayHi = function(who) {
  if (who) {
    alert(`Hello, ${who}`);
  } else {
*!*
    sayHi("Guest");
*/!*
  }
};
```

The problem with that code is that the value of `sayHi` may change. The function may go to another variable, and the code will start to give errors:

```js run
let sayHi = function(who) {
  if (who) {
    alert(`Hello, ${who}`);
  } else {
*!*
    sayHi("Guest"); // Error: sayHi is not a function
*/!*
  }
};

let welcome = sayHi;
sayHi = null;

welcome(); // Error, the nested sayHi call doesn't work any more!
```

That happens because the function takes `sayHi` from its outer lexical environment. There's no local `sayHi`, so the outer variable is used. And at the moment of the call that outer `sayHi` is `null`.

The optional name which we can put into the Function Expression is meant to solve exactly these kinds of problems.

Let's use it to fix our code:

```js run
let sayHi = function *!*func*/!*(who) {
  if (who) {
    alert(`Hello, ${who}`);
  } else {
*!*
    func("Guest"); // Now all fine
*/!*
  }
};

let welcome = sayHi;
sayHi = null;

welcome(); // Hello, Guest (nested call works)
```

Now it works, because the name `"func"` is function-local. It is not taken from outside (and not visible there). The specification guarantees that it will always reference the current function.

The outer code still has it's variable `sayHi` or `welcome`. And `func` is an "internal function name", how the function can call itself internally.

```smart header="There's no such thing for Function Declaration"
The "internal name" feature described here is only available for Function Expressions, not to Function Declarations. For Function Declarations, there's just no syntax possibility to add a one more "internal" name.

Sometimes, when we need a reliable internal name, it's the reason to rewrite a Function Declaration to Named Function Expression form.
```

## Summary

Functions are objects.

Here we covered their properties:

- `name` -- the function name. Exists not only when given in the function definition, but also for assignments and object properties.
- `length` -- the number of arguments in the function definition. Rest parameters are not counted.

If the function is declared as a Function Expression (not in the main code flow), and it carries the name, then it is called a Named Function Expression. The name can be used inside to reference itself, for recursive calls or such.

Also, functions may carry additional properties. Many well-known JavaScript libraries make great use of this feature.

They create a "main" function and attach many other "helper" functions to it. For instance, the [jquery](https://jquery.com) library creates a function named `$`. The [lodash](https://lodash.com) library creates a function `_`. And then adds `_.clone`, `_.keyBy` and other properties to (see the [docs](https://lodash.com/docs) when you want learn more about them). Actually, they do it to lessen their pollution of the global space, so that a single library gives only one global variable. That reduces the possibility of naming conflicts.

So, a function can do a useful job by itself and also carry a bunch of other functionality in properties.
