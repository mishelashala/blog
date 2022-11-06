# Result Type - TypeScript

The result type is a pattern used in functional programming languages used to model the success or the failure of an operation. It can be very useful to model expressive domains and to make implicit assumptions about a function explicit.

## Total Functions

A total function is a mathematical concept. The definition is pretty simple, a total function is a function such that for every input we have a corresponding output.

In mathematics all functions are total functions, but in programming not all functions are total functions. In other words, functions not always return a value for every input.

Let's take a look at the `AgeFactory` function:

```ts
type Age = number;

// AgeFactory :: number -> Age
function AgeFactory(value: number): Age {
  if (typeof value !== "number") {
    throw new Error("Age: value must be a number");
  }

  if (value < 0) {
    throw new Error("Age: Invalid value");
  }

  return value;
}
```

The function `AgeFactory` takes a `number` as input and returns an `Age` type as result. Or at least that's what the signature of `AgeFactory` tell us. But that is not true, the siganture of `AgeFactory` is lying to us.

If we pass a negative number, a number greater than `160` or anything besides a number the function throws an error instead of returning an `Age`. In other words, `AgeFactory` is not a total function.

The problem with non-total functions, at least from a functional programming perspective, is that they are not reliable nor predictible.
The signature has no way of telling us that this function throws an exeption, therefore we cannot be sure under what circunstances this function will work or not.

## Turning a non-total function into a total function

One approach we can follow is to avoid throw exceptions. Instead of throwing the error as an exception, we can return it as part of the result of the function.

```ts
type Age = number;

// AgeFactory :: number -> Age | Error
function AgeFactory(value: number): Age | Error {
  if (typeof value !== "number") {
    return new Error("Age: value must be a number");
  }

  if (value < 0) {
    return new Error("Age: Invalid value");
  }

  return value;
}
```

Now `AgeFactory` returns _either_ an `Age` value type or an `Error` object. With this change `AgeFactory` behaves like a total function, because for every input we have an output. One thing to note is that for a function to be total all the outputs don't have to be of the same type, the only requirement is that every input is mapped to an output.

By returning the `Error` instead of throwing an exception we are forced to change the way we do error handling. Instead of wrapping our calls within a `try/catch` block we have to add a logical guard to make sure we are working with an `Error` or with an `Age`.

```ts
// previous approach
try {
  const result = AgeFactory(-1);
} catch (err) {
  // handle error
}

// new appraoch
const result = AgeFactory(-1);

if (result.message) {
  // handle error
}
```

Every `Error` object has a `message` property with the reason for the failure, we can check if the `message` property is present in order to know if the result type is an `Error` object or something else. For now we are going to use this naive approach, later we'll come up with a more sophisticated mechanism, for now, the current implementation is more than enough.

One thing we can do in the meantime is just add a logical guard function that allow us to encapsulate the differentiation process:

```ts
const isFailure = (result: Age | Error): result is Error => {
  return typeof result === "object" && result.message !== undefined;
};
```

A logical guard is a function that allows you to narrow down the type of a variable. In this case our function `isFailure` allows us to narrow down from a `Age | Error` to a `Error`.

Logical guards are expressions or functions that return a boolean. In this case if the result is true, then it means that the result is an `Error`, otherwise it means that result is an `Age`.

```ts
const isFailure = (result: Age | Error): result is Error => {
  return typeof result === "object" && result.message !== undefined;
};

// this returns false
if (isFailure(AgeFactory(1))) {
}

// this returns true
if (isFailure(AgeFactory(-1))) {
}
```

One added benefit of using a logical guard to differentiate types is that it allow us to change the internals of the type definition without forcing the consumer to change the way they use type.

## A general abstraction

If we change the perspective a little bit we can notice that our `AgeFactory` function produces a `result`. This result production can be a `success` or it can be a `failure`. When it is a `success` it returns an `Age`, and when it is a `failure` it returns an `Error` object.

This allows us to come up with a general abstraction: this function either returns a `success` containing an `Age` or either returns a `failure` containing an `error` object.

```ts
type Result = { ok: boolean; data: Age | Error };
```

We can represent our `Result` type using an aliased type object. In this case our `Result` type has two properties: `ok` which can be `true` or `false`; and `data` that can be an `Age` or an `Error`.

When the result is a success then `ok` will be `true` and the data will contain an `Age`, when the result is a failure the `ok` property will be equal to `false` and the `data` will contain an `Error` object.

And from this general abstraction we can update the signature and the implementation of `AgeFactory`:

```ts
type Age = number;

type Result = { ok: boolean; data: Age | Error };

function AgeFactory(value: number): Result {
  if (typeof value !== "number") {
    return { ok: false, data: new Error("Age: value must be a number") };
  }

  if (value < 0) {
    return { ok: false, data: new Error("Age: Invalid value") };
  }

  return { ok: true, data: value };
}
```

This solves the problem we were facing at the beginning. Now the signature of the function is telling us that under certain conditions this function will return a `Failure` with an `Error` inside.

This also introduces a small inconvenient: we have to unwrap the data in order to access the value.

```ts
const age = AgeFactory(1);
console.log(age.data);
```

This is an issue that we will solve later.

The change on the result type forces us to update the `isFailure` function:

```ts
const isFailure = (result: Result): result is { ok: false; data: Error } => {
  return typeof result === "object" && !result.ok;
};

const result = AgeFactory(-1);

// this is true
if (isFailure(result)) {
  // handle error
} else {
  // do something with it
}
```

Because we had the logical guard encapsulated we did not have to update the `if` statement.

And now that we have an `isFailure` logical guard and a more sophisticated `Result` type we could come up with an `isSuccess` logical guard:

```ts
const isSuccess = (result: Result): result is { ok: true; data: Age } => {
  return typeof result === "object" && result.ok;
};

const result = AgeFactory(1);

// this is true
if (isSuccess(result)) {
  // do something with it
} else {
  // handle error
}
```

Notice how are narrowing the type from a `{ ok: boolean; data: Age | Error }` to a `{ ok: false; data: Error; }` in the case of `isFailure` and how are narrowing the type from a `{ ok: boolean; data: Age | Error }` to a `{ ok: true; data: Age; }` in the case of `isSuccess`. This seems like a small detail, but in reality it tell us something really important: both types are exclusive.

There is no scenario in which a `Failure` would have `ok` equal to `true` and `data` containing an `Age`. Also, there's no scenario in which a `Success` would have `ok` equal to `false` and `data` containing an `Error`.

## Narrowing down Failure and Success

From this new insight we can model `Failure` and `Success` independentely:

```ts
type Success = { ok: true; data: Age };
type Failure = { ok: false; data: Error };
```

And from these two new types we can build our `Result` type:

```ts
type Result = Success | Failure;
```

This new model makes the previously discussed implicit assumptions explicit.

This new model also forces us to update the logical guards:

```ts
const isFailure = (result: Result): result is Failure => {
  return typeof result === "object" && !result.ok;
};

const isSuccess = (result: Result): result is Success => {
  return typeof result === "object" && result.ok;
};
```

Now we can use each corresponding type on the `is` clause in our logical guards. Also from this we can see that the implementations of both `isFailure` and `isSuccess` are a little bit cleaner.

## Making Success a general abstraction

Our `Result` type has one problem: it's implementation is tied to both `Age` and `Error` types. If we wanted to use `Result` for other types we can't.

In order to make them work with different types we have to use generic in its type definition.

```ts
type Success<T> = { ok: true; data: T };
type Failure = { ok: false; data: Error };
type Result<T> = Success<T> | Failure;
```

And by changing our type definitions we have also to change our logical guards once again:

```ts
const isFailure = <T>(result: Result<T>): result is Failure => {
  return typeof result === "object" && !result.ok;
};

const isSuccess = <T>(result: Result<T>): result is Success<T> => {
  return typeof result === "object" && result.ok;
};
```

And now we can use result both on our `EmailFactory` and our `AgeFactory` functions:

```ts
type Email = string;

function EmailFactory(value: string): Result<Email> {
  return { ok: true, data: value };
}

type Age = number;

function AgeFactory(value: number): Result<Age> {
  if (typeof value !== "number") {
    return { ok: false, data: Error("Age: value must be a number") };
  }

  if (value < 0) {
    return { ok: false, data: new Error("Age: Invalid value") };
  }

  return { ok: true, data: value };
}
```

```ts
const ageResult = AgeFactory(-1);

if (isFailure(ageResult)) {
  // handle error
}

const emailResult = EmailFactory("example@email.com");

if (isFailure(emailResult)) {
  // handle error
}
```

As you can see we are not indicating the type when we are calling the `isFailure` function, `TypeScript` is smart enough to infer the type for us.

Now that we have made our `Success` type a general abstraction, why not make also `Failure` a general abstraction.

## Making Failure a general abstraction

First we need to understand the rationale behind this. Initially we would be tempted to think that `Failure` doesn't need to be a general abstraction because it will always hold an `Error`. And that is partially true. But just because we have an `Error` object in `JavaScript` it doesn't mean that we should be forced to always use `Error` to represent our objects. We could use any value type to represent errors.

We can update the type definition of `Failure` to make it more flexible and allow us to work with other types beside `Error` by using `generics`.

```ts
type Failure<T> = { ok: false; data: T };
```

And by updating the type definition of `Failure` we are also forced to update the `Result` type definition:

```ts
type Result<T, R> = Success<T> | Failure<R>;
```

And finally we have to update the signature of our logical guards:

```ts
const isFailure = (result: Result<T, R>): result is Failure<R> => {
  return typeof result === "object" && !result.ok;
};

const isSuccess = (result: Result<T, R>): result is Success<T> => {
  return typeof result === "object" && result.ok;
};
```

This last change can make the code a little bit harder to read if you are not familiar with `generics`, but once you are familiar with them it is pretty straight-forward.

Since we know that the `Failure` type represents a sort of `Error` we don't need to have an `Error` inside a `Failure`, we could just move away from that and represent the error as a string.

```ts
function AgeFactory(value: number): Result<Age, String> {
  if (typeof value !== "number") {
    return { ok: false, data: "Age: value must be a number" };
  }

  if (value < 0) {
    return { ok: false, data: "Age: Invalid value" };
  }

  return { ok: true, data: value };
}

const age = AgeFactory(-1);
if (isFailure(age)) {
  console.log(age.data);
}
```

## Making error domain objects

Now that we can use anything we want to represent errors we are able to model our errors as `domain concepts`:

```ts
type AgeNotANumber = "Age: value must be a number.";
type AgeOutOfRange = "Age: invalid value.";
type AgeError = AgeNotANumber | AgeOutOfRange;

function AgeFactory(value: number): Result<Age, AgeError> {
  if (typeof value !== "number") {
    return { ok: false, data: "Age: value must be a number." };
  }

  if (value < 0) {
    return { ok: false, data: "Age: invalid value." };
  }

  return { ok: true, data: value };
}

const age = AgeFactory(-1);
if (isFailure(age)) {
  console.log(age.data);
}
```

By treating errors as domain concepts we are not only introducing more semantic and richness into our model, but also narrowing down the errors that `AgeFactory` can return. If we try to return something that is not an `AgeError` we will get a compile-time error:

```ts
function AgeFactory(value: number): Result<Age, AgeError> {
  if (typeof value !== "number") {
    // this throws an error at compile-time
    return { ok: false, data: "Age: invalid error." };
  }

  if (value < 0) {
    return { ok: false, data: "Age: invalid value." };
  }

  return { ok: true, data: value };
}
```

## Encapsulating the creation mechanism

One problem we've been ignoring for a while is the chore that it is creating both `Success` and `Failure` objects. It is not only we are duplicating code over and over again, but error prone.

We need a way to create both `Failure` and `Success` objects. For that we are going to create two factory functions:

```ts
function success<T>(data: T): Success<T> {
  return { ok: true, data };
}

function failure<T>(data: T): Failure<T> {
  return { ok: false, data };
}
```

With these two functions not only we will be able to remove duplication, but we will also be able to encapsulate the creation process. This gives us the added benefit of centralizing sources of change. If we change the type definition of either `Success` or `Failure` we would have to update only these two functions instead of hunting down searching for places were we created ad-hoc objects.

```ts
function AgeFactory(value: number): Result<Age, String> {
  if (typeof value !== "number") {
    return failure("Age: value must be a number");
  }

  if (value < 0) {
    return failure("Age: Invalid value");
  }

  return success(value);
}
```

The same thing happens as with our factory functions as with our logical guards: we don't have to tell the type, `TypeScript` will infer it for us.

## Encapsulating the unwrapping mechanism

One recurrent problem that we have been encountering is the need to unwrap the value from the `Result` type:

```ts
const age = AgeFactory(1);
console.log(age.data);
```

This is a problem because, first, it forces the consumer to know the implementation details of the `Result` type, and second because if we change the type definition of `Result` we would have to update every single piece of code where we access the internal value of `Result`.

To encapsulate the access mechanism we just need to create an accessor function:

```ts
function getSuccess<T>(result: Success<T>): T {
  return result.data;
}

function getFailure<T>(result: Failure<T>): T {
  return result.data;
}
```

We are defining two individual functions because we want always to make sure the value we are passing has already been narrowed down, by doing so we don't have to use a logical guard inside the functions.

```ts
const result = AgeFactory(-1);
if (isFailure(result)) {
  const error = getFailure(result);
  console.log("error:", error);
} else {
  const age = getSuccess(result);
  console.log("age:", age);
}
```

## Opening the door

All seems good and dandy, but why would we want to use the `Result` type?

By modeling our `AgeFactory` function as a total function and introducing the `Result` type we open the door to functional programming patterns, specifically to `monadic operations`, `monadic objects` and `railway oriented programming`.

All those concepts are out of the scope of this post, but we'll discuss them later.

## Quick Recap

By using the `Result` type we do mainly three things: we turn our functions into total functions; we make the implicit assumptions of the behavior of our functions explicit; we make our functions predictible; and make our modeling more expresive and richer.

The downside of using the `Result` type is the need to create our own custom implementation, adding the extra complexity of all the utilty functions and having to wrap and unwrap values all the time.

The `Result` type can be a powerful type, but also cumbersome to work with.
