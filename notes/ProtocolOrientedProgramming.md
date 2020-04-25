# PROTOCOL-ORIENTED PROGRAMMING (POP)

Fundamentally: Generic programming + dynamic component.

Protocol exists in other languages too, Swift not first to have it, but deeply integrated in Swift. Used to implement for-in loop, to recognize literals, etc. Prevalent in std lib.

Unique to Swift: Protocol with associated type (PAT). Other languages, like Haskell, don't have it in language definition (only get through extension). Key to generic programming.

Enables _polymorphism with value semantics_. FP has immutable value semantics, but mutable value semantics more interesting.

Associated type + protocol extension + value semantics. E.g., extend collection when element is particular value type for specific API. Leads to code reuse and elegance. Removes complexity of generic programming. Protocols can be very small and narrow, but can be composed. E.g., extend type to conform to multiple protocols. Composition delivers promise of code reuse, where OOP failed due to tight coupling.

Protocol also used to model dependency - acts as interface between code and dependency - for easier swapping out for another implementation or testing purposes. Unlike other OOP languages, where everything is class, should **start with value type, but when need polymorphism, use protocol**. Using protocol unnecessarily is premature generalization.

Protocol should be discovered, not invented. When there's commonality among multiple concrete types, define protocol.

## Type Erasure

PAT cannot be used as concrete type (cannot reference directly, can only be used as generic constraint). Need wrapper that erases type.

Is program statically or dynamic polymorphic? Type erasure only needed with dynamic polymorphism. Only needed if type cannot be determined at compile time (e.g., reading heterogenous collection off disk). Push type erasure to as high level as possible; lower level, more problems (prefer type-erased collection of collections, each of same type, than flat type-erased collection).

Limited in Swift. Type-erased type removes part of interface. E.g., type-erased collection cannot be indexed by start index. Compiler cannot type check index (conforming types may not have same index types), run-time failure due to type unsafety. Solution: Identify part of API not dependent on associated type, refactor into base protocol of more limited API.

Avoid when possible. Compiler cannot generate good code for it.

Alternative to type erasure, use closure to capture protocol-conforming type and return only limited type needed. Compiler very good at optimizing closure composition.
