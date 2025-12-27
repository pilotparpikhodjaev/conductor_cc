# Swift Style Guide Summary

This document summarizes key rules from Apple's Swift API Design Guidelines and community best practices.

## 1. Formatting

- **Indentation:** 4 spaces (or 2 spaces per project convention). No tabs.
- **Line Length:** 120 characters maximum.
- **Braces:** Opening brace on same line, closing brace on new line.
- **SwiftFormat/SwiftLint:** Use automated tools for consistency.

## 2. Naming Conventions

| Item                  | Convention        | Example                    |
| --------------------- | ----------------- | -------------------------- |
| Types, Protocols      | `UpperCamelCase`  | `UserProfile`, `Codable`   |
| Functions, Methods    | `lowerCamelCase`  | `fetchUser()`              |
| Variables, Properties | `lowerCamelCase`  | `userName`                 |
| Constants             | `lowerCamelCase`  | `maximumRetries`           |
| Enum cases            | `lowerCamelCase`  | `.loading`, `.success`     |
| Boolean properties    | `is`/`has` prefix | `isEnabled`, `hasChildren` |

## 3. Clarity at Point of Use

- **Omit needless words:** `remove(at: index)` not `removeItem(at: index)`
- **Include necessary words:** `addSubview(_:)` not `add(_:)`
- **Name according to roles:** Parameters should describe their role, not type.
- **Compensate for weak types:** `addObserver(_: observer)` when type is `NSObject`

```swift
// Good
func insert(_ element: Element, at index: Int)
func removeAll(matching predicate: (Element) -> Bool)

// Bad
func insert(_ e: Element, position: Int)
func removeAll(filter: (Element) -> Bool)
```

## 4. Optionals

- **Prefer optional binding:** Use `if let` or `guard let`.
- **Avoid force unwrapping:** `!` should be rare in production code.
- **Use nil-coalescing:** `value ?? defaultValue` for defaults.
- **Optional chaining:** `object?.property?.method()` for safe access.

```swift
// Good
guard let user = fetchUser() else { return }
let name = user.name ?? "Anonymous"

// Bad
let user = fetchUser()!
let name = user!.name
```

## 5. Error Handling

- **Use `throws`:** For recoverable errors that should propagate.
- **Use `Result<T, Error>`:** When async completion needs error info.
- **`try?`:** When you want to ignore specific errors.
- **`try!`:** Only when failure is a programmer error.

```swift
func loadData() throws -> Data {
    guard let url = URL(string: urlString) else {
        throw DataError.invalidURL
    }
    return try Data(contentsOf: url)
}
```

## 6. Access Control

- **Default is internal:** Explicitly mark `public` for API surfaces.
- **Use `private`:** For implementation details.
- **Use `fileprivate`:** Only when truly needed across a file.
- **Mark `final`:** When subclassing isn't intended.

## 7. Protocols & Extensions

- **Protocol-oriented design:** Prefer protocols over inheritance.
- **Protocol extensions:** Provide default implementations.
- **Organize with extensions:** Group related functionality.

```swift
// Organize conformances in extensions
struct User: Identifiable {
    let id: UUID
    let name: String
}

extension User: Codable {}

extension User: CustomStringConvertible {
    var description: String { name }
}
```

## 8. Closures

- **Trailing closure syntax:** For final closure parameters.
- **Shorthand arguments:** `$0`, `$1` for simple closures.
- **Capture lists:** Prevent retain cycles with `[weak self]`.

```swift
// Good
users.filter { $0.isActive }
     .map { $0.name }

fetchData { [weak self] result in
    self?.handleResult(result)
}
```

## 9. Collections

- **Use `Array`, `Dictionary`, `Set`:** Over Foundation types.
- **Prefer `isEmpty`:** Over `count == 0`.
- **Use higher-order functions:** `map`, `filter`, `reduce`, `compactMap`.
- **Avoid index-based loops:** Use `for-in` or `forEach`.

## 10. Memory Management

- **ARC:** Understand strong, weak, and unowned references.
- **`weak` delegates:** Prevent retain cycles.
- **`unowned`:** Only when lifetime is guaranteed.
- **Value types:** Prefer structs over classes when appropriate.

## 11. Concurrency (Swift 5.5+)

- **`async`/`await`:** For asynchronous code.
- **`Task`:** For structured concurrency.
- **`actor`:** For thread-safe state.
- **`@MainActor`:** For UI updates.

```swift
func fetchUser() async throws -> User {
    let data = try await URLSession.shared.data(from: url)
    return try JSONDecoder().decode(User.self, from: data.0)
}
```

## 12. Documentation

- **Use `///` for doc comments**
- **Document parameters, returns, throws**
- **Include code examples**

```swift
/// Fetches a user by their unique identifier.
///
/// - Parameter id: The unique identifier of the user.
/// - Returns: The user if found.
/// - Throws: `UserError.notFound` if no user exists with the given ID.
func fetchUser(id: UUID) throws -> User
```

_Sources:_

- [Swift API Design Guidelines](https://swift.org/documentation/api-design-guidelines/)
- [The Swift Programming Language](https://docs.swift.org/swift-book/)
