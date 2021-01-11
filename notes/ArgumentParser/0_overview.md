# ArgumentParser

Open-source [library](https://github.com/apple/swift-argument-parser) to parse command-line arguments.

```swift
import ArgumentParser

struct Random: ParsableCommand {
    @Argument() var highValue: Int

    func run() throws {
        print(Int.random(in: 1...highValue))
    }
}

Random.main()
```

* `@Argument` indicates property should be drawn from command-line arg.
* Calling `main()` on `ParsableCommand` type kicks off parsing and, if parsing successful, runs command-line tool.
* Lib synthesizes help and error messages that guide user toward successful usage, using info given: `highValue`'s name and type, and name of command.

## Customize Validation and Help

Recommended to document and validate inputs. Add `CommandConfiguration` and implement `validate()`:

```swift
struct Random: ParsableCommand {
    static let configuration = CommandConfiguration(
        abstract: "Chooses a random number between 1 and your input.")

    @Argument(help: "The highest value to pick.")
    var highValue: Int
    
    func validate() throws {
        guard highValue >= 1 else {
            throw ValidationError("'<high-value>' must be at least 1.")
        }
    }
    
    func run() throws {
        print(Int.random(in: 1...highValue))
    }
}
```

`CommandConfiguration` and `ArgumentHelp` can be used to customize other traits like name, discussion, version info, etc.
