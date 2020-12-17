# OPERATOR

Describes behavior for chaging values. Acts like subscriber and publisher. Chainable, for processing, reacting to, and transforming data.

Adopts both `Publisher` and `Subscriber`. Subscribes to a `Publisher` (*upstream*). Sends result to a `Subscriber` (*downstream*).

Can transform values or types - both `Output` and `Failure` types. Can split, duplicate, or merge streams. Compiler enforces alignments of `Input/Output` and `Failure` types at each step of operator chain.

```swift
let _ = Just(17)
    .map { "Value is \($0)" }
    .sink { print("received value: \($0)") }
```

`map` transforms value type from `Int` to `String`.

Some operators have matching operators with prefix `try`, indicating they return `<Error>` failure type -- see `map` and `tryMap`. `map` allows any combination of `Output` and `Failure` types and passes them through. `tryMap` performs transformation like `map`, but if closure throws error, `tryMap` cathes it and terminates publishing by sending `.failure(Failure)`.
