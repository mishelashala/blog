# Thoughts on Continuation Passing Style

Continuation Passing-Style (CPS from now on) is a style of programming that makes enfasis on the control flow thru the use of continuations.

CPS was first mentioned on a paper about Scheme (a lisp-based languages) so it makes a lot of enfasis in functional programming concepts and techniques.

## Flow of control:

Functions return the control of execution when they reach the end of their bodies (life), but they can force the return of the control by using the keyword `return` or throwing exceptions.

As we said erlier: in CPS instead of using those mechanisms we use continuations.

A continuation is just an abstraction, and as such it can be so, technically, we can use anything we want to represent a continuation.

The general rule is to use functions to represent continuations.

## Return

```js
function divide(a, b) {
  return a / b
}
```

In this case the function twice produces a value: the result of dividing a by b.

A we use the `return` keyword to control the flow of control.

When we reacth that part of the function it automatically forces the return of the control flow to the procedure/function that invoked it in the first place.

So let's return as a mechanism to control the flow of execution.

As we said in the beginning: this is the job of the continuation, so let's replace the return statement with a continuation.

```js
function divide(a, b, continuation) {
  // The continuation must be a function
  continuation(a / b)
}
```

And because the return statement was also used to return a value to the caller of the function we must pass it as an argument to the continuation.

```js
divide(2 / 2, (result) => {
  console.log('result:', result)
}) // $> 'result: 1'
```

Now, continuation is an "abstraction" so, as long as it behaves like we defined it erlier it doesn't matter how it is named.

So let's rename continuation to done on the divide function.

function divide(a, b, done) {
  // some much better!
  done(a / b)
}

## Sequences of executions

Control flow is not only about early/explicit return but also about order of execution.

```js
// this is not permited
divide(1, 2)
divide(2, 2)
divide(3, 2)
```

The code above is not permitted because the control flow is not controlled by continuations.

So in order to have sequential order of execution we must rely on the continuations to do so.

```js
// continuations must control the flow of execution
divide(1, 2, (resultA) => {
  console.log("step:", resultA)
  divide(2, 2, (resultB) => {
    console.log("step: ", resultB)
    divide(3, 2, (resultC) => {
      console.log("step: ", resultC)
      console.log('done!')
    })
  })
})
```

When we call a divide it produces a value and only when it's done (or ready) it executes the continuation with the produced value (result).

The continuation calls again divide with other values and when divide has produced the value it passes it to the next continuation and so on and so forth.

So, with a little more syntax we can replicate the same behavior.

The downside of this is what many call the pyramid of doom: too many nested continuations.

Code like this is harder to read and harder to mantain.

So we must find a mechanism to try to mitigate it.

## Mitigating the pyramid of doom

Let's draw a few concepts from functional programming here

Instead of visualizing the multiple divide function calls as separate "things" let's visualize them as a sequence of operations.

And as any sequence we can represent them as a list of elements.

Let's start by creating a function called sequence that takes a list operations to perform.

```js
function sequence(operations, step, done) {
  operations.forEach(operation => operation(step))
  done()
}
```

The sequence function just calls a step function (or iteratee) that is passed as continuation to the current operation on the sequence, and when it's done it calls the done continuation.

> Note: The function sequence is not totally CPS complaint, but let's ignore that for the moment and move on.

```js
sequence(
  [
    (done) => divide(1, 2, done),
    (done) => divide(2, 2, done),
    (done) => divide(3, 2, done)
  ],
  result => console.log("step: ", result),
  () => console.log('all done!')
)
```

When we call sequence we pass three arguments: the list of sequences (operations) to perform, the (step) function that is gonna be called when an individual operation decides to continue (finishes executing) the control flow to the next sequence.

Now, inside our sequence function we have a minor detail we pass a function that only forwards the done continuation to the functions to execute, this is due to the fact that javascript doesn't suppor partial application.

Lucky for us lodash has a curry function that allow us to partially apply a function.

```js
var { curry } = require('lodash/fp')

// let's currify divide
var divide = curry((a, b, done) => {
  done(a / b)
})

// let's give to the sequence function the curry treatment too
var sequence = curry((operations, step, done) => {
  operations.forEach(operation => operation(step))
  done()
})

sequence(
  [
    divide(1, 2),
    divide(2, 2),
    divide(3, 2)
  ],
  result => console.log('step: ', result),
  () => console.log('done!')
)
```

By partially apply the divide and sequence we don't need those extra functions to forward the continuation.

> Note: We're not gonna stop discussing how curry works, we take for granted that the reader is familiar with the concept of partial application.

## Branching

But what happens if we try to divide by zero? We should have a mechanism to indicate that it's not possible to do such.

In JavaScript we have throw keyword that allows us to terminate the excution of the function.

```js
var divide = curry((a, b, done) => {
  // don't try to divide by zero
  if (b === 0) {
    throw Error('Cannot divide by zero')
  }

  done(a / b)
})
```

But we arrive to the same problem: throw takes over the control flow.

And as we've said earlier this is the job of the continuations.

So we must replace throw statements too with continautions.

We must note that there's no rule for how many continuations we can have or use.

So we can branch out the control flow using two different continuations (success/failure).

So let's add a second continuation named fail that let's use to indicate that the division by 0 is not a good idea.

```js
// done and fail are both different continuations
const divide = curry((a, b, done, fail) {
  if (b === 0) {
    // no more throws
    fail(new Error('Cannot divide by zero'))
  } else {
    done(a / b)
  }
})
```

Now, instead of passing one continuation we pass two.

The order in which we pass them is important. The order can be "arbitrary": first done then fail or visceversa.

But the recomendation is too always keep a consistent order of arguments.

```js
divide(
  1,
  2,
  // if everything is ok this function is called
  result => console.log("result:", result),
  // if something goes wrong this function is called
  err => console.log('error: ', err.message)
)
```

With this new version of divide we can fork (branch) the control flow of our program (keep this in mind).

If we can branch the control flow for success/failure states then we can use a similar mechanism to branch other kinds of control flow statements, like if statements.

So, let's create an ifElse function for that.

```js
var ifElse = curry((predicate, then, otherwise) => {
  predicate ? then() : otherwise()
})
```

We pass a predicate (boolean) as an argument and use the ternary operator to determine if we call the then continuation (when the predicate is true) or the otherwise continuation (when the predicate is false).

And now let's replace our if/else block in our divide function with our ifElse function.

```js
var divide = curry((a, b, done, fail) => {
  ifElse(
    b === 0,
    () => fail(new Error('Cannot divide by zero')),
    () => done(a / b)
  )
})
```

And if we execute our divide function again we see that it keeps working.

```js
divide(
  1,
  0,
  result => console.log('result:', result),
  err => console.log('error: ', err.message)
)
```

## try/catch block

Now, if we take a deep look at our our divide operation we can see that it behaves pretty much like a try/catch block.

But this behavior is exclusive of the divide operation.

We can try to generalize it by creating a continuation to handle try/catch blocks (control flow).

```js
var tryCatch = curry((tryBlock, success, fail) => {
  tryBlock(success, fail)
})
```

`tryCatch` is just a function that takes a function called tryBlock, a success continuation and a fail continaution.

The function tryCatch only forward the success and fail continuations to the tryBlock and let it decides witch one to call.

If we go back to our divide function, now instead of passing the success/fails continuations directely to the divide function we pass it to the tryCatch function.

```js
tryCatch(
  divide(a, b),
  result => console.log('The result is:', result),
  err => console.log('Error: ', err.message)
)
```

The benefit of this tryCatch function is that it works not only with the divide operation, but it works with a sequence of operations

```js
tryCatch(
  sequence(
    [
      divide(1, 2),
      divide(2, 2),
      divide(3, 2)
    ],
    value => console.log('step:', value)
  ),
  () => console.log('done!'),
  err => console.log('err:', err.message)
)
```

## Truncating the execution of a sequence

The problem with our sequence function is that if one of the steps fails it doesn't truncate the execution of the rest.

So multiple operations can fail and when that happens we end up calling the fail continuation multiple times.

This is a desired behavior, but if we wanted to truncate the execution when some of the operations fails then we need to implement a seriesAll function.

```js
// let's start by copying and pasting our series implementation
var seriesAll = curry((operations, step, done, fail) => {
  operations.forEach(op => {
    op(step, fail)
  })
  done()
})
```

The issue with CPS functions is that they have a color: this means that once you create a function that follow CPS all the functions invoked inside must follow CPS style too, also the functions that call other CPS functions must follow CPS. It's a all in scenario.

The problem that arrives with it is that javascript native functions don't follow CPS.

So we cannot control the flow of execution and truncate it when one of the function fails.

So we must create a wrapper for forEach that follows CPS.

```js
// CPS-yfied forEach
var forEach = (list, step, done, fail) => {
  list.forEach(op => {
    op(step, fail)
  })
  done()
}
```

But, we end up in the same place. The native forEach function doesn't follow CPS, so no matter how many wrappers we put on top we don't have the desired behavior.

To fix this we must distantiate ourselves from the native forEach and implement our own forEach function that follow CPS.

As we said earlier: we can interpret the execution of a series of functions as... well, a series (list/array).

forEach is an abstraction on top of loops and loops are an abstraction on top of iteration.

So we must focus our attention in the concept of iteration.

One way to achieve that is thru the use of recursive functions.

```js
var forEach = curry((list = [], iteratee, done, fail) => {
  ifElse(
    // check if there are more operations to perform
    list.length === 0,
    done,
    () => {
      const [head, ...tail] = list;
      iteratee(head, () => {
        forEach(tail, iteratee, done, fail)
      }, fail)
    }
  )
})
```

With this implementation of a recursive forEach function that follows CPS we can start implementing the seriesAll function.

```js
const seriesAll = curry((operations, step, done, fail) => {
  forEach(operations, (op, next) => {
    op(value => {
      step(value)
      next()
    }, fail)
  }, done, fail)
})
```

Something worth noting on forEach is that it passes (jumps) the execution to the iteratee and the iteratee passes (jumps) the execution back to the next iteration of forEach.

This behavior is called mutual recursive functions.

In other words: functions that call one eachother back and forth.

This behavior can also be used to replicate goto statements.

Please don't.

```js
tryCatch(
  seriesAll(
    [
      divide(1, 2),
      divide(2, 0),
      divide(3, 2),
    ],
     value => console.log('value: ', value),
  ),
  () => console.log('done!'),
  err => console.log('err:', err.message)
)
```

And with that we truncate the execution if one of the operations fail.

Other minor change that I would do is using a throwIt function instead of using new Error.

```js
var throwIt = (msg, done, fail) => {
  fail(new Error(msg))
}
```

And because the functions called inside a CPS function must comply with CPS too we must make sure throw it takes both the done and fail continuation even tho it only uses the fail continuation.

```js
var divide = curry((a, b, done, fail) => {
  ifElse(
    b === 0,
    () => throwIt('Cannot divide by zero', done, fail),
    () => done(a / b)
  )
})
```

And with that minor change it keeps working just like before

## Why is this important?

Note how we haven't talked about async tasks nor callbacks.

This is because on CPS style we don't about async vs sync. We care about control flow. And because both sync and async control flow can be "controlled" by continuations we don't make a distintion between an async a sync function.

Node.js is a great example of this. In node.js almost every function api is an async function.

Callback is just a name for a continuation executed after by an async function.

Other thing to note is that node.js instead of making you pass two funcitons for success/failure branching it makes you use the error-first pattern. Which in essence helps you to achieve the same, but without worrying about a second function.

Before we had promises on JavaScript everything was done using CPS-ish functions.

Actually the most popular library for async operations on node.js (at the time) called `async` uses a lot of the things that we've had discussed so far.

```js
const async = require('async')

async.map(
  ['file1','file2','file3'],
  // this is our step function!
  fs.stat,
  // one function instead of two - also error first pattern!
  function(err, results) {
    console.log("done!")
  }
);
```
