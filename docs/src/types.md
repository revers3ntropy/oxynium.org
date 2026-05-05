# Types

## Built-in primitive types

| Type   | Description                | Default (`new T`) | Size    |
|--------|----------------------------|-------------------|---------|
| `Int`  | 64-bit signed integer      | `0`               | 8 bytes |
| `Bool` | Boolean                    | `false`           | 8 bytes |
| `Char` | Single Unicode character   | null char (0)     | 8 bytes |
| `Str`  | Immutable character string | `""` (empty)      | pointer |
| `Void` | Unit / no value            | —                 | —       |

`Void` is the return type of functions that produce no value. `new Void` is a valid expression
used in low-level code but has no useful value.

## Literals

```oxynium
42          // Int
-7          // unary minus applied to Int 7
true        // Bool
false       // Bool
'a'         // Char
'\n'        // Char — newline
"hello"     // Str
""          // Str — empty string
```

## Type annotations

Annotations appear after the identifier, separated by `:`:

```oxynium
let x: Int = 5
let s: Str = "hello"
def f(a: Int, b: Bool) Str { ... }
```

## Type inference

The compiler infers types when they can be determined from context. Parameter types are
**never** inferred — they must always be explicit. Return types can be inferred for
single-expression (`->`) functions.

```oxynium
let x = 42               // inferred: Int
let s = "hi"             // inferred: Str
def square(n: Int) -> n * n   // return type inferred: Int
```

Using a variable before it is declared is a `TypeError`:

```oxynium
def f() Int {
    let b = a    // TypeError: a not yet declared
    let a = 5
    return b
}
```

## Generic types

Parameterised with angle brackets. The standard library provides `List<T>`, `Option<T>`,
`Result<T, E>`, `Ptr<T>`, and `Range`.

```oxynium
List<Int>
Option<Str>
Result<Int, Str>
Ptr<Bool>
```

Nesting is allowed:

```oxynium
List<Option<Int>>
Option<List<Str>>
Result<Option<Int>, Str>
```

## Optional shorthand

`T?` is syntactic sugar for `Option<T>`. Multiple `?` can be chained:

```oxynium
let a: Int?        // Option<Int>
let b: Int??       // Option<Option<Int>>
let c: Str?        // Option<Str>
```

## Function types

Written as `Fn(Param1, Param2, ...) ReturnType`. Used as parameter or variable types.

```oxynium
def apply(f: Fn(Int) Int) Int { return f(5) }
def run(f: Fn() Void) { f() }
def combine(f: Fn(Int, Str) Bool) { ... }
```

Generic function types (`Fn<T>(T) T`) are not permitted — this is a `SyntaxError`.

## The `typeof` operator

`typeof expr` evaluates to a `Str` naming the compile-time type of the expression.
See [typeof](./typeof.md).
