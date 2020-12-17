# PUBLISHER AND SUBSCRIBER

Combine is all about defining process of what to do with many values over time. Other than defining result (types to be returned), it also defines how failures happen.

## Publisher

Provides data when available and upon request. No subscription request, no data. Described with 2 associated types: `Output` and `Failure` (e.g., `<String, URLError>`).

Value type.

## Subscriber

Responsible for requesting and accepting data (and possible failures) from publisher. Again described with 2 associated types, `Input` and `Failure`. To connect to publisher, `Input` must match `Output`, and `Failure` must match.

Initiates request for data and controls amount of data it receives. "Driving the action": No request, no data.

Reference type.

### Special Subscribers

`sink(receiveCompletion:receiveValue:)`

`assign(to:on:)`: Immediately assigns every element it receives to property of object using keypath.

Both request unlimited number of elements from publisher.
