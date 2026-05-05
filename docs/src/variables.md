# Variables and Constants

## Global constants (`const`)

Declared at the top level of a file (outside any function). Always require an initial value.
Type is inferred from the value. Cannot be mutated.

```oxynium
const MAX = 100
const GREETING = "Hello"
const PI_APPROX = 314
```

Constants may reference expressions involving other constants:

```oxynium
const BASE = 10
const LIMIT = BASE * 5   // LIMIT = 50
```

Attempting to redeclare a constant with the same name is a `TypeError`. Declaring a `const`
inside a function is a `SyntaxError`.

## Local variables (`let`)

Declared inside a function body. Immutable by default — the variable cannot be reassigned
after its initial binding.

```oxynium
def main() {
    let x = 42
    let name = "Alice"
    let greeting = "Hello, " + name
}
```

An optional type annotation must match the inferred type:

```oxynium
let x: Int = 42      // valid
let x: Int = ""      // TypeError: mismatched types
```

Variables cannot be declared without an initial value (use `let mut` for that):

```oxynium
def f() {
    let a;        // SyntaxError: missing value
}
```

Redeclaring the same name in the same function is a `TypeError`. There is no shadowing.

Declaring a `let` at the top level is a `SyntaxError`.

## Mutable local variables (`let mut`)

Allows reassignment after the initial binding. The type is fixed at declaration.

```oxynium
def main() {
    let mut count = 0
    count = count + 1
    count = 99
}
```

Assigning a different type is a `TypeError`:

```oxynium
def main() {
    let mut a = 1
    a = ""    // TypeError: cannot assign Str to Int
}
```

## Empty `let mut` declarations

`let mut` may be declared with a type annotation but no initial value. The variable must be
assigned before it is read:

```oxynium
def f(condition: Bool) Int {
    let mut result: Int
    if condition {
        result = 1
    } else {
        result = 2
    }
    return result    // valid: both branches assign
}
```

Reading an uninitialised variable is a `TypeError`. An immutable `let` cannot use the
empty declaration form.

## Compound assignment operators

Shorthand for `variable = variable OP value`. The variable must be `mut`.

| Operator | Example  | Equivalent   |
|----------|----------|--------------|
| `+=`     | `a += 3` | `a = a + 3`  |
| `-=`     | `a -= 3` | `a = a - 3`  |
| `*=`     | `a *= 3` | `a = a * 3`  |
| `/=`     | `a /= 3` | `a = a / 3`  |
| `%=`     | `a %= 3` | `a = a % 3`  |

```oxynium
def main() {
    let mut a = 1
    a += 1    // 2
    a -= 1    // 1
    a *= 4    // 4
    a /= 2    // 2
    print(a.Str())
}
```

`+=` also works on `Str` variables (string concatenation):

```oxynium
def main() {
    let mut s = "hello"
    s += " world"
    print(s)    // hello world
}
```

## Scoping rules

Oxynium does **not** have block-level scoping. A variable declared inside an `if`, `while`,
or `for` block is accessible in the enclosing function after that block. Redeclaring a name
from an outer scope inside an inner block is a `TypeError`:

```oxynium
def main() {
    let a = 1
    if true {
        let a = 2    // TypeError: duplicate declaration
    }
}
```

For-loop variables persist after the loop ends, holding the last values seen:

```oxynium
def main() {
    for c, i in "abc" {}
    // c = 'c', i = 2 — last values from the loop
    print(i.Str() + c.Str())    // 2c
}
```

## Field mutation through immutable variables

The `let` / `let mut` distinction controls whether the *variable binding* can be reassigned.
Fields on a class instance can still be mutated through a non-`mut` variable:

```oxynium
class Point { x: Int, y: Int }

def main() {
    let p = new Point { x: 0, y: 0 }
    p.x = 5    // valid: mutating a field, not reassigning p
}
```
