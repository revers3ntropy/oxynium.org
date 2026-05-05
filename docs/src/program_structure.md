# Program Structure

## Two modes of execution

**Script mode** — no `main` function. Top-level statements execute in the order they appear:

```oxynium
const MSG = "Hello"
print(MSG)          // runs immediately
print("World")      // runs immediately
```

**Main mode** — a `main` function is defined. Only `main`, `const`, `class`, `def`, and
`extern def` may appear at the top level. Executable statements outside `main` are a
`SyntaxError`:

```oxynium
def f() {}
def main() {
    print("hi")
}
f()           // SyntaxError: executable statement after main definition
```

## What can appear at the top level

In script mode: `const`, `class`, `def`, `extern def`, and any executable statement.
In main mode: `const`, `class`, `def`, `extern def` only.

## Semicolons

Semicolons are optional. The compiler performs automatic end-of-statement insertion. Both
styles compile identically:

```oxynium
print("a"); print("b")
print("a")
print("b")
```

## Declaration order

The compiler performs a setup pass before type checking, so functions and classes can be
used before their definition in the source file:

```oxynium
def f(a: A) A { return a }
class A;    // A is used above its definition — valid
```

Local variables, however, cannot be used before their `let` declaration in the same
function body — this is a `TypeError`.

## Locally-defined classes

Classes can be defined inside function bodies and are scoped to that function:

```oxynium
def main() {
    class C
    let x = new C    // C is usable here
}
def f(a: C)    // UnknownSymbol: C is not in scope here
```

## Comments

Single-line comments start with `//` and run to the end of the line:

```oxynium
// This entire line is a comment.
let x = 1  // This is an inline comment.
```

There is no block comment syntax. Use multiple `//` lines.

`///` is used in the standard library for documentation annotations. In user code, `///`
behaves identically to `//`.
