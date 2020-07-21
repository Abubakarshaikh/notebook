# Runtimes
## Crate tokio
* A runtime for writing reliable, asynchronous, and slim applications.
* Tokio is an event-driven, non-blocking I/O platform for writing asynchronous applications with the Rust programming language.
* At a high level, it provides a few major components:
* Tools for working with asynchronous tasks, including synchronization primitives and channels and timeouts, delays, and intervals.
* APIs for performing asynchronous I/O, including TCP and UDP sockets, filesystem operations, and process and signal management.
* A runtime for executing asynchronous code, including a task scheduler, an I/O driver backed by the operating system's event queue (epoll, kqueue, IOCP, etc...), and a high performance timer.

## Crate async_std
* async-std is a foundation of portable Rust software, a set of minimal and battle-tested shared abstractions for the broader Rust ecosystem.
* It offers std types, like Future and Stream, library-defined operations on language primitives, standard macros, I/O and multithreading, among many other things.
* async-std is available from crates.io. Once included, async-std can be accessed in use statements through the path async_std, as in use async_std::future.
* Module ``async_std::future`` , Asynchronous values.
* Often it's desireable to await multiple futures as if it was a single future.
* The join family of operations converts multiple futures into a single future that returns all of their outputs.
* The race family of operations converts multiple future into a single future that returns the first output.
