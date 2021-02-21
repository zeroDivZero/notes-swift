# SIDE EFFECTS

Function or expression has side effect if it modifies state outside its local environment. Not captured in func signature. Difficult to test, not compose well.

Given same input, func with no side effect always returns same result regardless of times run, easy to test. If it has side effect, test may pass, but side effect not tested. When composing, order of side effect operations may change.

```swift
func computeWithSideEffect(_ x: Int) -> Int {
    let computation = x * x + 1
    print("Computed \(computation)")  // side effect
    return computation
}
// `print()` may not be serious side effect, but imagine if it were network request or analytics
```

## Isolate Side Effect

Side effect can be considered implicit output in func, observable to outside world:

```swift
func computeAndPrint(_ x: Int) -> (Int, [String]) {
    let computation = x * x + 1
    return (computation, ["Computed \(computation)"])
}
// can now test results of both func and side effect
```

However, `computeAndPrint()` no longer composable with self (output not same as input).

## Effectful Composition

Can define new operator:

```swift
precedencegroup EffectfulComposition {
    associativity: left
    higherThan: ForwardApplication
    lower
}

infix operator >=>: EffectfulComposition

func >=> <A, B, C>(
    _ f: @escaping (A) -> (B, [String]),
    _ g: @escaping (B) -> (C, [String])
) -> ((A) -> (C, [String])) {
    { a in
        let (b, logs) = f(a)
        let (c, moreLogs) = g(b)
        return (c, logs + moreLogs)
    }
}

2
    |> computeAndPrint
    >=> incr
    >>> computeAndPrint
    >=> square
    >>> computeAndPrint
// define `>>>` with higher prcedence than `>=>`
```

Universal, can define same operator if `f` and `g` return optionals (can chain failable inits), or arrays, etc.

## Side Effect in Output

```swift
func greetWithEffect(_ name: String) -> String {
    let seconds = Int(Date().timeIntervalSince1970) % 60
    return "Hello \(name)! It's \(seconds) seconds past the minute."
}
```

In `computeWithSideEffect()`, side effect `print()` takes input but no output. In `greetWithEffect()`, side effect `Date()` takes no input but output different each time called. Cannot test, since output potentially different each time. Turn date into input:

```swift
func greet(at date: Date = Date(), name: String) -> String {
    let seconds = Int(date.timeIntervalSince1970) % 60
    return "Hello \(name)! It's \(seconds) seconds past the minute."
}
```

Use default param for ease-of-use, but can test deterministically:

```swift
assert(greet(at: Date(timeIntervalSince1970: 36), name: "Bob") ==
        "Hello Bob! It's 36 seconds past the minute.")
```

However, composition broken. Separate inputs to get back `(String) -> String` signature:

```swift
func greet(at date: Date) -> (String) -> String {
    { name in
        let seconds = Int(date.timeIntervalSince1970) % 60
        return "Hello \(name)! It's \(seconds) seconds past the minute."
    }
}
```

Composition restored, and still testable:

```swift
func uppercased(_ string: String) -> String {
  return string.uppercased()
}

"Bob" |> uppercased >>> greet(at: Date())
"Bob" |> greet(at: Date()) >>> uppercased

assert(greet(at: Date(timeIntervalSince1970: 36))("Bob") ==
        "Hello Bob! It's 36 seconds past the minute.")
```

In general, if side effect caused by input, isolate it in output. If side effect in output, isolate it as input.
