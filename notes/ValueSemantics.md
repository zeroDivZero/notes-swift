# VALUE SEMANTICS

One main source of bugs is due to shared mutable state. Thread unsafe, leads to race condition. FP makes values immutable. Machine model is mutation: write to memory. When language deviates from machine model, compiler needs to do more work to make up; costly.

Instead of forbidding mutation, forbid sharing. Gets all benefits of FP: reason about code, less error-prone, thread safety, but works with not against machine model.

Local mutable value types allow efficient mutation, but when passed around in non-sharing manner, can no longer change and minimizes errors.

Copy-on-write makes passing value types like array efficient, underlying object in memory not copied until write. Not automatically applicable to every value type. Get behavior by composing type out of types that have copy-on-write.
