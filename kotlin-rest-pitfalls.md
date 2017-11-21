# Pitfalls Kotin / Jackson / REST / primitives

Technologies:
- Kotlin
- Spring Boot
- Jackson

Given a Kotlin data class definition like this:

```kotlin
data class SomeRequestBody (
  @get:NotNull
  val foo: Float
)
```

When using it to de-serialize a JSON like this: `{ "foo": 1.0 }`. 
Then the instance's `foo` value would be `1.0f` - as expected.

When de-serializing a JSON like this: `{}`, we would expect the
validation annotation `@get:NotNull` to trigger an exception.
But this is not the case. Instead we get an instance with a `foo`
value of `0.0f`. Why is that?

Well, for this to make sense you need to know that Kotlin's 
`Float` class maps to Java's `float` primitive type and not
to its object type `Float`. When the JSON is de-serialized, 
the value is never `null`. Instead the primitive `float` 
default of `0.0f` is used. This is why no validation exception
is thrown.

Paradoxically you would have to use the nullable `Float?` 
type in order to actually guarantee that no consumer request
body is missing the required `foo` property:

```kotlin
data class SomeRequestBody (
  @get:NotNull
  val foo: Float?
)
```
