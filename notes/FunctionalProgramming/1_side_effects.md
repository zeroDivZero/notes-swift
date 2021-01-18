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
