# Crate futures
### Abstractions for asynchronous programming.
* This crate provides a number of core abstractions for writing asynchronous code.

## Modules
Task execution.
* ```Module futures::executor```
* All asynchronous computation occurs within an executor, which is capable of spawning futures as tasks.
* This module provides several built-in executors, as well as tools for building your own.
* This module is only available when the executor feature of this library is activated, and it is activated by default.
* Run a future to completion on the current thread.
* This function will block the caller until the given future has completed.


## Macros
### Macro ```futures::join```
* Polls multiple futures simultaneously, returning a tuple of all results once complete.
* While join!(a, b) is similar to (a.await, b.await), join! polls both futures concurrently and therefore is more efficent.
* This macro is only usable inside of async functions, closures, and blocks.
* It is also gated behind the async-await feature of this library, which is activated by default.
Examples:
```
use futures::join;

let a = async { 1 };
let b = async { 2 };

assert_eq!(join!(a, b), (1, 2));
```


### Macro ```futures::try_join```
* Polls multiple futures simultaneously, resolving to a ``Result`` containing either a tuple of the successful outputs or an error.
* ```try_join!``` is similar to join!, but completes immediately if any of the futures return an error.
* This macro is only usable inside of async functions, closures, and blocks.
* It is also gated behind the async-await feature of this library, which is activated by default.
Examples.
When used on multiple futures that return Ok, try_join! will return Ok of a tuple of the values:
```
use futures::try_join;

let a = async { Ok::<i32, i32>(1) };
let b = async { Ok::<u64, i32>(2) };

assert_eq!(try_join!(a, b), Ok((1, 2)));
```
If one of the futures resolves to an error, try_join! will return that error:
```
use futures::try_join;

let a = async { Ok::<i32, i32>(1) };
let b = async { Err::<u64, i32>(2) };

assert_eq!(try_join!(a, b), Err(2));
```
