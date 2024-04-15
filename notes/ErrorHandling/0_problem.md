# PROBLEM

## Suboptimal Standard Solutions

### Obj-C - propagates error automatically (`NSError`)

* Duplicate code, bug-prone.
* Default behavior to ignore errors.

### Other extreme - implicitly propagates error (exception handling)

* Easy to ignore errors.
* Hard to reason about implicit behavior, like how control flows from one place to another.

## 3 Ways Functions Fail

### 1. Trivial Errors

`Int(someString)` can fail, but returning optional is fine. Reasons of failure usually not important to user.

### 2. Logic Errors

Assertions, overflows, `NSException`, etc. Probably should not recover, may end in undefined state.

### 3. Detailed, Recoverable Errors

File not found. Network failure. User cancellation. Primary usage of `NSError`. Focus of Swift error handling.
