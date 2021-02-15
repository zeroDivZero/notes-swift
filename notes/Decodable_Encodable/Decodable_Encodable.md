# `protocol Decodable`, `protocol Encodable`, `typealias Codable`

When sending data over network or saving to disk, often need to encode/decode (or serialize) data to/from intermediate format (such as JSON). Conform custom type to `Decodable` and `Encodable` to be decoded/encoded.

## Codable

Combination of `Decodable` and `Encodable`:

```swift
typealias Codable = Decodable & Encodable
```

## Automatic Adoption

Properties must be `Codable` (std lib types like `String`, `Int`, and `Double`, Foundation types like `Date`, `Data`, and `URL`, or other custom `Codable` types):

```swift
struct Coordinate: Codable {
    var latitude: Double
    var longitude: Double
}

struct Landmark: Codable {
    var name: String
    var foundingYear: Int
    var location: Coordinate
}
```

`Landmark` now automatically gets `Codable` methods `init(from:)` (`Decodable`) and `encode(to:)` (`Encodable`), even though they aren't written.

`Array`, `Dictionary`, and `Optional` also conform to `Codable` when they contain codable types.

## Coding Keys

If properties in custom type and intermediate data type don't match, or names differ, explicitly add special nested `enum CodingKeys` (conforming to `CodingKey`) with cases serving as property list to be encoded/decoded. Omit properties that shouldn't be included (must have defaults):

```swift
struct Landmark: Codable {
    var name: String
    var foundingYear: Int
    var location: Coordinate
    var vantagePoints: [Coordinate]
    var notes: String = ""

    enum CodingKeys: String, CodingKey {
        case name = "title"
        case foundingYear = "founding_date"
        case location
        case vantagePoints
    }
}
```

## Manual Adoption

If type structure differs from encoded structure, provide custom `Decodable`/`Encodable` implementation.

Ex: `Coordinate` is expanded to include elevation inside nested `additionalInfo` container when encoded.

```swift
struct Coordinate {
    var latitude: Double
    var longitude: Double
    var elevation: Double

    enum CodingKeys: String, CodingKey {
        case latitude
        case longitude
        case additionalInfo
    }

    enum AdditionalInfoKeys: String, CodingKey {
        case elevation
    }
}

extension Coordinate: Decodable {
    init(from decoder: Decoder) throws {
        let values = try decoder.container(keyedBy: CodingKeys.self)
        latitude = try values.decode(Double.self, forKey: .latitude)
        longitude = try values.decode(Double.self, forKey: .longitude)

        let additionalInfo = try values.nestedContainer(keyedBy: AdditionalInfoKeys.self, forKey: .additionalInfo)
        elevation = try additionalInfo.decode(Double.self, forKey: .elevation)
    }
}

extension Coordinate: Encodable {
    func encode(to encoder: Encoder) throws {
        var container = encoder.container(keyedBy: CodingKeys.self)
        try container.encode(latitude, forKey: .latitude)
        try container.encode(longitude, forKey: .longitude)

        var additionalInfo = container.nestedContainer(keyedBy: AdditionalInfoKeys.self, forKey: .additionalInfo)
        try additionalInfo.encode(elevation, forKey: .elevation)
    }
}
```

## Example Concrete Decoders

`PropertyListDecoder`, `JSONDecoder` (neither directly conforms to `Decoder` but underlying implementations do).

```swift
let decoder = JSONDecoder()
do {
    let items = try decoder.decode([Item].self, from: data)
} catch {
    print(error)
}
```

## Example Concrete Encoders

`PropertyListEncoder`, `JSONEncoder` (neither directly conforms to `Encoder` but underlying implementations do). Usually encodes to `Data`.

```swift
let data = try! JSONEncoder().encode(myObject)  // myObject conforms to Encodable
guard let dictOpt = try? JSONSerialization.jsonObject(with: data, options: .allowFragments) as? [String: Any], let dict = dictOpt else {
    return
}
// can use dict from this point on
```
