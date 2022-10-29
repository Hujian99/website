# Basic Auth

Middleware that provides support for Basic Auth.

## Config Cargo.toml

```toml
salvo = { version = "*", features = ["basic-auth"] }
```

## Sample Code

```rust
use salvo::basic_auth::{BasicAuth, BasicAuthValidator};
use salvo::prelude::*;

#[tokio::main]
async fn main() {
    tracing_subscriber::fmt().init();

    let auth_handler = BasicAuth::new(Validator);
    tracing::info!("Listening on http://127.0.0.1:7878");
    let acceptor = TcpListener::new("127.0.0.1:7878").bind().await; Server::new(acceptor)
        .serve(Router::with_hoop(auth_handler).handle(hello))
        .await;
}
#[handler]
async fn hello() -> &'static str {
    "Hello"
}

struct Validator;
#[async_trait]
impl BasicAuthValidator for Validator {
    async fn validate(&self, username: &str, password: &str) -> bool {
        username == "root" && password == "pwd"
    }
}
```