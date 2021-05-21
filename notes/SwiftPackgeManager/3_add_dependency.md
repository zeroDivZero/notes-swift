# ADD DEPENDENCY

In `Package.swift`:

```swift
dependencies: [
    .package(url: "https://github.com/apple/swift-argument-parser", from: "0.4.3"),
],
// ...
targets: [
    .target(
        name: "my-spm",
        dependencies: [
            .product(name: "ArgumentParser", package: "swift-argument-parser")
        ]),
// ...
```

Can go to swiftpm.co to find info like latest version of package.

To find dependency's product name, go to `.build/checkouts/<package name>/Package.swift`.
