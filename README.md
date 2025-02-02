# Scala DOM Test Utils
![Maven Central](https://img.shields.io/maven-central/v/com.raquo/domtestutils_sjs1_3.svg)


_Scala DOM Test Utils_ provides a convenient, type-safe way to assert that a real Javascript DOM node matches a certain description using an extensible DSL.

    "com.raquo" %%% "domtestutils" % "<version>"  // Scala.js 1.7.1+ only

The types of DOM tags, attributes, properties and styles are provided by [Scala DOM Types](https://github.com/raquo/scala-dom-types), but you don't need to be using that library in your application code, _Scala DOM TestUtils_ can test any DOM node no matter how it was created. 

You can use _Scala DOM Test Utils_ either directly to make assertions, or you if you're writing a DOM construction / manipulation library, to power its own test utils package. 



## Example Test

```scala

// Create a JS DOM node that you want to test (example shows optional Scala DOM Builder syntax)
val jsDomNode: org.scalajs.dom.Node = div(
  rel := "yolo"
  span("Hello, "),
  p("bizzare ", a(href := "http://y2017.com", "2017"), span(" world")),
  hr
).ref 
 
// Mount the DOM node for testing
mount(jsDomNode, "optional clue to show on failure")
 
// Assert that the mounted node matches the provided description (this test will pass given the input above)
expectNode(
  div.of(
    rel is "yolo", // Ensure that rel attribute is "yolo". Note: assertions for properties and styles work similarly 
    span.of("Hello, "), // Ensure the this element contains just one text node: "Hello, "
    p.of(
      "bizzare ",
      a.of(
        href is "http://y2017.com",
        title.isEmpty, // Ensure that title attribute is not set
        "2017"
      ),
      span.of(" world")
    ),
    hr // Just check existence of element and tag name. Equivalent to `hr like ()` 
  )
)
```

The above example defines `val jsDomNode` using [Scala DOM Builder](https://github.com/raquo/scala-dom-builder) which is a separate project, completely optional. It is a minimal, unopinionated library for building and manipulating Javascript DOM trees. You are free to create the DOM nodes that you want to test in any other way.

See more usage examples in [Laminar tests](https://github.com/raquo/Laminar/tree/master/src/test/scala/com/raquo/laminar)



## Usage

Canonical usage is to `mount` one DOM node / tree (e.g. the output of your component) and then test it using the `expectNode` method.

Alternative is to call `expectNode(actualNode, expectedNode)`, for example if you only want to test a subtree of what you mounted.

If the mechanics of `MountOps` do not work for you, you can bypass `MountOps` altogether and just call `ExpectedNode.checkNode(actualNode)` directly to get a list of errors.

**With ScalaTest**: Your test suite should extend the `MountSpec` trait. Use `mount` and `expectNode` methods in your test code. You can call `unmount` and then `mount` again within one test if you want to test multiple unrelated nodes (e.g. different variations in a loop).

**Without ScalaTest**: Write a tiny adapter like `MountSpec` for your test framework, which would:
 
- Extend `MountOps` and provide `doAssert` / `doFail` implementations specific to your test framework
- Call `resetDOM` in the beginning of each test, and `clearDOM` at the end of each test.

Pull requests for such adapters for popular test frameworks are welcome.

**To power your library's test utils**: If you are building or using a DOM construction / manipulation library like e.g. React, you might want to override the `mount` / `unmount` methods to give your library the chance to do proper setup and cleanup. Instead of calling super methods for these, you can make use of `assertEmptyContainer` / `assertRootNodeMounted` / `mountedElementClue` the same way they are used in the default implementations in `MountOps`.

For an example of the above, see [LaminarSpec](https://github.com/raquo/Laminar/blob/master/src/test/scala/com/raquo/laminar/utils/LaminarSpec.scala), which defines custom mounting / unmounting logic for [Laminar](https://github.com/raquo/Laminar)'s reactive nodes.

If all this is more hassle than it's worth for your use case, just forgo `MountOps` and drop down to calling `ExpectedNode.checkNode(actualNode)` to get a list of errors, and build your own test util around it.  



## Project Status

This project exists only to serve the needs of testing [Laminar](https://github.com/raquo/Laminar) and the basic needs of testing Laminar applications. Emphasis on _basic_. This is not going to be a full fledged test kit, nor are there any guarantees of documentation or stability. I just don't have the time for this. If you want something bigger, you'll need to fork this and/or create your own. It's a very small project anyway.


## Versioning

There is no promise of any backwards compatibility in this particular project. I _roughly_ align versions with Scala DOM Types for my own convenience.


## My Related Projects

- [Scala DOM Types](https://github.com/raquo/scala-dom-types) – Type definitions that we use for all the HTML tags, attributes, properties, and styles
- [Laminar](https://github.com/raquo/Laminar) – Reactive UI library based on _Scala DOM Types_
- [Scala DOM Builder](https://github.com/raquo/scala-dom-builder) – Low-level library for building and manipulating DOM trees



## Author

Nikita Gazarov – [@raquo](https://twitter.com/raquo)

License – MIT
