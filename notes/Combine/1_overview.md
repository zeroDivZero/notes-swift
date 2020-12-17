# OVERVIEW

Framework providing declarative API for processing values over time, and values can represent async events. Uses functional reactive programming (FRP) paradigm. Heavily used by SwiftUI.

Declares **publishers** to expose values that can change over time, and **subscribers** to receive them.

Publisher conforms to `Publisher` protocol. Chainable - has **operators** to transform values received from upstream publisher and republishes them.

At end of publisher chain, `Subscriber` acts on values as it receives them. Publisher only emits values when explicitly requested by subscriber. Subscriber in control of how fast it receives events.

Several Foundation types expose functionality through publishers, including `Timer`, `NotificationCenter`, and `URLSession`. Publisher also provided for KVO-compliant property.

Can combine output of multiple publishers and coordinate interaction. E.g., subscribe to updates from text field's publisher, and use text to perform URL requests. Then use another publisher to process responses to update UI.

Adopt Combine to make code easier to read and maintain, by centralizing event-processing code and eliminating techniques like nested closures and convention-based callbacks.

## When to Use

When something needs to react to variety of inputs, such as UI.
