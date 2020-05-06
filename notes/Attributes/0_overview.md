# ATTRIBUTES OVERVIEW

2 kinds: those that apply to declarations and those that apply to types. Provides additional info about declaration or type. E.g, `discardableResult` on function declaration indicates that compiler shouldn't generate warning for unused return value.

Specified with `@` followed by attrib name and any arg:

```
@attribute name
@attribute name(attribute arguments)
```

Some declaration attributes accept args that specify more info about attribute and how it applies to particular declaration. These args are enclosed in `()`, and format depends on attrib.
