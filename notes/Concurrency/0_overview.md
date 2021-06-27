# OVERVIEW

## Async vs Parallel

Swift supports writing asynchronous and parallel code in structured and memory-safe way. Async code is suspended and resumed, with 1 piece of code executing at same time; e.g., run short-term operations (updating UI) while continuing long-term operations (fetching network data, parsing files). Parallel code means multiple pieces of code run simultaneously; e.g., computer with 4-core processor can run 4 pieces of code, 1 per core, at same time.

*Concurrency* refers to combination of async and parallel code. This scheduling flexibility increases complexity. Swift provides some compile-time checks. Swift concurrency model builds on threads, but no direct interaction needed. Async func can be blocked and automatically give up current thread while another func executes on it.

## Async Func

Sync func runs to completion, throws error, or never returns. Async func or method can also pause/suspend while waiting for something. **Declaration**: Add `async` after params (like `throws`):

```swift
func listPhotos(inGallery name: String) async -> [String] {
    let result = // some async networking code
    return result
}
```

If it's both async and throwing, write `async` before `throws`.

Add `await` in front of call to mark suspension point, execution suspended until func returns (like `try` for throwing func, to mark possible program flow change). Flow is suspended only when calling another async func — suspension is never implicit or preemptive.

```swift
let photoNames = await listPhotos(inGallery: "Summer Vacation")
let sortedNames = photoNames.sorted()
let name = sortedNames[1]
let photo = await downloadPhoto(named: name)
show(photo)
```

*Yielding the thread* is waiting for async func. Behind scenes, Swift suspends execution on current thread and runs other code on that thread instead.

`await` needs to be able to suspend execution, so only certain places can call async func:

1. Code in body of async func, method, or property.
2. Code in `static main()` of struct, class, or enum marked with `@main`.
3. Code in detached child task (see **Unstructured Concurrency** below).

### `Task.sleep(_:)`

Method that does nothing but waits at least given nanosecs before it returns. Useful as placeholder or for testing.

```swift
func listPhotos(inGallery name: String) async -> [String] {
    await Task.sleep(2 * 1_000_000_000)  // 2 sec
    return ["IMG001", "IMG99", "IMG0404"]
}
```

### Completion Handler

Old way of writing concurrent code. Nested completion handlers, harder to read, unstructured (cannot use structured flow controls).

```swift
listPhotos(inGallery: "Summer Vacation") { photoNames in
    let sortedNames = photoNames.sorted()
    let name = sortedNames[1]
    downloadPhoto(named: name) { photo in
        show(photo)
    }
}
```

## Async Sequence

Previous `listPhotos()` asynchronously returns whole array at once, after all elements ready. To wait 1 element at a time, use *async sequence*:

```swift
let handle = FileHandle.standardInput
for try await line in handle.bytes.lines {
    print(line)
}
```

`for-await-in` loop potentially suspends execution at beginning of each iteration, waiting for next element to be available.

Conform to `AsyncSequence` protocol to use custom type in `for-await-in` (like `Sequence` types in `for-in`).

## Async Funcs in Parallel

While async code (called with `await`) running, caller waits for completion before executing next line of code (1 piece of code at a time). E.g., in following code, photo download is async and lets other work happen while it progresses, but each download must finish before next one starts:

```swift
let firstPhoto = await downloadPhoto(named: photoNames[0])
let secondPhoto = await downloadPhoto(named: photoNames[1])
let thirdPhoto = await downloadPhoto(named: photoNames[2])

let photos = [firstPhoto, secondPhoto, thirdPhoto]
show(photos)
```

If async operations can be run independently, can run them in parallel. Use `async let`:

```swift
async let firstPhoto = downloadPhoto(named: photoNames[0])
async let secondPhoto = downloadPhoto(named: photoNames[1])
async let thirdPhoto = downloadPhoto(named: photoNames[2])

let photos = await [firstPhoto, secondPhoto, thirdPhoto]
show(photos)
```

All downloads start without waiting, run simultaneously if sys rsc available. Execution continues until creating `photos`, which waits for downloads.

In summary:

* Call async func with `await` (suspension point) when subsequent code depends on that func's result. Sequential work.
* Call async func with `async-let` when result not needed until later. Parallel work.
* Both allow other code to run while suspended.

## Task and Task Group

*Task* is unit of work to be run asynchronously. All async code runs as part of some task. `async-let` creates child task. Create task group and add child tasks for control over priority and cancellation.

Tasks are arranged in hierarchy. Tasks in same group have same parent task, and each task can have child tasks. Explicit relationship between tasks and task groups leads to *structured concurrency*, which lets Swift handle behaviors like propagating cancellation and detect some errors at compile time.

To create task group (and spawn dynamic child tasks):

```swift
await withTaskGroup(of: Data.self) { taskGroup in
    let photoNames = await listPhotos(inGallery: "Summer Vacation")
    for name in photoNames {
        taskGroup.async { await downloadPhoto(named: name) }
    }
}
```

### Unstructured Concurrency

Unstructured task doesn't have parent task. Gives complete management flexibility, but also full correctness responsibility (Swift cannot help).

Create unstructured task on current actor with `async(priority:operation:)`, and not on current actor (*detached task*) with `asyncDetached(priority:operation:)`. Both return handle to interact with task (e.g., wait or cancel).

```swift
let newPhoto = // ... some photo data ...
let handle = async {
    return await add(newPhoto, toGalleryNamed: "Spring Adventures")
}
let result = await handle.get()
```

### Task Cancellation

Cooperative cancellation model. Each task checks if it has been canceled at appropriate points in execution, and responds to cancellation appropriately. Depending on work, that usually means one of following:

* Throwing error like `CancellationError`
* Returning `nil` or empty collection
* Returning partially completed work

To check cancellation, call `Task.checkCancellation()` (throws `CancellationError` if canceled) or check `Task.isCancelled` and handle cancellation in code. E.g., photo download task might need to delete partial downloads and close network connections.

To propagate cancellation manually: `Task.Handle.cancel()`.

## Actor

Reference type like class. Unlike class, allows only 1 task to access mutable state at a time, safe for multiple tasks to interact with same instance.

```swift
actor TemperatureLogger {
    let label: String
    var measurements: [Int]
    private(set) var max: Int

    init(label: String, measurement: Int) {
        self.label = label
        self.measurements = [measurement]
        self.max = measurement
    }
}
```

When access actor property or method, use `await` to mark potential suspension point:

```swift
let logger = TemperatureLogger(label: "Outdoors", measurement: 25)
print(await logger.max)
```

Code within actor doesn't need `await` when accessing properties:

```swift
extension TemperatureLogger {
    func update(with measurement: Int) {
        measurements.append(measurement)
        if measurement > max {
            max = measurement
        }
    }
}
```

`update()` has no suspension point, `max` updated before accessed by other code, no temporarily inconsistent state.

Accessing `max` outside actor without `await` fails because actor properties are part of that actor's isolated local state. *Actor isolation* - guarantee that only code inside actor can access actor's local state.

```swift
print(logger.max)  // compile error
```
