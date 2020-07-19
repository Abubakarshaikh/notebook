### If closures can be called like regular functions, why don’t we just use regular functions?
For one, we can pass closures as parameters and return closures to and from functions.

Let’s try to receive (and evaluate) our closure:
```
fn receives_closure(closure: ?) -> ?
```

But what should our closure type and function return type be? Recall that closures are synthesized structs whose type cannot be written out. We can see this by changing the lIne let add = |x, y| ... to let () = |x, y| ... and then trying to compile our program:
```
error[E0308]: mismatched types
 --> src/main.rs:2:9
  |
2 |     let () = |x, y| x + y;
  |         ^^   ------------ the expected closure
  |         |
  |         expected closure, found `()`
  |
  = note: expected closure `[closure@src/main.rs:2:14: 2:26]`
           found unit type `()`
```
To be able to pass around closures, we need to use one of the ``FnOnce``, ``FnMut``, or ``Fn`` traits

Additionally, we can’t just use, for example,Fn directly as a parameter type, as follows:
```
fn receives_closure(closure: Fn(i32) -> i32) {}
```
If we try that, we’ll get.
```
error[E0277]: the size for values of type `(dyn std::ops::Fn(i32) -> i32 + 'static)` cannot be known at compilation time
 --> src/main.rs:1:21
 ```
 Now if you’ve done any Rust before you might be familiar with this error, and might be tempted to just Box<dyn ... that trait:
 ```
 fn receives_closure(closure: Box<dyn Fn(i32) -> i32>) {}
 ```
 But if you try to call it now with our closure earlier:
 ```
 let add = |x, y| x + y;
receives_closure(add);
```
We promptly get:
```
error[E0308]: mismatched types
 --> src/main.rs:5:22
  |
4 |     let add = |x, y| x + y;
  |               ------------ the found closure
5 |     receives_closure(add);
  |                      ^^^ expected struct `std::boxed::Box`, found closure
  |
  = note: expected struct `std::boxed::Box<(dyn std::ops::Fn(i32) -> i32 + 'static)>`
            found closure `[closure@src/main.rs:4:15: 4:27]`
```

After much trial and error on my part, and without going in-depth as to the explanation, the elegant solution is to use generics so the Rust compiler is free to perform type inference at compile time:
```
fn receives_closure<F>(closure: F)
where
    F: Fn(i32, i32) -> i32,
{
    let result = closure(1, 2);
    println!("closure(1, 2) => {}", result);
}fn main() {
    let add = |x, y| x + y;
    receives_closure(add);
}
```
Running this outputs 3 again, as expected.

### But Rust also has function pointers, so again, why do we need closures?
Let’s rewrite receives_closure() to take a closure that only accepts one parameter:
```
fn receives_closure<F>(closure: F)
where
    F: Fn(i32) -> i32,
{
    let result = closure(1);
    println!("closure(1) => {}", result);
}fn main() {
    let add = |x| x + 2;
    receives_closure(add);
}
```
So far so good. But now let’s move out 2 from the closure into the surrounding scope (our main() function):
```
let y = 2;
let add = |x| x + y;
```
This still works! We say that the variable y has been ‘captured’ by the add closure. Even though receives_closure has no knowledge of y it is able to evaluate the closure correctly.

This would only be possible using fn pointers by us creating structs (to capture the surrounding scope’s ‘state’) and passing those around — but this is exactly what the Rust compiler does for us when we use closures!

To demonstrate this, let’s create nested blocks with different values. Also, this time, we won’t even declare our closure variables, but just pass them in directly:
```
    {
        let y = 2;
        receives_closure(|x| x + y);
    }
    {
        let y = 3;
        receives_closure(|x| x + y);
    }
```
The output, as expected, is 3 and 4.


### Returning Closures
Not only can we receive closures, we can return them from functions as well.
```
fn returns_closure() -> impl Fn(i32) -> i32 {
    |x| x + 4
}
```
We can call it directly:
```
let closure = returns_closure();
println!("closure(1) => {}", closure(1));
```
Or since it returns the same type that receives_closure() expects, we can just pass it to that as well!
```
let closure = returns_closure();
receives_closure(closure);
```
Either way prints 5 as expected.


### Rice and Curry
Armed with the above, let us now try to implement a function that will curry a closure.

That is, we want a function that will take a closure that accepts two parameters of type T, and a value also of type T, and returns a closure that only accepts a single T.

Symbolically, when given f(x, y), we want curry(f, a) to return a g(y) where g(y) => f(a, y).

First, our function signature:
```
fn curry<F>(f: F, x: i32) -> impl Fn(i32) -> i32
where
    F: Fn(i32, i32) -> i32,
```
Now, if we try to write our function as:
```
{
    |y| f(x, y)
}
```
We will promptly run into several errors. However, at least in this case the Rust compiler helpfully points us to the solution:
```
error[E0373]: closure may outlive the current function, but it borrows `f`, which is owned by the current function
  --> src/main.rs:17:5
   |
17 |     |y| f(x, y)
   |     ^^^ - `f` is borrowed here
   |     |
   |     may outlive borrowed value `f`
   |
note: closure is returned here
  --> src/main.rs:13:30
   |
13 | fn curry<F>(f: F, x: i32) -> impl Fn(i32) -> i32
   |                              ^^^^^^^^^^^^^^^^^^^
help: to force the closure to take ownership of `f` (and any other referenced variables), use the `move` keyword
```
That is, we need to move our captured variables into the closure we’re returning. The complete function thus reads
```
fn curry<F>(f: F, x: i32) -> impl Fn(i32) -> i32
where
    F: Fn(i32, i32) -> i32,
{
    move |y| f(x, y)
}
```
And used as such:
```
let add = |x, y| x + y;
let closure = curry(add, 5);
println!("closure(1) => {}", closure(1));
```
Produces 6 as expected.

### Generic Curry
Let’s take it step further now and try to turn curry fully generic! If we simply try to add the generic type arguments to the function:
```
fn generic_curry<F, X, Y, Z>(f: F, x: X) -> impl Fn(Y) -> Z
where
    F: Fn(X, Y) -> Z,
```
We run into a new error:
```
error[E0507]: cannot move out of `x`, a captured variable in an `Fn` closure
  --> src/main.rs:24:16
   |
20 | fn generic_curry<F, X, Y, Z>(f: F, x: X) -> impl Fn(Y) -> Z
   |                                    - captured outer variable
...
24 |     move |y| f(x, y)
   |                ^ move occurs because `x` has type `X`, which does not implement the `Copy` trait
```
Fortunately, the solution this time is pointed out as well. We need to tell the compiler that the X type argument needs to implement the Copy trait by using type bounds:
```
where
    F: Fn(X, Y) -> Z,
    X: Copy
```
Now the following works and produces 7 as expected.
```
let two = 2;
let add = |x, y| x + y + two;
let closure = generic_curry(add, 4);
receives_closure(closure);
```
Furthermore, to demonstrate that our generic_curry function is indeed generic, let’s try it with a closure that concatenates two strings:
```
let concat = |s, t: &str| format!("{}{}", s, t);
let closure = generic_curry(concat, "Hello, ");
let result = closure("world!");
println!("{}", result);
```
That outputs Hello, world! as expected.
