# `class CurrentValueSubject`

Subject that wraps single value and publishes new element when value changes.

Unlike `PassthroughSubject`, `CurrentValueSubject` maintains buffer of most recently published element.

Calling `send()` also updates current value, as if updating value directly.
