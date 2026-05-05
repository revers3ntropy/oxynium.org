# Generics

## Generic classes

Declared with one or more type parameters in angle brackets:

```oxynium
class Box<T> {
    value: T
}

let b = new Box<Int> { value: 42 }
print(b.value.Str())    // 42
```

Multiple type parameters:

```oxynium
class Pair<A, B> {
    first: A,
    second: B
}

let p = new Pair<Int, Str> { first: 1, second: "one" }
print(p.first.Str())    // 1
print(p.second)         // one
```

Type parameters are in scope for all field declarations and method bodies within the class.
Providing the wrong concrete type for a generic field is a `TypeError`.

## Generic methods on generic classes

Instance methods on a generic class can introduce their own additional type parameters:

```oxynium
class Wrapper<T> {
    data: T,
    def map<U>(self, f: Fn(T) U) Wrapper<U> ->
        new Wrapper<U> { data: f(self.data) }
}

let w = new Wrapper<Int> { data: 5 }
let s = w.map!<Str>(fn (n: Int) Str -> n.Str())
print(s.data)    // 5
```

A method-level type parameter must not duplicate the class-level parameter name — this is a
`TypeError`.

## Generic functions

```oxynium
def identity<T>(x: T) T -> x

print(identity!<Int>(42).Str())    // 42
print(identity!<Str>("hello"))     // hello
```

Multiple type parameters:

```oxynium
def apply<T, A>(t: T, f: Fn(T) A) A -> f(t)

const x = 2
print(apply!<Int, Int>(x, fn (n: Int) -> n + 1).Str())    // 3
```

## Instantiation syntax

Generic functions and methods are called with `!<Type>` between the name and the argument list:

```oxynium
identity!<Int>(42)
List.empty!<Int>()
list.map!<Str>(fn (n: Int, i: Int) -> n.Str())
```

Omitting the `!<Type>` on a generic function is a `TypeError`. Providing the wrong number
of type arguments is also a `TypeError`.

## First-class generic functions

Generic functions can be stored in variables and instantiated through the variable:

```oxynium
def f<T>(a: T) T -> a

def main() {
    let f_ = f
    print(f_!<Str>("abc"))    // abc
}
```

## Nested generics and chaining

```oxynium
let nested = new Box<Box<Int>> {
    value: new Box<Int> { value: 1 }
}
print(nested.value.value.Str())    // 1
```

Chained generic method calls:

```oxynium
let result = List.empty!<Int>()
    .map!<Str>(fn (n: Int, i: Int) -> n.Str())
    .map!<Int>(fn (s: Str, i: Int) -> s.len())
```

## Type parameters are not first-class

Passing an unknown type `T` to a function that does not declare it is an `UnknownSymbol`:

```oxynium
def a<T>(x: T) T { return x }
a!<T>("")    // UnknownSymbol: T is not defined at the call site
```
