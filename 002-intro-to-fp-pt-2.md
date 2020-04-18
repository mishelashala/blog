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

Objects have internal state:

```js
const john = {
  name: 'John Doe',
  timesCalled: 0,
  // sayHello :: () -> ()
  sayHello: () => {
    ++this.timesCalled
    console.log(`Hi, my name is ${this.name}, and I was called ${this.timesCalled}`)
  }
}

john.sayHello() // > 'Hi, my name is John Doe, and I was called 1 times'
john.sayHello() // > 'Hi, my name is John Doe, and I was called 2 times'
```

Objects don't enforce immutability by default

```js
const john = {
  name: 'John Doe',
  setName: (newName) => {
    this.name = newName
    return this
  }
}

john.name // 'John Doe'
john.setName('Jenny Doe')
john.name // 'Jenny Doe'
```

`this` can be problematic

```js
var name = 'Jenny Doe'

var john = {
  name: 'John Doe',
  sayHello() {
    console.log(`Hi, my name is ${this.name}`)
  }
}

john.sayHello() // 'Hi, my name is John Doe'

var sayHello = john.sayHello

sayHello() // 'Hi, my name is Jenny Doe'
```

`this` has been "fixed", but... arrow functions are not bullet proof

```js
var name = 'Jenny Doe'

var john = {
  name: 'John Doe',
  // sayHello :: () -> ()
  sayHello: () => {
    console.log(`Hi, my name is ${this.name}`)
  }
}

john.sayHello() // 'Hi, my name is John Doe'

var sayHello = john.sayHello

sayHello() // 'Hi, my name is John Doe'
```

`this` is implicit, things should be explicit (as much as possible)

```js
// sayHello :: User -> ()
function sayHello(user) {
  console.log(`Hi, my name is ${user.name}`)
}

const john = { name: 'John Doe' }
const jenny = { name: 'Jenny Doe' }

sayHello(john) // 'Hi, my name is John Doe'
sayHello(jenny) // 'Hi, my name is Jenny Doe'
```

functions can be as reusable as classes/types

```js
// inc :: Number -> Number
function inc(num) {
  return num + 1
}

inc(1) // 2
inc(2.2) // 3.2
inc(-4) // 3
```
