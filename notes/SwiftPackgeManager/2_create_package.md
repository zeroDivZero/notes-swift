# CREATE PACKAGE

Create directory for package and enter it.

```zsh
mkdir Hello
cd Hello
```

## `init`

Creates default directory structure and files, including required manifest `Package.swift` at root.

```zsh
swift package init
```

By default creates library package. To create executable:

```zsh
swift package init --type executable
```

## `build`

Downloads, resolves, and compiles dependencies.

```zsh
swift build
```

## `test`

Run tests on package.

```zsh
swift test
```

## Executable

Target is considered executable if it contains `main.swift`. To build and run it as binary executable:

```zsh
swift run
```

Typically need executable name after `run`, but if there is only 1 in this package can be omitted.

Can find binary in `.build` folder (can just run `swift build` to get this file).

## Xcode Project

To create Xcode project for easier development in Xcode:

```zsh
swift package generate-xcodeproj
```
