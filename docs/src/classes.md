# Classes

## Defining a class

```oxynium
class Point {
    x: Int,
    y: Int
}
```

Fields are separated by commas. A trailing comma is allowed. Fields and method definitions
can be interleaved in any order.

Empty class forms:

```oxynium
class Token
class Empty;
class Empty {}
```

Classes cannot be defined inside other classes (`SyntaxError`). Redefining an existing class
name is a `TypeError`.

## Instantiation (`new`)

```oxynium
let p = new Point { x: 3, y: 4 }
```

All fields must be provided. Providing the wrong fields, wrong count, or wrong types is a
`TypeError`. For classes with no fields, `new C`, `new C{}`, and `new C { }` are all valid.

**Field shorthand:** if a local variable has the same name as a field, the name alone can be used:

```oxynium
class C { a: Int }

def main() {
    let a = 1
    print(new C { a }.a.Str())    // prints 1
}
```

## Field access and assignment

Fields can be read and assigned through any variable — field mutation is independent of
whether the variable itself is `mut`:

```oxynium
class A { a: Int }

def main() {
    let a = new A { a: 1 }
    a.a = 2
    print(a.a.Str())    // 2
}
```

## Instance methods

Defined with `self` as the first parameter. `self` must not have a type annotation:

```oxynium
class Point {
    x: Int,
    y: Int,
    def magnitude_sq(self) Int -> self.x * self.x + self.y * self.y
}

let p = new Point { x: 3, y: 4 }
print(p.magnitude_sq().Str())    // 25
```

## Static methods

Methods without a `self` parameter. Called on the class name, not on an instance:

```oxynium
class Counter {
    count: Int,
    def zero() Counter -> new Counter { count: 0 }
}
let c = Counter.zero()
```

**Calling instance methods statically** by passing the instance explicitly is also valid:

```oxynium
class S {
    def f(self, msg: Str) Str { return msg }
}
def main() {
    print(S.f(new S, "abc"))    // abc
}
```

## Default parameters on methods

Same rules as for standalone functions:

```oxynium
class Greeter {
    def say(self, msg: Str = "Hello") {
        print(msg)
    }
}
new Greeter{}.say()        // Hello
new Greeter{}.say("Hi")   // Hi
```

## Static methods as first-class values

Static methods can be stored in variables and used as first-class functions.
Instance methods cannot:

```oxynium
class S {
    def g() -> 1
}
def main() {
    let g = S.g
    print(g().Str())    // 1
}
```

## Operator overloading

See [Operators](./operator_overriding.md) for full details:

```oxynium
class Foo {
    x: Int,
    def + (self, other: Foo) Foo {
        return new Foo { x: self.x + other.x }
    }
}
```

## Class composition

```oxynium
class Inner { value: Int }
class Outer { inner: Inner, tag: Str }

def main() {
    let o = new Outer {
        inner: new Inner { value: 42 },
        tag: "hello"
    }
    print(o.inner.value.Str())    // 42
    print(o.tag)                  // hello
}
```
