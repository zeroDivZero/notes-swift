# CHILD

## Add

```swift
parent.view.addSubview(child.view) // move child vc's view to parent's
parent.addChild(child)             // add vc as child
child.didMove(toParent: parent)    // notify child that it was moved to parent
```

## Remove

```swift
child.willMove(toParent: nil)      // notify child that it's about to be moved away from parent
child.removeFromParent()           // remove child
child.view.removeFromSuperview()   // Remove child's view from parent's
```

## As Extensions

```swift
extension UIViewController {
    func add(_ child: UIViewController) {
        addChild(child)
        view.addSubview(child.view)
        child.didMove(toParent: self)
    }

    func remove() {
        guard parent != nil else {
            return
        }

        willMove(toParent: nil)
        view.removeFromSuperview()
        removeFromParent()
    }
}
```
