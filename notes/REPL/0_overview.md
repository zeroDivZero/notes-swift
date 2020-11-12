# REPL

With Swift installed (such as Xcode on macOS), run following on command line:

```zsh
% swift
```

## Import System Module

Can import system modules, such as Darwin on macOS:

```repl
1> import Darwin
2> arc4random_uniform(10)
$R0: UInt32 = 4
```

## Exit

Any of following:

```repl
:exit
:quit
:q
```

## LLDB

REPL works in conjuction with LLDB debugger. `:` is REPL escape prefix to execute LLDB command. In REPL, `:help` lists LLDB commands.

Enter `:` alone to exit REPL to enter LLDB. `c` (or `continue`) to go back.
