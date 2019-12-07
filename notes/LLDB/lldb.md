# LLDB

## Command `po` ("print object") and Command `e`

```
po myProperty
0
```

Prints content of property. Actually alias for `expression --object-description --`, argumented version of command `expression` (or just `e`):

```
e myProperty
(Int) $R4 = 0
```

`po` can do everything `e` can. `e` is meant for **evaluating expressions**, which can range from printing properties to changing their values and even defining new classes. Can change property value to force new flow without recompiling:

```
po myProperty = 1
po myProperty
1
```

If `po` entered alone, can write multiline expression, such as creating new methods and classes inside debugging session:

```
po
Enter expressions, then terminate with an empty line to evaluate:
class $BreakpointUtils {
    static var $counter = 0
}
func $increaseCounter() {
    $BreakpointUtils.$counter += 1
    print("Times I've hit this breakpoint: \($BreakpointUtils.$counter)")
}
```

(Dollar signs to indicate that these belong to lldb, not actual code.)

Can now call `$increaseCounter()` directly from lldb:

```
po $increaseCounter()
Times I've hit this breakpoint: 1
po $increaseCounter()
Times I've hit this breakpoint: 2
```

LLDB can import plugins. For example, Facebook's [Chisel](https://github.com/facebook/chisel), which has command `border` that adds bright border to `UIView` to easily locate it on screen.
