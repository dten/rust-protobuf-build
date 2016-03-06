# rust-protobuf-build
Generate Protobuf Rust sources during `cargo build`.

This crate simplifies protobuf files generation, by allowing them to be generated by cargo.
`protoc-gen-rust` does not need to be in $PATH for it to work, reducing the number of external dependencies.

On the other hand, `libprotoc-dev` is required.

## Usage
Add to your `Cargo.toml`
```toml
[package]
build = "build.rs"

[build-dependencies.protobuf-build]
git = "https://github.com/plietar/rust-protobuf-build.git"
```

And create a [cargo build script](http://doc.crates.io/build-script.html), `build.rs`:
```rust
extern crate protobuf_build;

use std::env;
use std::path::PathBuf;

fn main() {
    let root = PathBuf::from(env::var("CARGO_MANIFEST_DIR").unwrap());
    let source = root.join("proto");

    let out = PathBuf::from(env::var("OUT_DIR").unwrap());

    let mut compiler = protobuf_build::Compiler::new(&source, &out);

    compiler.compile("example.proto").unwrap();
}
```

The easiest way to include the file is using the [mod_path compiler plugin](https://crates.io/crates/mod_path).
Unfortunately, this requires Rust nightly. Once rust-lang/rust#18849 is fixed, this will be usable on stable rust.
```rust
mod_path! example (concat!(env!("OUT_DIR"), "/example.rs"));
```