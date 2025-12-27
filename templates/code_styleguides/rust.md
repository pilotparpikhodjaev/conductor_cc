# Rust Style Guide Summary

This document summarizes key rules from the Rust API Guidelines and community best practices.

## 1. Formatting

- **`rustfmt`:** All Rust code **must** be formatted with `rustfmt`. Run `cargo fmt` before committing.
- **Line Length:** 100 characters maximum (configurable in `rustfmt.toml`).
- **Indentation:** 4 spaces per level.

## 2. Naming Conventions

| Item                           | Convention             | Example            |
| ------------------------------ | ---------------------- | ------------------ |
| Crates                         | `snake_case`           | `my_crate`         |
| Modules                        | `snake_case`           | `my_module`        |
| Types (structs, enums, traits) | `UpperCamelCase`       | `MyStruct`         |
| Functions, methods             | `snake_case`           | `do_something()`   |
| Local variables                | `snake_case`           | `my_variable`      |
| Constants                      | `SCREAMING_SNAKE_CASE` | `MAX_SIZE`         |
| Statics                        | `SCREAMING_SNAKE_CASE` | `GLOBAL_STATE`     |
| Type parameters                | Single uppercase       | `T`, `E`, `K`, `V` |
| Lifetimes                      | Short lowercase        | `'a`, `'de`        |

## 3. Error Handling

- **`Result<T, E>`:** Use for recoverable errors. Propagate with `?` operator.
- **`Option<T>`:** Use for optional values. Avoid `unwrap()` in production code.
- **`panic!`:** Reserved for unrecoverable errors and invariant violations.
- **Custom Error Types:** Implement `std::error::Error` trait. Consider `thiserror` or `anyhow` crates.

## 4. Ownership & Borrowing

- **Prefer borrowing over ownership:** Pass `&T` or `&mut T` when ownership transfer isn't needed.
- **Use `Clone` sparingly:** Only when semantically meaningful.
- **Lifetimes:** Elide when possible. Be explicit when compiler requires it.
- **Interior mutability:** Use `RefCell`, `Cell`, or `Mutex` only when necessary.

## 5. Traits & Generics

- **Derive common traits:** `#[derive(Debug, Clone, PartialEq, Eq, Hash)]` when appropriate.
- **Trait bounds:** Prefer `impl Trait` for simple cases, explicit bounds for complex.
- **Default implementations:** Provide sensible defaults in traits.
- **Sealed traits:** Use to prevent external implementation when needed.

## 6. Documentation

- **Doc comments:** Use `///` for items, `//!` for modules.
- **Examples:** Include runnable examples in doc comments.
- **`# Panics`:** Document conditions that cause panics.
- **`# Errors`:** Document error conditions for `Result`-returning functions.
- **`# Safety`:** Required for `unsafe` functions.

````rust
/// Creates a new `Widget` with the given configuration.
///
/// # Examples
///
/// ```
/// let widget = Widget::new(Config::default());
/// assert!(widget.is_valid());
/// ```
///
/// # Errors
///
/// Returns `WidgetError::InvalidConfig` if configuration is invalid.
pub fn new(config: Config) -> Result<Widget, WidgetError> {
    // ...
}
````

## 7. Testing

- **Unit tests:** Place in `#[cfg(test)]` module at bottom of file.
- **Integration tests:** Place in `tests/` directory.
- **Doc tests:** Examples in doc comments are tested automatically.
- **Test naming:** `test_<function>_<scenario>_<expected>`.

## 8. Unsafe Code

- **Minimize usage:** Only when absolutely necessary.
- **Document invariants:** Explain why the code is safe.
- **Encapsulate:** Wrap in safe abstractions.
- **Use `// SAFETY:` comments:** Explain preconditions.

## 9. Performance

- **Avoid allocations in hot paths:** Use `&str` over `String`, `&[T]` over `Vec<T>`.
- **Use iterators:** Prefer `.iter()` chains over manual loops.
- **Profile first:** Don't optimize prematurely. Use `cargo flamegraph`.

## 10. Cargo & Dependencies

- **Semantic versioning:** Follow semver strictly.
- **Minimal dependencies:** Each dependency is a maintenance burden.
- **Feature flags:** Use for optional functionality.
- **`Cargo.lock`:** Commit for binaries, not for libraries.

_Sources:_

- [Rust API Guidelines](https://rust-lang.github.io/api-guidelines/)
- [The Rust Programming Language](https://doc.rust-lang.org/book/)
