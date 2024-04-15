# ERROR HANDLING

## Old Way

```swift
func preflight(inout error: Error?) -> Bool {
     if !url.checkResourceIsReachableAndReturnError(&error) {
          return false
     }
     resetState()
     return true
}
```

**Downsides:** Adds boilerplate, original code hidden. Need to know convention (`checkResourceIsReachableAndReturnError()` returns `false` when failed), compiler can't help.

**Upsides:** Readability. Error parameter and function name indicate method can fail. Explicit control flow.

## New Way

When call can fail, must add `try` and need to handle error. By default function can't throw; errors bounded unlike Java or C# (anything can raise exception). To propagate error, add `throws`. Think about control flow (if `resetState()` should be called if failed):

```swift
func preflight() throws {
     try url.checkResourceIsReachable()
     resetState()
}
```

To handle/swallow error, use `do-catch`:

```swift
func preflight() -> Bool {
     do {
          try url.checkResourceIsReachable()
          resetState()
          return true
     } catch { // shorthand for catch let error {
          return false
     }
}
```

Switch's pattern-matching capability available to catch:

```swift
func preflight() -> Bool {
     do {
          try url.checkResourceIsReachable()
          resetState()
          return true
     } catch URLError.FileDoesNotExist {
          return true
     } catch { // shorthand for catch let error {
          return false
     }
}
```

**Impossible Error:** If error happens, something very wrong, shouldn't recover:

```swift
func preflight() -> Bool {
     do {
          try url.checkResourceIsReachable()
          resetState()
          return true
     } catch {
          fatalError("I sure didn't expect this error: \(error)")
     }
}

// compact form; similar to assertion
func preflight() {
     try! url.checkResourceIsReachable()
     resetState()
}
```
