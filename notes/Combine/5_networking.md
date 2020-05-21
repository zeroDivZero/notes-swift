# COMBINE + NETWORKING

```swift
let publisher = urlSession.dataTaskPublisher(for: urlRequest)
```

Concrete publisher `URLSession.DataTaskPublisher`, with `Output` as `(data: Data, response: URLResponse)` and `Failure` as `URLError`. It wraps URL session data task for given URL request and delivers results of performing said task. Publishes data when task completes, or terminates if task fails with error.

## With `URLRequest`

```swift
let publisher = urlSession.dataTaskPublisher(for: urlRequest)
    .tryMap { response in
        guard let httpURLResponse = response.response as? HTTPURLResponse,
            httpURLResponse.statusCode == 200 else {
                throw MyError.statusCode
        }
        return response.data
    }
    .decode(type: MyResponse.self, decoder: JSONDecoder())
    .mapError { MyError.map($0) }
    .eraseToAnyPublisher()
```

## With `URL`

```swift
func download(url: String) -> AnyPublisher<UIImage, MyError> {
    guard let url = URL(string: url) else {
      return Fail(error: MyError.invalidURL) // publisher that immediately terminates with error
        .eraseToAnyPublisher()
    }

    return URLSession.shared.dataTaskPublisher(for: url)
      .tryMap { response -> Data in
        guard
          let httpURLResponse = response.response as? HTTPURLResponse,
          httpURLResponse.statusCode == 200
          else {
            throw MyError.statusCode
        }

        return response.data
      }
      .tryMap { data in
        guard let image = UIImage(data: data) else {
          throw MyError.invalidImage
        }
        return image
      }
      .mapError { MyError.map($0) }
      .eraseToAnyPublisher()
}
```
