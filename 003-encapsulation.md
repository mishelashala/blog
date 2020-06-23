# Encapsulation

In the context of OOP: encapsulation is a mechanism or technique to restrict access to properties

One of the mechanisms used to achieve this in OOP languages is thru the use of access modifiers (public, private, protected).

## Why would we want encapsulation?

In OOP we don't treat objects just as a bag with properties, a data structure, or a type.

We visualize it as unit with its own internal state and internal processes. The entire idea behind encapsulation is not to worry about that internal state, and those internal processes. So, the goal of the object is just to expose us information to work with: a public interface.

## It's all about access

Prior to es2019 we didn't have access modifiers on JavaScript. In fact all properties in objects are pubic.

```js
const john = {
  name: "John Doe",
  age: 21,
};

// I can access `name`
john.name; // 'John Doee'

// I can modify `name`
john.name = "Jenny Doe";
```

In this example we have an object with "John Doe" as name, and 21 as age.
Both properties are public, so any one (in any point of the program's life) we can access and modify them.

Then we access the value associated with the property name on the object john and we get (as we expected) "John Doe".

And then in the following line we change the value of name to "Jenny Doe".

Due to the lack of access modifiers some developers came up with a convention:
Everything that is part of the "private" api should be prefixed with an underscore (_).
This convention doesn't change the access modifiers of the properties, it only tells the consumer that it shouldn't touch anything that starts with an underscore (_).

```js
const john = {
  name: "John Doe",
  age: 21,
  _privateKey: "secret",
};

john._privateKey; // 'secret'

// we can modify it
john._privateKey = "oopsis";

john._privateKey; // 'oopsis'
```

If we take the previos object and associate the value `secret` to `_privateKey` we are telling to whoever is gonna use this object/module that it shouldn't touch it

But, again, we have the same issue, and just like with any other property we can try to acces it and modify it

## Meta-properties and Enumerability

Another approached that emerged was to change the enumerability of properties.
Every property has meta-properties (in other words properties of properties).
Two of those properties are enumerable and value. Enumerable is used to indicate if a property should be "listed" when using `for in` loop or `console.log`.

```js
const john = {
  name: "John Doe",
  age: 21,
};

Object.defineProperty(john, "_privateKey", {
  enumerable: false, // "hide the prop"
  value: "secret",
});

// look ma, it's gone!
console.log(john); // { name: 'John Doe', age: 21 }

// well yes, but actually no
john._privateInfo; // 'secret'

// oh sh*t, here we go again
john._privateInfo = "oopsis";
```

We have again our object `john` with `John Doe` as name and `21` as `age`.

And to define a new property and set its meta properties we use `Object.defineProperty`.

Object.define property takes as first argument the object that we're gonna attach the property, as second argument the name of the property and as third argument an object representing the meta-properties (in which the value is included).

So we set the property `_privateKey` on the object john, with the value `secret` and we set its enumerability to `false`.

With this if we try to log the object `john` we'll only get the properties `name` and `age`.

All properties are enumerables by default.

Even though this seems to work, we can still access the property.

And even worse, we can still modify it.

And even though we cannot see the properties using `for in` or `console.log` get can get a list of all own properties (not enumerables too) using `Object.getOwnPropertyNames`

```js
Object.getOwnPropertyNames(john); // ['name', 'age', '_privateKey']
```

With `Object.getOwnPropertyNames` we get an array with `name`, `age` and `_privateKey`
So, this approach doesn't give us encapsulation neither.

Fortunately we have a third approach that works (kinda).

## Factory Functions + Closures

Instead of using object literals (or functions as constructors) to create objects we're gonna use factory functions and closures.

```js
// person :: (String, Number, String) -> IPerson
function person(name, age, privateKey) {
  return {
    age,
    name,
  };
}

const john = person("John Doe", 21, "secret");
// And poof... just like that  we don't know about `privateKey` any moar.
john; // { age: 21, name: 'John Doe' }
```

We create a function called `person` that takes a `name`, `age` and `privateKey` as arguments and returns an object with the properties `name` and `age`.

If we create a person and pass "John Doe', 21 and "Secret" we get an object with only the properties age and name.

Now encapsulation is not only about data privacy or information hiding, it's about limiting the access of a property or method.

In many cases we need to have read-only properties.
Properties that can't (or shouldn't) change over time, but that we should be able to access.

```js
// person :: (String, Number, String) -> IPerson
function person(name, age, apiKey) {
  // getApiKey :: () -> String
  const getApiKey = () => apiKey;

  return {
    age,
    name,
    getApiKey,
  };
}

const john = person("John Doe", 21, "123");
john.getApiKey(); // '123'

// jenny is a completely different object
const jenny = person("Jenny Doe", 22, "456");
jenny.getApiKey(); // '456'
```

In this case we define a getter called `getApiKey` and we return it on the list of properties of the object.
And with this getter we can access the `apiKey` inside our closure.

The cool thing about this approach is that we can create as many  objects as we want.

Every time we execute the function `person` we create a new function scope, and a new closure (thru the function `getApiKey`). So, every object created thru `person`
has its own reference to a unique scope with unique values.

If we try to add a property `apiKey` to the object, there's nothing to stop us.

```js
// oh well
john.apiKey = "other-api-key";
john.getApiKey(); // '123'
```

This doesn't change the value returned by `getApiKey` because the value it references is not on the object itself, but in a scope.

The trick is to think of objects in terms of modules.

The object/module exposes us an interface to work with, so we don't have to worry about its internals.

If we need to change an internal state then the object should expose us a mechanism on the api to do so.

This is the purpose of setters:

```js
// person :: (String, Number, String) -> IPerson
function person(name, age) {
  let n = name;

  // getName :: () -> String
  const getName = () => name;

  // setName :: String -> String
  const setName = (name) => (n = name);

  return {
    age,
    setName,
    getName,
  };
}

const john = person('John Doe', 21)

john.setName('Jenny Doe')
john.getName() // 'Jenny Doe'
```

In this case we have the same factory function person, but instead of exposing the property name we expose a getter `getName` and the setter `setName`.

`setName` is a function that allows us to change the internal value of `name` on the object.

It's considered an anti-pattern to provide a setter and a getter for a private property.

If we provide both then we're giving full access to the property, therefore making the entire idea of having a private property pointless.

## Summary:

Encapsulations is used to limit the access to the internals of an object.

An object is more than a collection of data, but something that we interact with thru a public interface

JavaScript doesn't have access modifiers built-in on objects, but we can use some techniques to try to replicate this behavior. Been the mos effective factory functions + closures.

