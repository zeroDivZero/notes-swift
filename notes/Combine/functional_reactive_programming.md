# FUNCTIONAL REACTIVE PROGRAMMING (FRP)

Data flows from one place to other automatically through subscriptions. Particularly useful when data changes over time.

E.g., push slider value to stream, or use publisher to send value to subscribers, such as label showing slider value.

FP applies to lists of elements, FRP applies to streams of elements. FP functions like `map`, `filter`, and `reduce` have analogues applicable to streams. In addition, FRP includes functions to split and merge streams. Like FP, can create custom operations to transform data streams.

In addition to driving UI updates, also useful in asynchronous programming. E.g., when making network request, get result back eventually. Instead of executing completion closure, request method would return publisher, which publishes result when request done. If result needs to be transformed, or need to chain with another request, easier to read code than nested tree of completion closures.

Combine extends FRP by embedding concept of *back-pressure*: subscriber controls how much info it gets at once to process. Leads to efficient operation, volume of processed stream data is controllable and cancellable.
