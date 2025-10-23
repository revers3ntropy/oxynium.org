# Functions

```oxynium
def my_function (param: Int) Int {
    return param * 9
}
```

equiv to

```oxynium
let my_function = fn (param: Int) -> param * 9
```

*Note that `let` can only be used inside a function definition, 
so this definition cannot be used at the top level*