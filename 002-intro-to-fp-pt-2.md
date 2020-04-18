## Introduction to functional programming (part 2)

Separation of data structures and operations

On OOP fields and methods are part of a semantic unit. On FP that's not the case, in the case of
javascript the term object is used to represent a data structure (in this case it's called a map or
dictionary).

```js
var john = {
  name: "john doe",
  age: 21,
  // john ~> sayHello :: () -> String
  sayHello() {
    return `Hello, my name is ${this.name}, and I am ${this.age} yo`;
  },
};

john.sayHello(); // 'Hello, my name is john doe, and I am 21 yo'
```

```js
var john = {
  name: "john doe",
  age: 21,
};

// sayHello :: User -> String
function sayHello(user) {
  return `Hello, my name is ${user.name}, and I am ${user.age}`;
}

sayHello(john); // 'Hello, my name is john doe, and I am 21 yo'
```
