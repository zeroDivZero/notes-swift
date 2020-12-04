# Functions and Composition

Free functions:

```swift
func incr(_ x: Int) -> Int { x + 1 }
func square(_ x: Int) -> Int { x * x }

square(incr(2)) // composition
```

Can be hard to read if nesting (composing) many functions. Can use methods:

```swift
extension Int {
    func incr() -> Int { self + 1 }
    func square() -> Int { self * self }
}

2.incr().square()
```

Or define custom operator:

```swift
precedencegroup ForwardApplication {
    associativity: left
}
infix operator |>: ForwardApplication
func |> <A, B>(a: A, f: (A) -> B) -> B { f(a) }

2 |> incr |> square //9
```

Minor downside: Loses autocomplete.

## Rules of Thumb to Define Operator

1. Is this a new operator not currently in the language? Overloading operator can be confusing.
2. Does this operator have prior art? Should define operators existing in other FP languages.
3. Does it solve universal or domain-specific problem? Aim to solve universal problem.

## Composition Operator

Can define operator that takes 2 funcs and returns a func that is composition of them:

```swift
precedencegroup ForwardComposition {
    associativity: left
    higherThan: ForwardApplication
}
infix operator >>>: ForwardComposition
func >>> <A, B, C>(f: @escaping (A) -> B, g: @escaping (B) -> C) -> ((A) -> C) {
    { a in
        g(f(a))
    }
}

(incr >>> square)(2)  // 9
(square >>> incr)(2)  // 5
2 |> incr >>> square  // 9; no parens because >>> has higher precendence
```

More reusable. `incr >>> square` still compiles and makes sense, but in method world `incr().square()` does not compile.

With composition, many functions at disposal:

```swift
2 |> incr >>> square >>> String.init  // "9"
```

Less readable in method world:

```swift
String(2.incr().square())
```

## Point-Free Style

Or _tacit programming_, programming paradigm in which function definitions do not identify args (or "points") on which they operate.

With points:

```swift
[1, 2, 3]
    .map { ($0 + 1) * ($0 + 1) }
```

Using free functions, without points:

```swift
[1, 2, 3]
    .map(incr)
    .map(square)

// or, optimized:
[1, 2, 3]
    .map(incr >>> square)
```
