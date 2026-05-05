# Primitives

Primitives are types backed by a single 64-bit value rather than a heap-allocated struct.
They are not stored as a reference.

The built-in primitives are `Int`, `Bool`, `Char`, and `Ptr<T>`. These are defined using the
`primitive` keyword in the standard library. User code **cannot** define new primitive types —
`primitive MyType` is a `TypeError`.

`new Int` produces `0`. `new Bool` produces `false`. `new Char` produces the null character.

## `Int`

64-bit signed integer. Range: −2⁶³ to 2⁶³−1.

Integer literals exceeding 2⁶³−1 produce a `NumericOverflow` error at compile time.
Runtime overflow wraps around silently (two's complement):

```oxynium
print((9223372036854775807 + 1).Str())    // -9223372036854775808
```

| Method    | Description |
|-----------|-------------|
| `Str`     | Convert to decimal string |
| `Bool`    | `0` → `false`, any other value → `true` |
| `max(other=MAX_INT)` | Return the larger of the two values |
| `min(other=MIN_INT)` | Return the smaller of the two values |
| `abs`     | Absolute value |
| `compare(other)` | Returns `-1`, `0`, or `1` |

```oxynium
print((-5).abs().Str())      // 5
print(3.max(7).Str())        // 7
print(10.min(4).Str())       // 4
print(0.Bool().Str())        // false
print((-1).Bool().Str())     // true
print(Int.compare(1, 2).Str()) // -1
```

## `Bool`

Boolean type. Literals are `true` and `false`.

Operators: `&&` (AND), `||` (OR), `!` (NOT). Neither `&&` nor `||` short-circuits.

| Method | Description          |
|--------|----------------------|
| `Str`  | `"true"` or `"false"` |

## `Char`

Single Unicode character. Single-quoted literals: `'a'`, `'Z'`, `'💖'`.

Escape sequences: `'\n'` (newline), `'\t'` (tab), `'\r'` (CR), `'\\'` (backslash),
`'\''` (single quote), `'\"'` (double quote).

| Method           | Description |
|------------------|-------------|
| `Str`            | Convert to a one-character string |
| `Int`            | Code point as integer |
| `from_int(i)`    | Create `Char` from a code point (static) |
| `is_digit`       | True for `'0'`–`'9'` |
| `is_alphabetic`  | True for `'A'`–`'Z'` or `'a'`–`'z'` |
| `is_uppercase`   | True for `'A'`–`'Z'` |
| `is_lowercase`   | True for `'a'`–`'z'` |
| `is_alphanumeric`| True for alphabetic or digit |
| `is_ascii`       | True if code point < 128 |
| `is_whitespace`  | True for `' '`, `'\n'`, `'\t'`, `'\r'` |

```oxynium
print('A'.is_uppercase().Str())    // true
print('3'.is_digit().Str())        // true
print('a'.Int().Str())             // 97
print(Char.from_int(65).Str())     // A
```

## `Ptr<T>`

An unmanaged pointer to a heap-allocated value. No garbage collection or reference counting.
Use `Ptr<T>` only for low-level code — prefer `Option<T>` or `Result<T, E>` for safe values.

```oxynium
def main() {
    let p = Ptr.make!<Int>(42)
    print(p.unwrap().Str())     // 42
    print(p.is_null().Str())    // false
}
```

See [Memory Management](./memory.md) for full details.
