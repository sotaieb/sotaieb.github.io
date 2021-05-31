---
title: "Javascript-What we should Know"
categories:
  - Javascript
tags:
  - Javascript
  - ES5
  - ES6
---

Let's explore what we should know about Javascript.

## Data Types and structure types

- Data types (primitives)

  - undefined
  - boolean
  - number
  - string
  - bigint
  - symbol

- Structure Types

  - object (array, map, set, classes with new keyword)
  - function
  - null (typeof instance === "object")

Immutable Types(number, string, null, undefined, boolean)

Mutable Types:(object, array, map, set, function)

## Strict Mode

Strict mode is declared by adding "use strict"; to the beginning of a script or a function.
Using a variable, without declaring it, is not allowed

```javascript
"use strict"; // global scope.
//x = 10; // throws ReferenceError, variable should be declared with strict mode.
```

```javascript
(function () {
  "use strict"; // function scope
})();
```

## Hoisting

Hoisting is the process of moving all the declarations (variables and functions) to the top of the scope.

```javascript
"use strict";

(function () {
  // console.log(a); // ReferenceError: a is not defined

  console.log(v); // undefined
  var v = 10; // ES5: var -- The declaration (var v;) will be hoisted on the top of the scope (initialiaztion (v=10) of the variable is not hoisted).
  console.log(v); // 10

  // Variables defined with let and const are hoisted to the top of the block, but not initialized.
  // it cannot be used until it has been declared.

  // console.log(z);
  // let z; // ReferenceError: Cannot access 'z' before initialization

  // console.log(z);
  // const y; // const should be initilized.
  // const z = "z"; // ReferenceError: Cannot access 'z' before initialization

  //   var a = "a";
  //   var a;
  //   console.log(a); // a

  //   let b;
  //   let b; // SyntaxError: Identifier 'b' has already been declared

  //   const c;
  //   const c; // SyntaxError: Missing initializer in const declaration

  //   var d;
  //   var d;
  //   console.log(d); // undefined

  //   var g = "g";
  //   var g;
  //   console.log(g); // g

  //   if (true) {
  //     let e = "e";
  //     console.log(e); // e
  //   }
  //   let e = "ee";
  //   console.log(e); // ee

  //   if (true) {
  //     const f = "f";
  //     console.log(f); // f
  //   }
  //   const f = "ff";
  //   console.log(f); // ff
})();
```

## Keyword this

The use of "this" varies depending on the context and also depending on the mode of JavaScript (strict mode or not).

We're going to explore different use cases.

- By default, "this" refers to the global object (window in browser, global in node):

```javascript
if (typeof window !== "undefined") {
  console.log(this === window); // browser context
} else {
  console.log(this === global); // node context
}
```

- "this", when invoked inside a function, refers to the global object.
  In strict mode, it refers to undefined.

```javascript
(function () {
  // strict mode is not enabled
  // "this" refers to global context

  console.log(`(this === window) => ${this === window}`); // true
})();
```

```javascript
(function () {
  "use strict";

  // strict mode is enabled
  console.log(`(this === undefined) => ${this === undefined}`); // true
})();
```

- "this", refers to the new instance.

```javascript
(function () {
  function Person(name, surname) {
    this.name = name;
    this.surname = surname;
    this.fullName = function () {
      return `${this.name}  ${this.surname}`;
    };
  }
  console.log(new Person("richard", "stellman").fullName());
})();
```

- "this", refers to the invoker object.

```javascript
(function () {
  const o = {
    attr: "val",
    func: function () {
      return this === window; // "this" refers to the object
    },
  };

  let func = o.func;

  // true => "this" refers to global context.
  console.log(func());

  // false => "this" refers to the object context.
  console.log(o.func());
})();

(function () {
  "use strict";
  const o = {
    attr: "val",
    func: function () {
      return this === window; // "this" refers to the object
    },
  };

  let func = o.func;

  // false => "this" refers to the object context.
  console.log(func());

  // false => "this" refers to the object context.
  console.log(o.func());
})();
```

- "this" refers to a custom instance with bind, apply,call.

```javascript
(function () {
  "use strict";
  function Person(name) {
    this.name = name;
    this.displayName = function () {
      console.log(this.name);
    };
  }

  // Every function has call, bind, and apply methods.
  // These methods can be used to set a custom value to this in
  // the execution context of the function.

  let p1 = new Person("myname1");
  p1.displayName(); // myname1

  let p2 = new Person("myname2");
  p2.displayName(); // myname2

  let f = p1.displayName.bind(p2);
  f(); // myname2

  p1.displayName.call(p2); // myname2
  p1.displayName.apply(p2); // myname2
})();
```

- "this" in arrow function, keeps on referring to the same object it’s referring to outside of the function.

```javascript
(function () {
  "use strict";

  let incrementer = {
    sum: 0,
    computeSum: function (values) {
      // inner function as arrow function
      values.forEach((value) => (this.sum += value));
    },
  };

  incrementer.computeSum([1, 2, 3]);
  console.log(incrementer.sum);
})();

/*(function () { // error
  "use strict";

  let incrementer = {
    sum: 0,
    computeSum: function (values) {
      //  in strict mode, "this" is undefined in the inner function
      values.forEach(function (value) {
        this.sum += value; //  Cannot read property 'sum' of undefined
      });
    },
  };

  incrementer.computeSum([1, 2, 3]);
  console.log(incrementer.sum);
})();*/
```
