# `reduce()`

```swift
struct RenderingPipeline {
    var operations: [(RenderingContext) -> RenderingContext]

    func render() -> Image {
        var context = RenderingContext()

        context = operations.reduce(context) { context, operation in
            operation(context)
        }

        return context.makeImage()
    }
}
```
