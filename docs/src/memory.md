# Memory Management

Oxynium does not have a garbage collector or reference counting. Memory management is manual
for heap-allocated values, but most programs never need to think about this — `Str`, `List<T>`,
and other standard types manage their own memory internally.

The only explicit memory primitive is `Ptr<T>`.

## `Ptr<T>`

`primitive Ptr<T>` — an unmanaged pointer to a heap-allocated value of type `T`. The programmer
is fully responsible for memory.

### Creating a pointer

```oxynium
let p = Ptr.make!<Int>(42)
```

This allocates 8 bytes on the heap, stores `42`, and returns a pointer to that memory.

### Methods

| Method    | Signature                         | Description |
|-----------|-----------------------------------|-------------|
| `make`    | `static <From>(val: From) Ptr<From>` | Allocate and store a value |
| `unwrap`  | `(self) T`                        | Dereference the pointer; no null check |
| `is_null` | `(self) Bool`                     | True if the address is 0 (null pointer) |
| `Str`     | `(self) Str`                      | `"Ptr(<address>)"` |

```oxynium
let p = Ptr.make!<Int>(42)
print(p.is_null().Str())    // false
print(p.unwrap().Str())     // 42
```

### Null pointers

```oxynium
def main() {
    let null_ptr = #unchecked_cast(Ptr<Int>, 0)
    print(null_ptr.is_null().Str())    // true
    // calling null_ptr.unwrap() causes undefined behaviour
}
```

### Safety

`Ptr<T>` bypasses the type system entirely. Dereferencing a null or invalid pointer is
undefined behaviour. Prefer `Option<T>` or `Result<T, E>` for safe optional/fallible values.
Use `Ptr<T>` only when interfacing with memory, system calls, or external C functions.

## Unsafe type casting (`#unchecked_cast`)

```oxynium
#unchecked_cast(TargetType, expression)
```

The bits of `expression` are reinterpreted directly as `TargetType`. No conversion occurs.
Equivalent to a C-style transmute.

Common uses:

```oxynium
#unchecked_cast(Ptr<Int>, 0)    // create a null pointer
#unchecked_cast(Int, my_ptr)    // pointer address as integer
#unchecked_cast(Int, 'A')       // 65
```

Completely unsafe. Misuse causes undefined behaviour. Prefer the safe conversion methods
(`Int.Bool()`, `Char.Int()`, etc.) where possible.

## Inline assembly (`#asm`)

For complete control over generated code, `#asm` embeds raw x86-64 NASM assembly:

```oxynium
#asm ReturnType "assembly string"
```

Must appear inside a function body. The assembly string must be a compile-time literal.
For non-`Void` return types, the assembly must push the result onto the stack.

Parameters are available on the stack relative to `rbp`:
- 1st parameter: `[rbp + 16]`
- 2nd parameter: `[rbp + 24]`
- nth parameter: `[rbp + (8 + n*8)]`

```oxynium
def asm_passthrough(arg: Str) Str {
    return #asm Str "
        push qword [rbp + 16]
    "
}
print(asm_passthrough("hi"))    // hi
```

Using `#asm` makes your code non-portable to architectures other than x86-64.
