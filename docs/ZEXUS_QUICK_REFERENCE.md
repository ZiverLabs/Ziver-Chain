# Zexus Quick Reference Card

A cheat sheet for quick reference while coding in Zexus.

## Variables

```zexus
let variable = value
let name: string = "text"
let count: integer = 42
let price: float = 9.99
let active: boolean = true
let optional: string?
```

## Actions (Functions)

```zexus
action function_name(param: type) {
    # code
}

action with_return(x: integer) -> integer {
    return x * 2
}

action async async_function() {
    await some_call()
}
```

## Control Flow

```zexus
if condition {
    # code
} else if other {
    # code
} else {
    # code
}

for each item in list {
    # code
}

for each key, value in map {
    # code
}

while condition {
    # code
}
```

## Contracts

```zexus
contract Name {
    name: "Name"
    version: "1.0.0"
    
    persistent storage field: type
    
    action init() {
        this.field = value
    }
    
    action method() -> boolean {
        return true
    }
}
```

## Entities

```zexus
entity Person {
    name: string
    age: integer
    email: string?
}

let person = Person{
    name: "Alice",
    age: 30
}
```

## Protocols

```zexus
protocol Interface {
    action method(param: type) -> type
}

contract Implementation implements Interface {
    action method(param: type) -> type {
        # implementation
    }
}
```

## Imports

```zexus
use "module" as alias
use "../path/file.zx" as name
use { Item1, Item2 } from "./file.zx"
```

## Collections

```zexus
# Lists
let list = [1, 2, 3]
list.push(4)
list[0]
list.contains(item)
len(list)

# Maps
let map = {"key": "value"}
map["key"] = "new value"
map.get("key", default)
map.has("key")
```

## Operators

```zexus
# Arithmetic
+ - * /

# Comparison
== != < > <= >=

# Logical
and or not

# Assignment
= += -= *= /=
```

## Type Conversion

```zexus
string(value)
integer(value)
float(value)
```

## String Operations

```zexus
"text" + "more"
len(text)
```

## Common Patterns

```zexus
# Error handling
if error_condition {
    throw "Error message"
}

# Null checks
if value != null {
    use(value)
}

# Validation
if not is_valid(data) {
    return false
}

# Print with emojis
print("✅ Success")
print("❌ Error")
print("💰 Transaction")
print("🚀 Starting")
```

## Comments

```zexus
# Single line comment

"""
Multi-line comment
or docstring
"""
```

## Built-in Functions

```zexus
print(message)
len(collection)
string(value)
integer(value)
datetime.now()
datetime.now().timestamp()
math.random_int(min, max)
```

---

For full documentation, see [ZEXUS_SYNTAX_REFERENCE.md](./ZEXUS_SYNTAX_REFERENCE.md)
