# A hitchhiker's guide to `wasm-bindgen`

Navigating a fairly large codebase is not an easy thing. To help new contributors understand the architecture of `wasm-bindgen`, this article traces a common workflow and introduces what `wasm-bindgen` does during each stage.

## Greetings, world

Here's a very simple rust snippet:

```Rust
#[wasm_bindgen]
pub struct MyStruct {
    pub name: String
}

#[wasm_bindgen]
impl MyStruct {
    pub fn new(n: name) -> String {
        MyStruct {
            name
        }
    }

    pub fn greet(&self) -> String {
        format!("greetings from {}", name)
    }
}
```

We are going to generate a `struct` with two `methods`. The `struct` doesn't do much, but it's ok. We don't care. We are going to see how `wasm-bindgen` transform that code. The transformation is our focus.

## The big picture

`wasm-bindgen` facilitates Rust-JS interop by exporting rust types to javascript or vice versa. We can depict this process as follows:

```
      1         2                3
Rust ---> Rust ---> WebAssembly ---> JavaScript / TypeScript
```

### Rust -> Rust: through proc macros

User's rust code needs further preprocessing because:

- Compiling to wasm lose information. Some of it is useful when generating js bindings.

- We need auxiliary functions for situations like copy strings.

The macro `#[wasm_bindgen]` is defined in `macro` crate. The `macro-support` crate is invoked by `macro` to do the real work.


