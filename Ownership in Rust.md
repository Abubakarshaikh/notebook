# Understanding Ownership
* Ownership is Rust’s most unique feature, and it enables Rust to make memory safety guarantees without needing a garbage collector.
* Therefore, it’s important to understand how ownership works in Rust.

## What is Ownership?
* Rust’s central feature is ownership.
* All programs have to manage the way they use a computer’s memory while running.
* Some languages have garbage collection that constantly looks for no longer used memory as the program runs.
* In other languages, the programmer must explicitly allocate and free the memory.
* Rust uses a third approach: memory is managed through a system of ownership with a set of rules that the compiler checks at compile time.
* None of the ownership features slow down your program while it’s running.

### The Stack and the Heap
* In many programming languages, you don’t have to think about the stack and the heap very often.
* But in a systems programming language like Rust, whether a value is on the stack or the heap has more of an effect on how the language behaves and why you have to make certain decisions.
* Both the stack and the heap are parts of memory that are available to your code to use at runtime, but they are structured in different ways.
* The stack stores values in the order it gets them and removes the values in the opposite order.
* This is referred to as last in, first out.
* Think of a stack of plates: when you add more plates, you put them on top of the pile, and when you need a plate, you take one off the top.
* Adding or removing plates from the middle or bottom wouldn’t work as well.
* Adding data is called pushing onto the stack, and removing data is called popping off the stack.
* All data stored on the stack must have a known, fixed size.
* Data with an unknown size at compile time or a size that might change must be stored on the heap instead.
* The heap is less organized: when you put data on the heap, you request a certain amount of space.
* The memory allocator finds an empty spot in the heap that is big enough, marks it as being in use, and returns a pointer, which is the address of that location.
* This process is called allocating on the heap and is sometimes abbreviated as just allocating.
* Pushing values onto the stack is not considered allocating.
* Because the pointer is a known, fixed size, you can store the pointer on the stack, but when you want the actual data, you must follow the pointer.
* Pushing to the stack is faster than allocating on the heap because the allocator never has to search for a place to store new data; that location is always at the top of the stack.
* Comparatively, allocating space on the heap requires more work, because the allocator must first find a big enough space to hold the data and then perform bookkeeping to prepare for the next allocation.
* Accessing data in the heap is slower than accessing data on the stack because you have to follow a pointer to get there. 

### Ownership Rules
* Each value in Rust has a variable that’s called its owner.
* There can only be one owner at a time.
* When the owner goes out of scope, the value will be dropped.

### Variable Scope
```
   {                      // s is not valid here, it’s not yet declared
        let s = "hello";   // s is valid from this point forward

        // do stuff with s
    }                      // this scope is now over, and s is no longer valid
```

### The String Type
```
let mut s = String::from("hello");

    s.push_str(", world!"); // push_str() appends a literal to a String

    println!("{}", s); // This will print `hello, world!`
```
* So, what’s the difference here? Why can String be mutated but literals cannot? The difference is how these two types deal with memory.

### Memory and Allocation
```
    let x = 5;
    let y = x;
```
```
 let s1 = String::from("hello");
    let s2 = s1;
```
```
    let s1 = String::from("hello");
    let s2 = s1;

    println!("{}, world!", s1);
```
* You’ll get an error like this because Rust prevents you from using the invalidated reference:
```
cargo run
   Compiling ownership v0.1.0 (file:///projects/ownership)
error[E0382]: borrow of moved value: `s1`
 --> src/main.rs:5:28
  |
2 |     let s1 = String::from("hello");
  |         -- move occurs because `s1` has type `std::string::String`, which does not implement the `Copy` trait
3 |     let s2 = s1;
  |              -- value moved here
4 | 
5 |     println!("{}, world!", s1);
  |                            ^^ value borrowed here after move
```

### Ways Variables and Data Interact: Clone
* If we do want to deeply copy the heap data of the String, not just the stack data, we can use a common method called clone. 
Here’s an example of the clone method in action:
```
let s1 = String::from("hello");
    let s2 = s1.clone();

    println!("s1 = {}, s2 = {}", s1, s2);
```

### Stack-Only Data: Copy
```
    let x = 5;
    let y = x;

    println!("x = {}, y = {}", x, y);
```
* This code seems to contradict what we just learned: we don’t have a call to clone, but `x` is still valid and wasn’t moved into `y`.
* The reason is that types such as integers that have a known size at compile time are stored entirely on the stack, so copies of the actual values are quick to make. 
* That means there’s no reason we would want to prevent x from being valid after we create the variable y.
* n other words, there’s no difference between deep and shallow copying here, so calling clone wouldn’t do anything different from the usual shallow copying and we can leave it out.
* Here are some of the types that are Copy:
  * All the integer types, such as u32.
  * The Boolean type, bool, with values true and false.
  * All the floating point types, such as f64.
  * The character type, char.
  * Tuples, if they only contain types that are also Copy. For example, (i32, i32) is Copy, but (i32, String) is not.
  
### ownership and Functions
```
fn main() {
    let s = String::from("hello");  // s comes into scope

    takes_ownership(s);             // s's value moves into the function...
                                    // ... and so is no longer valid here

    let x = 5;                      // x comes into scope

    makes_copy(x);                  // x would move into the function,
                                    // but i32 is Copy, so it’s okay to still
                                    // use x afterward

} // Here, x goes out of scope, then s. But because s's value was moved, nothing
  // special happens.

fn takes_ownership(some_string: String) { // some_string comes into scope
    println!("{}", some_string);
} // Here, some_string goes out of scope and `drop` is called. The backing
  // memory is freed.

fn makes_copy(some_integer: i32) { // some_integer comes into scope
    println!("{}", some_integer);
} // Here, some_integer goes out of scope. Nothing special happens.
```
* If we tried to use s after the call to takes_ownership, Rust would throw a compile-time error.
* These static checks protect us from mistakes.

### Return Values and Scope
```
fn main() {
    let s1 = gives_ownership();         // gives_ownership moves its return
                                        // value into s1

    let s2 = String::from("hello");     // s2 comes into scope

    let s3 = takes_and_gives_back(s2);  // s2 is moved into
                                        // takes_and_gives_back, which also
                                        // moves its return value into s3
} // Here, s3 goes out of scope and is dropped. s2 goes out of scope but was
  // moved, so nothing happens. s1 goes out of scope and is dropped.

fn gives_ownership() -> String {             // gives_ownership will move its
                                             // return value into the function
                                             // that calls it

    let some_string = String::from("hello"); // some_string comes into scope

    some_string                              // some_string is returned and
                                             // moves out to the calling
                                             // function
}

// takes_and_gives_back will take a String and return one
fn takes_and_gives_back(a_string: String) -> String { // a_string comes into
                                                      // scope

    a_string  // a_string is returned and moves out to the calling function
}
```

```
fn main() {
    let s1 = String::from("hello");

    let (s2, len) = calculate_length(s1);

    println!("The length of '{}' is {}.", s2, len);
}

fn calculate_length(s: String) -> (String, usize) {
    let length = s.len(); // len() returns the length of a String

    (s, length)
}
```

# References and Borrowing
# The Slice Type
