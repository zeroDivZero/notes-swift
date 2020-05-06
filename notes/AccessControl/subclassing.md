# SUBCLASSING

Can subclass class visible in access context in same module, or open class in different module. **Subclass cannot have higher access level than superclass.**

Can override class member (method, property, initializer, or subscript) visible in access context in same module, or open class member in another module. **Override can make inherited class member more accessible than superclass version.**

Subclass member can call superclass member with lower access, if call is within access context (same file as superclass for file-private member, or in same module as superclass for internal member).

```swift
public class A {
    fileprivate func someMethod() {}
}

internal class B: A {
    override internal func someMethod() {
        super.someMethod()
    }
}
```
