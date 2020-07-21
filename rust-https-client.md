# hyper
**A fast and correct HTTP implementation for Rust.**
* HTTP/1 and HTTP/2
* Asynchronous design
* Leading in performance
* Tested and correct
* Extensive production use
* Extensive production use
* Client and Server APIs

To start with, we’ll just get a simple GET request to a webpage working, so we can see all the moving parts. First, we need our dependencies. Let’s tell Cargo about our dependencies by having this in the Cargo.toml.
Dependencies:
```
[dependencies]
hyper = "0.13"
tokio = { version = "0.2", features = ["full"] }
```
Now, we are ready to import the dependencies in our Rust file:
```
extern crate hyper;
```
We need to import pieces to use from our dependencies::
```
use hyper::Client;
```
### Runtime
Now, we’ll make a request in the ``main`` of our program. This may seem like a bit of work just to make a simple request, and you’d be correct, but the point here is just to show all the setup required. Once you have this, you are set to make thousands of client requests efficientl

We have to setup some sort of runtime. By default, hyper can make use of the Tokio runtime.:
```
#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error + Send + Sync>> {
    // This is where we will setup our HTTP client requests.

    Ok(())
}
```
### GET
We can now create a hyper Client that will be registered to our runtime.

Calling client.get returns a Future that will eventually be fulfilled with a Response.:
```
// Still inside `async fn main`...
let client = Client::new();

// Parse an `http::Uri`...
let uri = "http://httpbin.org/ip".parse()?;

// Await the response...
let resp = client.get(uri).await?;

println!("Response: {}", resp.status());
```
### Response bodies
Bodies in hyper are always streamed asynchronously. But it’s easy to await for each chunk as it comes in! We’ll make use of the HttpBody trait.

We’ll also simply write the body to stdout. So, some new imports:
```
use hyper::body::HttpBody as _;
use tokio::io::{stdout, AsyncWriteExt as _};
```
```
// Previously...
let client = Client::new();
let uri = "http://httpbin.org/ip".parse()?;
let mut resp = client.get(uri).await?;
println!("Response: {}", resp.status());

// And now...
while let Some(chunk) = resp.body_mut().data().await {
    stdout().write_all(&chunk?).await?;
}
```


# reqwest 
**higher level HTTP client library -- An ergonomic, batteries-included HTTP Client for Rust.**
* Plain bodies, JSON, urlencoded, multipart
* Customizable redirect policy
* HTTP Proxies
* HTTPS via system-native TLS (or optionally, rustls)
* Cookie Store
* WASM

**Example:**
This asynchronous example uses Tokio and enables some optional features, so your ```Cargo.toml``` could look like this:
```
[dependencies]
reqwest = { version = "0.10", features = ["json"] }
tokio = { version = "0.2", features = ["full"] }
```
And then the code:
```
use std::collections::HashMap;

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let resp = reqwest::get("https://httpbin.org/ip")
        .await?
        .json::<HashMap<String, String>>()
        .await?;
    println!("{:#?}", resp);
    Ok(())
}
```
