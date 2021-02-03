# INHERITANCE

Like class, protocol can inherit another. Unlike class, can inherit from *multiple* parents. Example from std lib: `Hashable` inherits from `Equatable`, since, like when inserting to set, each successful hash value check is followed by equality check.

## Custom Example

```swift
protocol User {
    var id: UUID { get }
    var name: String { get }
}

protocol AuthenticatedUser: User {
    var accessToken: AccessToken { get }
}

extension AnonymousUser: User {}
extension Member: AuthenticatedUser {}
extension Admin: AuthenticatedUser {}
```

Hierarchy allows increasing specialization, avoids unnecessary optionals (and no dummy methods that return `nil` or do nothing to satisfy protocol conformation).

## Specialization

Ex: Build component UI system; component can be implemented using `UIView`, `UIViewController`, `CALayer`, etc.

```swift
protocol Component {
    associatedtype Container
    func add(to container: Container)
}
```

Most components will be `UIView`, so specialize (to minimize boilerplate):

```swift
protocol ViewComponent: Component where Container: UIView {
    associatedtype View: UIView
    var view: View { get }
}
```

Compiler enforces `ViewComponent` to have view and container as `UIView`, so can provide default implementation:

```swift
extension ViewComponent {
    func add(to container: Container) {
        container.addSubview(view)
    }
}
```
