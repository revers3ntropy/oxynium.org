# Operators

## Arithmetic operators

All arithmetic operators work on `Int` operands and return `Int`. Mixing types is a `TypeError`.

| Operator | Description      | Example | Result |
|----------|------------------|---------|--------|
| `+`      | Addition         | `3 + 4` | `7`    |
| `-`      | Subtraction      | `10 - 3`| `7`    |
| `*`      | Multiplication   | `3 * 4` | `12`   |
| `/`      | Integer division | `7 / 2` | `3`    |
| `%`      | Modulo           | `7 % 2` | `1`    |

Integer division truncates toward zero. Division or modulo by the literal `0` is a
compile-time `TypeError`. Division by a runtime zero value causes undefined behaviour.

Operator precedence follows standard mathematical convention:
- `*`, `/`, `%` bind tighter than `+`, `-`
- Left-to-right associativity within the same precedence level

```oxynium
print((1 + 2 * 3 + 4 * 5).Str())      // 27  (= 1 + 6 + 20)
print((1 + 2 * 3 - 4 * 5 / 2).Str())  // -3
```

## Unary operators

| Operator | Description        | Example | Result  |
|----------|--------------------|---------|---------|
| `-`      | Arithmetic negation| `-5`    | `-5`    |
| `!`      | Logical NOT        | `!true` | `false` |

Unary `-` applies only to `Int`. `!` applies only to `Bool`. Applying `!` to an `Int`
is a `TypeError`.

## Comparison operators

Return `Bool`. For `Int`, all six are built in. `Str` supports `==` and `!=` only.

| Operator | Description           |
|----------|-----------------------|
| `==`     | Equal                 |
| `!=`     | Not equal             |
| `<`      | Less than             |
| `>`      | Greater than          |
| `<=`     | Less than or equal    |
| `>=`     | Greater than or equal |

Comparison operators are **not chainable** — `1 < 2 < 3` is a `TypeError` because
the result of `1 < 2` is `Bool`, and `Bool < Int` is not defined.

## Logical operators

| Operator | Description  | Operand types | Return type |
|----------|--------------|---------------|-------------|
| `&&`     | Logical AND  | `Bool, Bool`  | `Bool`      |
| `\|\|`   | Logical OR   | `Bool, Bool`  | `Bool`      |
| `!`      | Logical NOT  | `Bool`        | `Bool`      |

`&&` and `||` do **not** short-circuit — both sides are always evaluated.

```oxynium
true && false    // false
true || false    // true
!true            // false
0 && 1           // TypeError: && requires Bool operands
```

## String concatenation

`+` on two `Str` values returns a new `Str`:

```oxynium
"Hello, " + "world!"    // "Hello, world!"
"" + "abc"              // "abc"
```

## None-coalescing operator (`??`)

Unwraps an `Option<T>`. Returns the contained value if `some`, or the right-hand default
if `none`:

```oxynium
let x: Int? = Option.none!<Int>()
let v = x ?? 42     // v = 42

let y: Int? = Option.some!<Int>(7)
let w = y ?? 42     // w = 7
```

## Compound assignment

`+=`, `-=`, `*=`, `/=`, `%=` — see [Variables](./variables.md). These are not overloadable.

## Full precedence table (high to low)

| Level | Operators                          | Associativity |
|-------|------------------------------------|---------------|
| 1     | unary `-`, `!`, `typeof`           | right         |
| 2     | `*`, `/`, `%`                      | left          |
| 3     | `+`, `-`                           | left          |
| 4     | `<`, `>`, `<=`, `>=`              | left          |
| 5     | `==`, `!=`                         | left          |
| 6     | `&&`                               | left          |
| 7     | `\|\|`                             | left          |
| 8     | `??`                               | left          |
| 9     | `=`, `+=`, `-=`, `*=`, `/=`, `%=` | right         |

## Operator overloading

Classes may define methods named after binary operator symbols. Only binary operators may
be overloaded; unary operators (`!`) may not.

Valid overloadable operators: `+`, `-`, `*`, `/`, `%`, `==`, `!=`, `<`, `>`, `<=`, `>=`,
`&&`, `||`, `??`.

Rules:
- The method must take exactly one parameter in addition to `self`.
- That parameter must not have a default value.
- Operator methods can only be defined inside a `class`, not at the top level.
- Compound assignment operators (`+=` etc.) cannot be overloaded.

```oxynium
class Vec2 {
    x: Int,
    y: Int,
    def + (self, other: Vec2) Vec2 ->
        new Vec2 { x: self.x + other.x, y: self.y + other.y },
    def == (self, other: Vec2) Bool ->
        self.x == other.x && self.y == other.y
}

def main() {
    let a = new Vec2 { x: 1, y: 2 }
    let b = new Vec2 { x: 3, y: 4 }
    let c = a + b
    print(c.x.Str())          // 4
    print(c.y.Str())          // 6
    print((a == b).Str())     // false
}
```

Invalid overload forms:

```oxynium
class C {
    def ! (self) C { ... }              // SyntaxError: ! is not overloadable
    def += (self, other: C) C { ... }   // SyntaxError: compound assignment not overloadable
    def + (self) C { ... }              // TypeError: must take exactly one non-self parameter
}
def + (a: C, b: C) C { ... }           // SyntaxError: top-level operator definition
```
