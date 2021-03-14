# KEY PATH

To reference instance property as separate value. Can be passed around, used in expressions, and enables readwrite property without knowing exact instance.

Allows dynamic property access without sacrificing type safety. Makes code more declarative and often in simpler syntax.

3 main variants:

1. `KeyPath`: Read-only of property.

2. `WritableKeyPath`: Readwrite of mutable property with value semantics (instance must be mutable).

3. `ReferenceWritableKeyPath`: Readwrite of property of instance with reference semantics.

There are other types to reduce code duplication and for type erasure.

## Functional Shorthand

```swift
struct Movie {
    let id: UUID
    let title: String
    let year: Int
    let isLiked: Bool
    // ... and other properties
}
```

`let movie: Movie`, to use key path to access property:

```swift
let movieID = movie[keyPath: \.id]
let worldwideRating = movie[keyPath: \.ratings.worldwide]  // nested
```

`let movies: [Movies]`, to transform, can use key path instead of closure:

```swift
let movieTitles = movies.map(\.title)
let moviesLiked = movies.filter(\.isLiked)
```

Can extend other functions to support key path:

```swift
extension Sequence {
    func sorted<T: Comparable>(by keyPath: KeyPath<Element, T>) -> [Element] {
        sorted { lhs, rhs in
            lhs[keyPath: keyPath] < rhs[keyPath: keyPath]
        }
    }
}

let moviesSortedByYear = movies.sorted(by: \.year)
let moviesSortedByWorldwideRating = movies.sorted(by: \.ratings.worldwide)
```

## No Instance Required

Syntax sugar is nice, but power of key path is to access property without instance.

E.g., typical to have configurator to configure table cell with model. To generalize:

```swift
struct CellConfigurator<Model> {
    let titleKeyPath: KeyPath<Model, String>
    let subtitleKeyPath: KeyPath<Model, String>
    let imageKeyPath: KeyPath<Model, UIImage?>

    func configure(_ cell: UITableViewCell, for model: Model) {
        cell.textLabel?.text = model[keyPath: titleKeyPath]
        cell.detailTextLabel?.text = model[keyPath: subtitleKeyPath]
        cell.imageView?.image = model[keyPath: imageKeyPath]
    }
}
```

Can create specialized configurators:

```swift
let movieCellConfigurator = CellConfigurator<Movie>(
    titleKeyPath: \.title,
    subtitleKeyPath: \.director.name,
    imageKeyPath: \.posterImage
)

let playlistCellConfigurator = CellConfigurator<Playlist>(
    titleKeyPath: \.title,
    subtitleKeyPath: \.authorName,
    imageKeyPath: \.artwork
)
```

## Convert to Function

Key path can be used to dynamically write to property. E.g., in list view, typical to load items, then assign to local property and render:

```swift
class ListViewController {
    private var items = [Item]() { didSet { render() } }

    func loadItems() {
        loader.load { [weak self] items in
            self?.items = items
        }
    }
}
```

Create setter function that converts writable key path into closure that sets property, and pass it as completion handler to `load()`:

```swift
func setter<Object: AnyObject, Value>(
    for object: Object,
    keyPath: ReferenceWritableKeyPath<Object, Value>
) -> (Value) -> Void {
    { [weak object] value in
        object?[keyPath: keyPath] = value
    }
}

class ListViewController {
    private var items = [Item]() { didSet { render() } }

    func loadItems() {
        loader.load(then: setter(for: self, keyPath: \.items))
    }
}
```

Simpler syntax (more declarative), and removes need for `weak self`. These functions allow more composability.
