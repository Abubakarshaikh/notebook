# actix-web
**Actix web is a simple, pragmatic and extremely fast web framework for Rust**
### Features
* Supports HTTP/1.x and HTTP/2
* Streaming and pipelining
* Keep-alive and slow requests handling
* Client/server WebSockets support
* Transparent content compression/decompression (br, gzip, deflate)
* Powerful request routing
* Multipart streams
* Static assets
* SSL support using OpenSSL or Rustls
* Middlewares (Logger, Session, CORS, etc)
* Includes an async HTTP client
* Supports Actix actor framework
* Runs on stable Rust 1.41+

# rocket
**Web framework for nightly with a focus on ease-of-use, expressibility, and speed**
* Rocket is a web framework for Rust (nightly) with a focus on ease-of-use, expressibility, and speed.

Here's an example of a complete Rocket application:
```
#![feature(proc_macro_hygiene, decl_macro)]

#[macro_use] extern crate rocket;

#[get("/<name>/<age>")]
fn hello(name: String, age: u8) -> String {
    format!("Hello, {} year old named {}!", age, name)
}

fn main() {
    rocket::ignite().mount("/hello", routes![hello]).launch();
}
```
Visiting localhost:8000/hello/John/58, for example, will trigger the hello route resulting in the string Hello, 58 year old named John! being sent to the browser. If an <age> string was passed in that can't be parsed as a u8, the route won't get called, resulting in a 404 error.
