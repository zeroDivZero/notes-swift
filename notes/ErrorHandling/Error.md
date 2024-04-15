# `Error`

Any conforming type can represent error in error handling system (can throw or catch).

## Custom Error

`enum` great for group of related errors. Can associate data:

```swift
enum DataError: Error {
     case missingName, missingYear
}
```

Function that can throw error must have keyword `throws`:

```swift
func processPerson(json: AnyObject) throws -> Person {
     guard let name = json["name"] as? String else {
          throw DataError.missingName
     }
     guard let year = json["year"] as? Int else {
          throw DataError.missingYear
     }
     return Person(name, year)
}
```

To notify delegate when processing begins and ends, use `defer` (executed when function exits) to avoid duplicate code at every throw:

```swift
func processSale(json: AnyObject) throws -> Sale {
     delegate?.didBeginReadingSale()
     defer {delegate?.didEndReadingSale()}

     guard let buyerJSON = json["buyer"] as? Dictionary else {
          throw ParseError.missingBuyer
     }
     let buyer = try processPerson(buyerJSON)

     guard let price = json["price"] as? Int else {
          throw ParseError.missingPrice
     }

     return Sale(buyer, price)
}
```

Traditional exception handling schemes: Anything can throw. Performance heavily biased against exceptions. Swift error handling more balanced. Not table-based unwinding. Similar in performance to explicit if statement.

## Cocoa Errors

Most common Cocoa API errors changed to work with catch syntax: `URLError`, `DecodingError`, `EncodingError`, `AVError`, etc.
