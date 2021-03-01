# `receive(on:options:)`

Specifies scheduler on which to receive values from publisher, such as performing UI work on main run loop.

Prefer `receive(on:)` over explicit dispatch queue when performing work in subscriber. Don't use:

```swift
pub.sink {
    DispatchQueue.main.async {
        // do something
    }
}
```

Use:

```swift
pub.receive(on: DispatchQueue.main).sink {
    // do something
}
```

## `DispatchQueue.main` / `RunLoop.main`

`RunLoop.main` runs callbacks only in `.default` mode, which is not when tracking touch and mouse events.

`DispatchQueue.main` runs callbacks in all `.common` modes, which include tracking touch and mouse events.

E.g., if scrolling while async-loading image, image will not be loaded if running on `RunLoop.main`.

## vs `subscribe(on:)`

`subscribe(on:)` affects upstream messages, `receive(on:)` changes execution context of downstream messages. In following code, `subscribe(on:)` causes `jsonPublisher` to receive requests on `backgroundQueue`, `receive(on:)` causes `labelUpdater` to receive on `RunLoop.main`:

```swift
let jsonPublisher = MyJSONLoaderPublisher()
let labelUpdater = MyLabelUpdateSubscriber()

jsonPublisher
    .subscribe(on: backgroundQueue)
    .receive(on: RunLoop.main)
    .subscribe(labelUpdater)
```
