# 33. 7번째 데이터 타입 Symbol

## 📌 33.1 심벌이란?

`심벌`은 자바스크립트의 6개의 타입 즉, `문자열`, `숫자`, `불리언`, `undefined`, `null`, `객체`에 더불어 ES6에서 도입된 7번째 타입이다. 변경 불가능한 원시타입의 값으로, 심벌 값은 **다른 값과 중복되지 않는 유일무이한 값이다.** 따라서 주로 이름의 충돌 위험이 없는 유일한 **프로퍼티 키**를 만들기 위해 사용한다.

> _10.3 프로퍼티_ <br> 
> _프로퍼티 키로 사용할 수 있는 값은 빈 문자열을 포함하는 모든 문자열 또는 심벌 값이다._

## 📌 33.2 심벌 값의 생성

### ▶️ Symbol 함수

심벌 값은 다른 원시값과는 다르게 리터럴 표기법을 사용할 수 없고 Symbol 함수를 호출하여 생성해야 한다.

```js
// Symbol 함수를 호출하여 유일무이한 심벌 값을 생성한다.
const mySymbol = Symbol();
console.log(typeof mySymbol); // symbol

// 심벌 값은 외부로 노출되지 않아 확인할 수 없다.
console.log(mySymbol); // Symbol()

// new 연산자로 객체를 생성하는 것과는 다르다.
new Symbol(); // TypeError: Symbol is not a constructor
```

Symbol 함수에는 문자열을 인수로 전달할 수 있다. 하지만 이는 심벌 값에 대한 설명으로, 디버깅 용도로만 사용된다.

```js
// 심벌 값에 대한 설명이 같더라도 유일무이한 심벌 값을 생성한다.
const mySymbol1 = Symbol("mySymbol");
const mySymbol2 = Symbol("mySymbol");

console.log(mySymbol1 === mySymbol2); // false
```

심벌 값도 문자열, 숫자, 불리언과 같이 객체처럼 접근하면 암묵적으로 래퍼 객체를 생성한다.

```js
const mySymbol = Symbol("mySymbol");

// 심벌도 레퍼 객체를 생성한다
console.log(mySymbol.description); // mySymbol
console.log(mySymbol.toString()); // Symbol(mySymbol)
```

심벌 값은 문자열이나 숫자 타입으로는 암묵적 변환되지 않고 불리언 타입으로는 암묵적 변환이 된다.

```js
const mySymbol = Symbol();

// 심벌 값은 암묵적으로 문자열이나 숫자 타입으로 변환되지 않는다.
console.log(mySymbol + ""); // TypeError: Cannot convert a Symbol value to a string
console.log(+mySymbol); // TypeError: Cannot convert a Symbol value to a number

// 불리언 타입으로는 암묵적으로 타입 변환된다.
console.log(!!mySymbol); // true

// if 문 등에서 존재 확인을 위해 사용할 수 있다.
if (mySymbol) console.log("mySymbol is not empty.");
```

### ▶️ Symbol.for / Symbol.keyFor 메서드

`Symbol.for` 메서드는 인수로 전달받은 문자열을 키로 사용하여 전역 심벌 레지스트리에서 해당 키와 일치하는 심벌 값을 검색한다. <br>
_💡 전역 심벌 레지스트리에는 키와 심벌 값의 쌍들이 저장되어 있다._

-   검색에 성공하면 검색된 심벌 값을 반환한다.
-   검색에 실패하면 새로운 심벌 값을 생성하여 Symbol.for 메서드의 인수로 전달된 키로 전역 심벌 레지스트리에 저장한 후, 생성된 심벌 값을 반환한다.

```js
// 전역 심벌 레지스트리에 mySymbol이라는 키로 저장된 심벌 값이 없으면 새로운 심벌 값을 생성
const s1 = Symbol.for("mySymbol");
// 전역 심벌 레지스트리에 mySymbol이라는 키로 저장된 심벌 값이 있으면 해당 심벌 값을 반환
const s2 = Symbol.for("mySymbol");

console.log(s1 === s2); // true
```

`Symbol.keyFor` 메서드를 사용하면 전역 심벌 레지스트리에 저장된 심벌 값의 키를 추출할 수 있다.

```js
// 전역 심벌 레지스트리에 mySymbol이라는 키로 저장된 심벌 값이 없으면 새로운 심벌 값을 생성
const s1 = Symbol.for("mySymbol");
// 전역 심벌 레지스트리에 저장된 심벌 값의 키를 추출
Symbol.keyFor(s1); // -> mySymbol

// Symbol 함수를 호출하여 생성한 심벌 값은 전역 심벌 레지스트리에 등록되어 관리되지 않는다.
const s2 = Symbol("foo");
// 전역 심벌 레지스트리에 저장된 심벌 값의 키를 추출
Symbol.keyFor(s2); // -> undefined
```

위의 예제에서 볼 수 있듯이 `Symbol` 함수를 호출하여 생성한 심벌 값은 검색할 수 있는 **키**를 지정할 수 없다.       
따라서 전역 심벌 레지스트리에 등록되어 관리되지 않는다.       
하지만 `Symbol.for` 메서드를 사용하면 심벌 값을 단 하나만 생성하여 전역 심벌 레지스트리를 통해 애플리케이션 전역에서 공유가 가능하다.       

## 📌 33.3 심벌과 상수

```js
// 위, 아래, 왼쪽, 오른쪽을 나타내는 상수를 정의한다.
// 이때 값 1, 2, 3, 4에는 특별한 의미가 없고 상수 이름에 의미가 있다.
const Direction = {
    UP: 1,
    DOWN: 2,
    LEFT: 3,
    RIGHT: 4,
};

// 변수에 상수를 할당
const myDirection = Direction.UP;

if (myDirection === Direction.UP) {
    console.log("You are going UP.");
}
```

위의 예제처럼 값에는 특별한 의미가 없고 상수 이름에 의미가 있는 경우가 있다.    
이때 **값이 변경될 수 있고, 다른 변수 값과 중복될 수도 있다.**    
이런 경우에 **심벌 값**을 사용할 수 있다.

```js
// 위, 아래, 왼쪽, 오른쪽을 나타내는 상수를 정의한다.
// 중복될 가능성이 없는 심벌 값으로 상수 값을 생성한다.
const Direction = {
    UP: Symbol("up"),
    DOWN: Symbol("down"),
    LEFT: Symbol("left"),
    RIGHT: Symbol("right"),
};

const myDirection = Direction.UP;

if (myDirection === Direction.UP) {
    console.log("You are going UP.");
}
```

## 📌 33.4 심벌과 프로퍼티 키

```js
const obj = {
    // 심벌 값으로 프로퍼티 키를 생성
    [Symbol.for("mySymbol")]: 1,
};

obj[Symbol.for("mySymbol")]; // -> 1
```

심벌 값으로 프로퍼티 키를 사용하려면 심벌 값에 대괄호를 붙여야한다. 프로퍼티에 접근할 때도 마찬가지로 대괄호를 사용한다. <br>
**심벌 값은 유일무이한 값이므로 심벌 값으로 프로퍼티 키를 만들면 다른 프로퍼티 키와 절대 충돌하지 않는다.**

## 📌 33.5 심벌과 프로퍼티 은닉

심벌 값을 프로퍼티 키로 사용하여 프로퍼티를 생성하면 외부에 노출할 필요가 없는 프로퍼티를 **은닉**할 수 있다.

```js
const obj = {
    // 심벌 값으로 프로퍼티 키를 생성
    [Symbol("mySymbol")]: 1,
};

for (const key in obj) {
    console.log(key); // 아무것도 출력되지 않는다.
}

console.log(Object.keys(obj)); // []
console.log(Object.getOwnPropertyNames(obj)); // []
```

하지만 ES6에서 도입된 `Object.getOwnPropertySymbols` 메서드를 사용하면 심벌 값을 프로퍼티 키로 생성한 프로퍼티를 찾을 수 있기 때문에 프로퍼티를 완전하게 숨길 수 있는 것은 아니다.

```js
const obj = {
    // 심벌 값으로 프로퍼티 키를 생성
    [Symbol("mySymbol")]: 1,
};

// getOwnPropertySymbols 메서드는 인수로 전달한 객체의 심벌 프로퍼티 키를 배열로 반환한다.
console.log(Object.getOwnPropertySymbols(obj)); // [Symbol(mySymbol)]

// getOwnPropertySymbols 메서드로 심벌 값도 찾을 수 있다.
const symbolKey1 = Object.getOwnPropertySymbols(obj)[0];
console.log(obj[symbolKey1]); // 1
```

## 📌 33.6 심벌과 표준 빌트인 객체 확장

**일반적으로 표준 빌트인 객체에 사용자 정의 메서드를 직접 추가하여 확장하는 것은 권장하지 않는다.** 그 이유는 개발자가 직접 추가한 메서드와 미래에 표준 사양으로 추가될 메서드의 이름이 중복될 수 있기 때문이다.

```js
// 표준 빌트인 객체는 읽기 전용으로 사용하는 것이 좋다.
Array.prototype.sum = function () {
    return this.reduce((acc, cur) => acc + cur, 0);
};

[1, 2].sum(); // -> 3
```

하지만 중복될 가능성이 없는 심벌 값으로 프로퍼티 키를 생성하여 표준 빌트인 객체를 확장한다면 다른 프로퍼티 키와 충돌할 위험이 없어, 안전하게 확장이 가능하다.

```js
// 심벌 값으로 프로퍼티 키를 동적 생성하면 다른 프로퍼티 키와 절대 충돌하지 않아 안전하다.
Array.prototype[Symbol.for("sum")] = function () {
    return this.reduce((acc, cur) => acc + cur, 0);
};

[1, 2][Symbol.for("sum")](); // -> 3
```

## 📌 33.7 Well-known Symbol

자바스크립트가 기본 제공하는 빌트인 심벌 값이 있다. 빌트인 심벌 값은 `Symbol` 함수의 프로퍼티에 할당되어 있다.

<img width="800" alt="image" src="https://user-images.githubusercontent.com/87116017/221399044-d84e9833-479f-4c3c-8818-e0d20ea2c05a.png"><br>_Symbol 함수의 프로퍼티_

빌트인 심벌 값을 **Well-known Symbol**이라 부르는데 이는 자바스크립트 엔진의 내부 알고리즘에 사용된다. <br>
예를들어 `Array`, `String`, `Map`, `Set`, `TypedArray등` `for ... of` 문으로 순회 가능한 빌트인 이터러블은 Well-known Symbol인 `Symbol.iterator`를 키로 갖는 메서드를 가진다. `Symbol.iterator` 메서드를 호출하면 이터레이터를 반환하도록 하는 **이터레이션 프로토콜**을 준수한다..
<br>
<br>
만약 빌트인 이터러블이 아닌 일반 객체를 이터러블처럼 동작하도록 구현하고 싶다면 이터레이션 프로토콜을 따르면 된다. 즉, `Symbol.iterator`를 키로갖는 메서드를 객체에 추가하고 이터레이터를 반환하도록 구현하면 그 객체는 이터러블이 된다.

```js
// 1 ~ 5 범위의 정수로 이루어진 이터러블
const iterable = {
    // Symbol.iterator 메서드를 구현하여 이터러블 프로토콜을 준수
    [Symbol.iterator]() {
        let cur = 1;
        const max = 5;
        // Symbol.iterator 메서드는 next 메서드를 소유한 이터레이터를 반환
        return {
            next() {
                return { value: cur++, done: cur > max + 1 };
            },
        };
    },
};

for (const num of iterable) {
    console.log(num); // 1 2 3 4 5
}
```

▶️ 이처럼 심벌은 중복되지 않는 상수값을 생성하는 것은 물론 기존에 작성된 코드에 영향을 주지 않고 새로운 프로퍼티를 추가하기 위해, 즉 하위 호환성을 보장하기 위해 도입되었다.
