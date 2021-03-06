# OUTPUT STREAM

Unix-based systems like macOS define two output streams:

1. Standard output stream (`stdout`): Normally attached to display and should be used to display message.
2. Standard error stream (`stderr`): Should be used to display error message. Normally attached to display but can be redirected to file.

When launching command-line program from Xcode or Terminal, by default `stdout` and `stderr` messages written to console. Common practice to redirect `stderr` to file - hides info not immediately important to user but can be inspected later to debug.

```swift
// use C function `fputs` to write string to global var `stderr`
fputs("\(error)\n", stderr)
```

Common practice to print usage statement when user uses command-line program with incorrect args.

## Terminal Text Color

Sequence `\u{001B}[0;31m` are control characters for Terminal to change following text color to red. Use sequence `\u{001B}[;m` to reset text color.

```swift
func writeMessage(_ message: String, to: OutputType = .standard) {
    switch to {
    case .standard:
        print("\u{001B}[;m\(message)")
    case .error:
        fputs("\u{001B}[0;31m\(message)\n", stderr)
    }
}
```

Does not work if running app in Xcode.
