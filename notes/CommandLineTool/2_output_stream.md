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
