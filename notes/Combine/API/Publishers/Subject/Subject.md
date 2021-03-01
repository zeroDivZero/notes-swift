# `protocol Subject`

Publisher that exposes method for outside callers to publish values. Used to "inject" values into stream, by calling `send()`. Useful for adapting imperative code to Combine.

"Mutable publisher", can both be subscribed to and be sent values to emit. When designing API, be careful about exposing ability to send values from outside.
