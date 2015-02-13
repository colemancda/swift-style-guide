# Swift Style Guide
Swift style guide &amp; coding conventions

#### Whitespace

 * Tabs, not spaces.
 * End files with a newline.
 * Make liberal use of vertical whitespace to divide code into logical chunks.
 * Don’t leave trailing whitespace.
   * Not even leading indentation on blank lines.

#### Prefer `let`-bindings over `var`-bindings wherever possible

Use `let foo = …` over `var foo = …` wherever possible (and when in doubt). Only use `var` if you absolutely have to (i.e. you *know* that the value might change, e.g. when using the `weak` storage modifier).

_Rationale:_ The intent and meaning of both keywords is clear, but *let-by-default* results in safer and clearer code.

A `let`-binding guarantees and *clearly signals to the programmer* that its value is supposed to and will never change. Subsequent code can thus make stronger assumptions about its usage.

It becomes easier to reason about code. Had you used `var` while still making the assumption that the value never changed, you would have to manually check that.

Accordingly, whenever you see a `var` identifier being used, assume that it will change and ask yourself why.

#### Avoid Using Implicitly Unwrapped Optionals

Where possible, use `let foo: FooType?` instead of `let foo: FooType!` if foo may be nil (Note that in general, `?` can be used instead of `!`).

_Rationale:_ Explicit optionals result in safer code. Implicitly unwrapped optionals have the potential of crashing at runtime.

#### Prefer implicit getters on read-only properties and subscripts

When possible, omit the `get` keyword on read-only computed properties and
read-only subscripts.

So, write these:

```swift
var myGreatProperty: Int {
	return 4
}

subscript(index: Int) -> T {
    return objects[index]
}
```

… not these:

```swift
var myGreatProperty: Int {
	get {
		return 4
	}
}

subscript(index: Int) -> T {
    get {
        return objects[index]
    }
}
```

_Rationale:_ The intent and meaning of the first version is clear, and results in less code.

#### Always specify access control explicitly for top-level definitions in frameworks

Top-level functions, types, and variables should always have explicit access control specifiers:

```swift
public var whoopsGlobalState: Int
internal struct TheFez {}
private func doTheThings(things: [Thing]) {}
```

However, definitions within those can leave access control implicit, where appropriate:

```swift
internal struct TheFez {
	var owner: Person = Joshaber()
}
```

_Rationale:_ Most top-level definitions for frameworks will not be specifically `internal`, and being explicit ensures that careful thought goes into that decision. Within a definition, reusing the same access control specifier is just duplicative, and the default is usually reasonable.

#### Never specify access control explicitly for top-level definitions in app sources

Top-level functions, types, and variables should always have implicit access control specifiers.

_Rationale:_ Swift code written for an app will only be used within that app. Not specifying an access control level will default it to ```internal```, which is ideal for code that will not be used outside of the application's executable. If you have custom views, or model code that might be resuable, put them in a separate framework. Ideally your app's Swift sources will only consist of controller classes and app-specific variables, enums, etc.

#### When specifying a type, always associate the colon with the identifier

When specifying the type of an identifier, always put the colon immediately
after the identifier, followed by a space and then the type name.

```swift
class SmallBatchSustainableFairtrade: Coffee { ... }

let timeToCoffee: NSTimeInterval = 2

func makeCoffee(type: CoffeeType) -> Coffee { ... }
```

_Rationale:_ The type specifier is saying something about the _identifier_ so
it should be positioned with it.

Also, when specifying the type of a dictionary, always put the colon immediately
after the key type, followed by a space and then the value type.

```swift
let capitals: [Country: City] = [Sweden: Stockholm]
```

#### Always explicitly refer to `self`

When accessing properties or methods on `self`, explicitly reference `self`:

```swift
private class History {
	var events: [Event]

	func rewrite() {
		self.events = []
	}
}
```

_Rationale:_ This avoids confusion with local variables or method arguments that might share the same name as a property. Also avoids confusion between methods and functions.

#### Functions should always start with an uppercase letter

When creating a function, the name of the function should always start with a lowercase letter.

```swift
func SaveDefaults() { ... }
```

_Rationale:_ Follows the Objective-C coding style found in Cocoa APIs. Also avoids confusion between methods and functions.

#### Methods should always start with a lowercase letter

When creating a method, the name of the method should always start with an uppercase letter.

```swift
public class History {
	var events: [Event]

	func rewrite() {
		self.events = []
	}
}
```

_Rationale:_ Follows the Objective-C coding style found in Cocoa APIs. 

#### Prefer classes over structs

_Rationale:_ Classes provide memory management while structs are not accessed by reference. Use structs when you need light weight containers for classes, as they are copied erverytime they are referenced.

From [The Swift Programming Language](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/ClassesAndStructures.html#//apple_ref/doc/uid/TP40014097-CH13-XID_108) documentation:

>Structure instances are always passed by value, and class instances are always passed by reference. This means that they are suited to different kinds of tasks. As you consider the data constructs and functionality that you need for a project, decide whether each data construct should be defined as a class or as a structure.
>
>As a general guideline, consider creating a structure when one or more of these conditions apply:
>
>The structure’s primary purpose is to encapsulate a few relatively simple data values.
>It is reasonable to expect that the encapsulated values will be copied rather than referenced when you assign or pass around an instance of that structure.
>Any properties stored by the structure are themselves value types, which would also be expected to be copied rather than referenced.
>The structure does not need to inherit properties or behavior from another existing type.
>Examples of good candidates for structures include:
>
>The size of a geometric shape, perhaps encapsulating a width property and a height property, both of type Double.
>A way to refer to ranges within a series, perhaps encapsulating a start property and a length property, both of type Int.
>A point in a 3D coordinate system, perhaps encapsulating x, y and z properties, each of type Double.
>In all other cases, define a class, and create instances of that class to be managed and passed by reference. In practice, this means that most custom data constructs should be classes, not structures.

#### Omit type parameters where possible

Methods of parameterized types can omit type parameters on the receiving type when they’re identical to the receiver’s. For example:

```swift
struct Composite<T> {
	…
	func compose(other: Composite<T>) -> Composite<T> {
		return Composite<T>(self, other)
	}
}
```

could be rendered as:

```swift
struct Composite<T> {
	…
	func compose(other: Composite) -> Composite {
		return Composite(self, other)
	}
}
```

_Rationale:_ Omitting redundant type parameters clarifies the intent, and makes it obvious by contrast when the returned type takes different type parameters.

