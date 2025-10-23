# Classes

```oxynium
class MyClass {
    name: Str,
    
    def my_instance_method(self) {
        print("hi from " + self.name)
    }
    
    def my_static_method() {
        // no access to 'name' from here
        print("hi from MyClass")
    }
}
```