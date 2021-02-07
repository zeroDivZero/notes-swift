# ArgumentParser

Open-source [library](https://github.com/apple/swift-argument-parser) to parse command-line args.

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

```zsh
% random 20
6
```

## Subcommand

Modern command-line tools, such as Git and SPM, use subcommands to group related tools in command tree. With `ArgumentParser`, declare each subcommand as separate type.

```swift
struct Random: ParsableCommand {
    static let configuration = CommandConfiguration(
        abstract: "Randomness utilities.",
        subcommands: [Number.self, Pick.self])
}

extension Random {
    struct Number: ParsableCommand {
        static let configuration = CommandConfiguration(
            abstract: "Chooses a random number between 1 and your input.")

        @Argument(help: "The highest value to pick.")
        var highValue: Int
        
        func validate() throws {
            guard highValue >= 1 else {
                throw ValidationError("'<high-value>' must be at least 1.")
            }
        }
        
        func run() {
            print(Int.random(in: 1...highValue))
        }
    }
}

extension Random {
    struct Pick: ParsableCommand {
        static let configuration = CommandConfiguration(
            abstract: "Picks random elements from your input.")

        @Option(help: "The number of elements to choose.")
        var count: Int = 1

        @Argument(help: "The elements to choose from.")
        var elements: [String]

        func validate() throws {
            guard !elements.isEmpty else {
                throw ValidationError("Must provide at least one element.")
            }
        }

        func run() {
            let picks = elements.shuffled().prefix(count)
            print(picks.joined(separator: "\n"))
        }
    }
}
```

`@Option` indicates property should be read from args, using property name to make key-value pair.

```zsh
% random pick Sunday Monday Tuesday Wednesday Thursday Friday Saturday --count 2
Thursday
Monday
```
