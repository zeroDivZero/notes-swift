# COMBINE + NETWORKING

```swift
let publisher = urlSession.dataTaskPublisher(for: urlRequest)
```

Publisher type `URLSession.DataTaskPublisher`, with `Output` as `(data: Data, response: URLResponse)` and `Failure` as `URLError`. It wraps URL session data task for given URL request and delivers results of performing said task. Publishes data when task completes, or terminates if task fails with error.

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
