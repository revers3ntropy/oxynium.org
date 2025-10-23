# Primitives

Primitives are like classes, except they can only hold one 64-bit value,
and are not stored as a reference.

There are several built-in primitives: `Int`, `Char`, `Bool`, `Ptr`.

Other primitives generally should not be created: instead use classes.

```oxynium
primitive MyPrimitive {
    // cannot declare fields on primitives,
    // only methods and static methods
    
    def do_something(self) {}
    def something_static() {}
}
```