---
title: "Javascript-Tricks"
categories:
  - Javascript
tags:
  - Javascript
  - ES5
  - ES6
---

Let's explore some Javascript tricks.

## Nullish Coalescing (??) VS Logical OR (||)

The nullish coalescing operator (??) is a logical operator that returns its right-hand side operand when its left-hand side operand is null or undefined

```javascript
(function () {
  // the right side is executed if value is undefined, null
  // The left side is executed if empty string, 0, NaN
  function tryNullishCoalescing() {
    let value1;
    let test1 = value1 ?? true;
    console.log(test1); // true

    let value2 = null;
    let test2 = value2 ?? true;
    console.log(test2); // true

    let value3 = 0;
    let test3 = value3 ?? true;
    console.log(test3); // 0

    let value4 = "";
    let test4 = value4 ?? true;
    console.log(test4); // ""

    let value5 = 2 * undefined; // NaN ; 2 * null = 0
    let test5 = value5 ?? true;
    console.log(test5); // NaN
  }

  tryNullishCoalescing();
})();
```

The operator `||` is a logical operator that returns its right-hand side operand when its left-hand side operand is null or undefined or empty string or 0 or NaN.

```javascript
(function () {
  // the right side is executed if the value is undefined, null, empty, 0, NaN
  function tryOr() {
    let value1;
    let test1 = value1 || true;
    console.log(test1); // true

    let value2 = null;
    let test2 = value2 || true;
    console.log(test2); // true

    let value3 = 0;
    let test3 = value3 || true;
    console.log(test3); // true

    let value4 = "";
    let test4 = value4 || true;
    console.log(test4); // true

    let value5 = 2 * undefined; // NaN ; 2 * null = 0
    let test5 = value5 || true;
    console.log(test5); // true
  }

  tryOr();
})();
```

Let's take a look to null vs undefined

```javascript
(function () {
  var value;
  console.log(typeof value); // undefined

  var value = null;
  console.log(typeof value); // object

  var value = new Array();
  console.log(typeof value); // object

  //   console.log(undefined == null); // true
  //   console.log(undefined === null); // false
  //   console.log(typeof undefined); // undefined
  //   console.log(typeof null); // object
})();
```

## Optional Chaining (?.) VS Logical AND

The optional chaining operator provides a way to simplify accessing values through connected objects when it's possible that a reference or function may be undefined or null.

```javascript
(function () {
  var person = {
    // address: {
    //   street: "",
    // },
  };

  // console.log(person.address); // undefined
  //  console.log(person.address.street);// Cannot read property 'street' of undefined

  // expressions that can be converted to false are: undefined, null, NaN,0, ""
  console.log(person && person.address && person.address.street);

  console.log(person?.address?.street);
})();
```

## defer Attribute

No need to put the "script" tag in the "body".
The script will be executed after the document has been parsed.

```html
<html lang="en">
  <head>
    <script src="./script.js" defer></script>
  </head>
  <body></body>
</html>
```
