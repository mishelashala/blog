# Value Objects and Value Types

Value Objects are cool, well, kinda.

## Primitive Obsession

Let's take a look at a simple user type definition:

```ts
type User = {
  age: number;
};
```

A user type is an object that has an age. In this specific example, we are modeling the age as a number.

An age has a semantic value and a set of constraints: it means something and not every number can be a valid age.

When we model everything as a primitive we are incurring in something called `primitive obsession`.

The problem with primitive obsession, as we said at the beginning, is the lack of semantic value and constraint validations.

This is a problem, because we can end up creating a `User` with an invalid age, making the entire state of the `User` invalid.

```ts
const user: User = { age: -1 }; // lel
```

Sure, we can infer what could be the valid values for an age (zero or a positive number), but in more complex domains some values are not as easy to infer.

Let's extend our `User` model and let's add another property called `createdAt`.

```ts
type User = {
  age: number;
  createdAt: number;
};
```

From the name and the type we can infer partially what it is: a number representing the moment at which the `User` was created. But the question arises: what kind of number is it? Is `createdAt` `miliseconds` or `seconds`? We can see that even in this "simple" example we can see the kind of problems that `primitive obsession` can cause.

Primitives are our primary building blocks, even though they are useful, they are not enough. To aliviate this problem we can use value objects.

### What is a value object

A value object is wrapper on top of a primitive value. Since value objects is a pattern that emerged on the OOP they rely heavely on classes. In order to create an Age value object we must create a class that wraps a primitive number value:

```ts
class Age {
  value: number;

  constructor(value: number) {
    this.value = value;
  }
}

const age = new Age(1);
```

As we can see we created a class called Age. When we want to create an instance we just have to call `Age` and pass a number as argument to the constructor.

This process of taking a primitive value and wrapping it within a value object class is called boxing.

```ts
type User = {
  age: Age;
};
```

This also forces us to update the type of definition of our `User` type, and now instead of using a primitive to represent the age we can use an `Age` class.

```ts
const john: User = {
  age: new Age(18),
};
```

And during construction instead of passing a primitive we pass an instance of `Age`.

## Enforcing Invariants

So far we have covered half of the problem: the lack of semantics. But we still have to cover the other half: enforcing invariants.

Value objects can have multiple valid states, we call these _valid_ states invariants. The purpose of enforcing invariants is to make illegal states impossible to represent.

Under the current implementation we are not enforcing any invariant, if we wanted we could create an `Age` instance with a negative number:

```ts
// this should not be allowed
const negativeAge = new Age(-1);
```

In order to enforce invariants we must add a validation during construction. If the provided value is not valid, then we throw an exception, otherwise we just continue with the creation of the object.

```ts
class Age {
  value: number;

  constructor(value: number) {
    if (value < 0) {
      throw new Error("Age: number must be equal or greater than zero");
    }

    this.value = value;
  }
}

// this throws an exception
const age = new Age(-1);
```

And with this change we cover the other half. Now we have both a semantic model and a model that only represents legal states thru the enforcement of invariants.

### Defensive programming

The TypeScript compiler will only protect us until certain point. Once we compile our code to JavaScript all type checks are thrown out of the window and we are left defenseless.

The only way to remediate this is thru the use of a technique called `defensive programming`. The idea is pretty simple: we never blindly trust inputs. In other words, we always verify inputs are valid, not only in content, but also in type.

We already do that: we verify that the number that is being provided is zero or a positive number. We just need to take it one step further and just ask: is this even a number? If that's not the case, then we throw an exception, otherwhise we just let it pass.

```ts
class Age {
  value: number;

  constructor(value: number) {
    if (typeof value !== "number") {
      throw new Error("Age: value must be a number");
    }

    if (value < 0) {
      throw new Error("Age: number must be equal or greater than zero");
    }

    this.value = value;
  }
}
```

With this we make sure that once the protections TypeScript provide us are gone we still have a line of defense against invalid inputs during run time.

### Value Objects as our primitives

By wrapping primitives and injecting semantics into our model we end up creating our own primitives. These primitives become our new building blocks and we can use them to create not only more complex but also richer models.

```ts
type User = {
  age: Age;
};

type Dog = {
  age: Age;
};
```

We can use `Age` as the building of block both `User` and `Dog` types. Not only `Age` provides us a more semantic model for the definition of `Dog` and `User`, but reusability. The rules that apply for the age of both `User` and `Dog` are the same, so we can heavely rely on `Age` to enforce such rules instead of having both `User` and `Dog` enforcing them.

### When to use value objects

Value objects are useful when we want to represent any type of abstract concept, computation or measurement.

`Age` is a both an abstract concept and a measurement. It is an abstract concept because it doesn't exist in the real world, and it is a measurement because it is used to track the time has passed since the moment of birth of a person (or any other life form).

The simplest rule of thumb is just to use value objects everytime we need to have constraints over a primitive value.

If we need to have a string that has certain length or certain pattern then we can use a value object that enforces such rules and provide semantic to what that string represents.

For example, an email must follow a well-defined pattern (this is a constraint), therefore we could use an `Email` value object for that.

```ts
class Email {
  value: string;
  // implementation
  constructor(value: string) {
    if (!isValidEmail(value)) {
      throw new Error("Email: Invalid Email");
    }

    this.value = value;
  }
}

const email1 = new Email(""); // invalid email, throws an exception
const email2 = new Email("john.com"); // invalid email, throws an exception
const email3 = new Email("john.doe@email.com"); // valid email, all good
```

### The question of identity

Value objects lack individual identity. We know two value objects are the same objects because both value objects hold the same value:

```ts
const age1 = new Age(1);
const age2 = new Age(1);

// they hold the same value, therefore are the same
age1.value === age2.value; // true
```

This aspect of value objects make them interchangable. It should not concern us if we replace one value object with another value object that holds the same value, since both are technically the same.

```ts
const age1 = new Age(18);
const user: User = { age: age1 };

// lol, who cares?
const age2 = new Age(18);
user.age = age2;
```

### Value objects are immutable

Since value objects lack identity and are measurements or abstract concepts they are a perfect candidate for immutability.

The primitive 1, for example, cannot be reassigned:

```ts
1 = 2;
```

This is because a 1 cannot become a 2, in other words, 1 is immutable. This is not true only for numbers, every primitive is immutable.

```ts
const age = new Age(1);
age.value = 2; // this should not be allowed
```

The same applies for value objects. They should be immutable. The way to enforce immutability is pretty simple: we just make the value readonly.

```ts
class Age {
  readonly value: number;

  constructor(value: number) {
    if (typeof value !== "number") {
      throw new Error("Age: value must be a number");
    }

    if (value < 0) {
      throw new Error("Age: number must be equal or greater than zero");
    }

    this.value = value;
  }
}

const age = new Age(1);
age.value = 2; // this throws a compile-time error
```

If we need to update a value object, then we don't modify it directely. Instead we create a new object and apply the change to it:

```ts
class Age {
  readonly value: number;

  constructor(value: number) {
    // ...
  }

  setAge(value: number): Age {
    return new Age(value);
  }
}

const age = new Age(1);
const newAge = age.setAge(2); // creates a new Age(2)
```

### Comparability

Since we are defining our own primitives by using classes we cannot rely anymore on the primitive mechanism for equality: the double equal and triple equal operators.

In the case of objects when we compare them with the double or triple equals operators what the interpreter verifies is that they point to the same memory region. If they do, then the interpreter asumes they are the "same".

```ts
const age1 = new Age(1);
const age2 = new Age(1);

// they point to different regions in memory, for the interpreter they are different
age1 === age2; // false
```

This is something that we must always thake into account. Every time we define a new `value object`, we must also provide a mechanism to compare them.

```ts
class Age {
  value: string;

  constructr(value: number) {
    if (typeof value !== "number") {
      throw new Error("Age: value must be a number");
    }

    if (value < 0) {
      throw new Error("Age: number must be equal or greater than zero");
    }

    this.value = value;
  }

  areEqual(ageToCompare: Age): boolean {
    return this.value === ageToCompare.value;
  }
}

const age1 = new Age(1);
const age2 = new Age(1);

age1.areEqual(age2); // returns true
```

As we can see here, one downside of value objects being, well... objects is that in order to do something with the value it holds we always have to unwrap it, we cannot access it directely.

### The problems Value Objects

As we pointed out earlier, value objects have a set of problems:

First, no separation between type definition and creation. The Age class is both the definition of our Age Abstract Data Type and the mechanism to create an instance of an Age abstract data type.

Second, we must always create our own comparation mechanism. Since they are objects, we cannot just compare them using the double equal and tripple operator.

Third, we cannot access values directely, we must always go inside the object and unwrap the value.

These are all problems that object oriented programming creates. Unfortunately value objects are a pattern that relies heavely on OOP, and I don't like OOP... If there was another way...

## Value Types

In typescript we have multiple mechanisms to define abstract data types: classes, interfaces and type definitions. Because of that we are not constrained to the object oriented paradigm.

We can follow a different approach and rely on algebraic data types. With typescript's algebraic data types we have downside: have to provide the type definition and the instantiation mechanism separately.

When we use algebraic data types I prefer to refer to `value objects` as value types. Since this term not only takes distance from OOP, but also because it better reflects what `algebraict data types` are: `type definitions` for `values`.

### Age type definition

Since value objects are just wrappers on top of primitives we can define an `Age` as an alias for a `number`

```ts
type Age = number;
```

The type definition doesn't tell us anything about the constraints or the invariants of the `Value Object`, it just allows us to inject semantic to the model.

## Ad-hoc creation

One side effect of `type aliases` is that we can create types ad-hoc:

```ts
const age: Age = 1;
```

This at first could be seen as a benefit, but one problem with ad-hoc creationg is that we end up bypassing invariant enforcing. There's nothing preventing us from creating a negative age:

```ts
const age: Age = -1; // this is allowed
```

So, even thoug it may look tempting to use ad-hoc creationg we must avoid it at all cost and always use a creation mechanism.

## Age creation mechanism

Since we have the word `Age` used for our type definition, we cannot use it for the creation mechanism, we have to use a different name. We can use `AgeFactory`, for our creation mechanism:

```ts
function AgeFactory(value: number): Age {
  if (typeof value !== "number") {
    throw new Error("Age: value must be a number");
  }

  if (value < 0) {
    throw new Error("Age: number must be equal or greater than zero");
  }

  return value;
}
```

`AgeFactory` is just a function that takes a value of type `number` and returns a value of type `Age`.
Within the body of the function we just do the same validation process as we did with our `Age` class.

> Note: The signature of the function tell us something really important: it takes a `number` and returns an `Age` value
> This is subtle but extremely important detail. This tell us that we are mapping from a primitive to wrapper value. `AgeFactory` is essentially a mapper function.
> This process of mapping from a primitive to a wrapper value is known as `lifting` in functional programming jargon.

And to create an `Age` we just have to call the function passing a number as argument:

```ts
const age = Age(1);
```

This is not only cleaner since we don't have to write `new` everysingle time, but also we don't have to create comparator mechanism, since they are just glorified primitives:

```ts
const age1 = Age(1);
const age2 = Age(1);
const age3 = Age(2);

age2 === age1; // true
age2 === age3; // false
```

## Narrowing Value Types

One benefit of using type aliases in typescript is that we can rely on narrowing in order to define our invariants.

Let's change context a little bit, and let's suppose we are working on a shipping company. One common use case for shipping companies is to have a limited list of states that they ship to.

We can not only model a `State` value object, but we can use `type narrowing` to enforce at compile time our invariants:

```ts
type State = "FL" | "TX";

const state: State = "CA"; // this throws an error at compile time
```

This is useful when we are creating values ad-hoc, but as we discussed earlier, we should use always our user-defined creation mechanism.

One benefit of narrowing is that it will always force us to manually validate that the value we are returning has been narrowed down. If we just return the string we take as input in our `StateFactory` function we will get a compilation error.

```ts
type State = "FL" | "TX";

function StateFactory(value: string): State {
  // this throws an error at compilation time
  return state;
}

const state = SateFactory("FL");
```

This is because a string is not a subset of `State`. That forces us to use `conditional narrowing` to make sure the value we are returning is indeed one of our valid values ("FL" or "TX").

```ts
function StateFactory(value: string): State {
  if (value === "TX" || value === "FL") {
    return value;
  }

  throw new Error("State: invalid state");
}

// at compile time it throws an error
// at run time it throws an exception
const state = SateFactory("CA");
```

Indirectely narrowed types forces us to use defensive programming. If we pass an invalid state at compile time it will throw an error and at run time an exception.

### Immutablity

Enforcing immutabilty suffers a little bit here, because the only way to enforce it is by using constants.

```ts
const state = StateFactory("TX");
state = StateFactory("FL"); // this throws a compilation error
```

But if we decide to use `let` or `var`, then we are on our own:

```ts
let state = StateFactory("TX");
state = StateFactory("FL"); // lelelelelel
```

Unfortunately we cannot lean on the compiler and let it prevent us from shooting ourselves on the foot. Instead we must do the conscious effort of always treating value types as immutable, even when they are not (lol).

## Conclussion

Value Objects kinda cool, but value types are cooler.

Value Objects have a set of benefits:

- Allow us to inject semantic into our model
- Help us enforcing invariants
- Allow us create our own primitives
- Help us reducing duplication

But value objects also have a set of downsides:

- They heavely on classes
- We must come up with our own comparation mechanism
- We cannot access values directely

Value Types are an alternative that moves away from OOP, they also have the same benefits, plus they solve all the problems that value objects introduce:

- Allow us to inject semantic into our model
- Help us enforcing invariants
- Allow us create our own primitives
- Help us reducing duplication
- We don't have to rely on classes
- We don't have to come up with our own comparation mechanism
- We CAN access values directely
- We can use narrowing to enforce invariants
- We are forced to follow a defensive approach when working with narrowing

But at the same type they introduce new problems:

- We can bypass invariant enforcement by using ad-hoc creations
- We need to come up with both a type definition and a creation mechanism

Personally I prefer value types over value objects. Not only because I don't have to use OOP patterns, but because they have extra benefits and none of the downsides of value objects.
