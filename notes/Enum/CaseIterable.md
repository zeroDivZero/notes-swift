# `CaseIterable`

Protocol that, when conformed to by type (typically by enum without associated value), automatically implements `allCases` property, which is collection of all of type's values (in order declared).

```swift
enum TextType: CaseIterable {
    case title
    case subtitle
    case sectionTitle
    case body
    case comment
}
```

If enum has associated value, define `allCases` explicitly:

```swift
enum Punctuality: CaseIterable {
    case onTime
    case delay(Double)

    static var allCases: [Punctuality] = [.onTime, .delay(10.0)]
}
```

Usage:

```swift
var fonts = [TextType: UIFont]()

for type in TextType.allCases {
    switch type {
    case .title:
        fonts[type] = .preferredFont(forTextStyle: .headline)
    case .subtitle:
        fonts[type] = .preferredFont(forTextStyle: .subheadline)
    case .sectionTitle:
        fonts[type] = .preferredFont(forTextStyle: .title2)
    case .body:
        fonts[type] = .preferredFont(forTextStyle: .body)
    case .comment:
        fonts[type] = .preferredFont(forTextStyle: .footnote)
    }
}
```

If mapping enum cases to values, convenient way to have compiler check all cases defined.

However, above example has repetitive code (`fonts[type] = ...`), and call site still gets optional:

```swift
let titleFont = fonts[.title] // type UIFont?
```

## Enum Map

Wrapper around dictionary to map cases to values:

```swift
struct EnumMap<Enum: CaseIterable & Hashable, Value> {
    private let values: [Enum: Value]

    init(resolver: (Enum) -> Value) {
        var values = [Enum : Value]()

        for key in Enum.allCases {
            values[key] = resolver(key)
        }

        self.values = values
    }

    subscript(key: Enum) -> Value {
        // safe to force-unwrap, since values is private; can add tests to make sure
        return values[key]!
    }
}
```

Can now make `fonts` const and pass resolver closure to `EnumMap`'s initializer:

```swift
let fonts = EnumMap<TextType, UIFont> { type in
    switch type {
    case .title:
        return .preferredFont(forTextStyle: .headline)
    case .subtitle:
        return .preferredFont(forTextStyle: .subheadline)
    case .sectionTitle:
        return .preferredFont(forTextStyle: .title2)
    case .body:
        return .preferredFont(forTextStyle: .body)
    case .comment:
        return .preferredFont(forTextStyle: .footnote)
    }
}
```

Lookup uses same subscript syntax, but result is non-optional:

```swift
let titleFont = fonts[.title] // type UIFont
```

### Applicable Example

Common to define enum for all sections of table view:

```swift
extension ProductListViewController {
    enum Section: String, CaseIterable {
        case featured
        case onSale
        case categories
        case saved
    }
}
```

Easy to forget to register cell classes. Use `allCases` and similar resolver pattern to enforce compiler check.

```swift
extension ProductListViewController {
    func registerCellClasses() {
        let resolver: (Section) -> UITableViewCell.Type = { section in
            switch section {
            case .featured:
                return FeaturedProductCell.self
            case .onSale:
                return ProductCell.self
            case .categories:
                return CategoryCell.self
            case .saved:
                return BookmarkCell.self
            }
        }

        for section in Section.allCases {
            tableView.register(resolver(section),
                               forCellReuseIdentifier: section.rawValue)
        }
    }
}
```

To generalize:

```swift
extension UITableView {
    func registerCellClasses<T: CaseIterable & RawRepresentable>(
        for sectionType: T.Type,
        using resolver: (T) -> UITableViewCell.Type
    ) where T.RawValue == String {
        for section in sectionType.allCases {
            register(resolver(section), forCellReuseIdentifier: section.rawValue)
        }
    }
}
```
