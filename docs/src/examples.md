# Examples

These are a few simple examples which showcase some of the language's features.

## Hello, World!

```oxynium
print("Hello, World!")
```

## Fibonacci

```oxynium
const N = 10

def fib (n: Int) Int {
    if n <= 1 {
        return n
    }
    return fib(n - 1) + fib(n - 2)
}

print(fib(N).Str()) // 55
```

## FizzBuzz

```oxynium
def main() {
	for i in range(100) {
		print(fizzbuzz(i))
	}
}

def fizzbuzz (n: Int) Str {
    if n % 3 == 0 && n % 5 == 0 {
        return "FizzBuzz"
    }
    if n % 3 == 0 -> return "Fizz"
    if n % 5 == 0 -> return "Buzz"
    return n.Str()
}
```