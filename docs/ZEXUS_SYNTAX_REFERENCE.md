# Zexus Language Syntax Reference

This document provides a comprehensive reference for the Zexus programming language syntax based on the Ziver-Chain codebase.

## About Zexus-interpreter Repository

**Note**: This reference is compiled from the Ziver-Chain repository. If you have a separate `Zexus-interpreter` repository that contains the full language specification and interpreter implementation, it is currently not publicly accessible under the ZiverLabs organization. 

To make the Zexus-interpreter repository accessible:
1. Ensure the repository is public (if it exists as a private repository)
2. Or share the repository URL if it exists under a different organization/account
3. Alternatively, consider adding it as a git submodule to this repository

## Table of Contents

1. [Basic Syntax](#basic-syntax)
2. [Variables and Types](#variables-and-types)
3. [Functions (Actions)](#functions-actions)
4. [Control Flow](#control-flow)
5. [Contracts](#contracts)
6. [Protocols and Interfaces](#protocols-and-interfaces)
7. [Entities (Data Structures)](#entities-data-structures)
8. [Storage](#storage)
9. [Module System](#module-system)
10. [Advanced Features](#advanced-features)

## Basic Syntax

### Comments

```zexus
# Single-line comment

"""
Multi-line comment
or docstring
"""
```

### Print Statement

```zexus
print "Hello, World!"
print("Hello with parentheses")
print("Variable: " + variable_name)
print("Number: " + string(42))
```

## Variables and Types

### Variable Declaration

```zexus
# Using let keyword
let message = "Hello"
let count = 42
let pi = 3.14
let is_valid = true

# Type annotations (optional)
let name: string = "Ziver"
let age: integer = 25
let score: float = 95.5
let active: boolean = true
```

### Built-in Types

- `string` - Text strings
- `integer` - Whole numbers
- `float` - Decimal numbers
- `boolean` - true/false values
- `list` - Dynamic arrays
- `map` - Hash maps/dictionaries
- `any` - Dynamic type

### Collections

```zexus
# Lists
let numbers = [1, 2, 3, 4, 5]
let mixed = ["text", 42, true]

# Maps/Dictionaries
let user = {
    "name": "Alice",
    "age": 30,
    "email": "alice@example.com"
}

# Accessing elements
let first = numbers[0]
let name = user["name"]
let age = user.get("age", 0)  # With default value
```

## Functions (Actions)

### Basic Action Definition

```zexus
action greet(name: string) {
    print("Hello, " + name + "!")
}

action add(a: integer, b: integer) -> integer {
    return a + b
}
```

### Async Actions

```zexus
action async fetch_data() {
    # Async operation
    await some_async_call()
}

action async main() {
    let result = await fetch_data()
    print("Result: " + result)
}
```

### Actions with Return Types

```zexus
action calculate_sum(numbers: list) -> integer {
    let total = 0
    for each num in numbers {
        total += num
    }
    return total
}

action is_valid(value: integer) -> boolean {
    return value > 0
}
```

## Control Flow

### If-Else Statements

```zexus
if condition {
    # code block
}

if condition {
    # true block
} else {
    # false block
}

# Not operator
if not is_valid {
    print("Invalid")
}

# Comparison operators
if amount > 100 {
    print("Large amount")
} else if amount > 50 {
    print("Medium amount")
} else {
    print("Small amount")
}
```

### Loops

```zexus
# For-each loop (arrays)
for each item in items {
    print(item)
}

# For-each loop (maps)
for each key, value in dictionary {
    print(key + ": " + value)
}

# While loop (implied from patterns)
let counter = 0
while counter < 10 {
    counter += 1
}
```

## Contracts

### Basic Contract Structure

```zexus
contract ContractName {
    # Contract metadata
    name: "ContractName"
    version: "1.0.0"
    
    # Storage variables
    persistent storage owner: string
    persistent storage balance: integer
    persistent storage data: map
    
    # Initialization
    action init(param: string) {
        this.owner = param
        this.balance = 0
    }
    
    # Contract methods
    action do_something() -> boolean {
        # Contract logic
        return true
    }
}
```

### Contract Storage

```zexus
# Persistent storage (survives between calls)
persistent storage variable_name: type
persistent storage counter: integer = 0  # With default value

# Accessing storage
this.variable_name = value
let value = this.variable_name
```

### Nested Contracts

Zexus supports nested contract definitions for organizing related data structures:

```zexus
contract ParentContract {
    persistent storage users: map
    
    # Nested contract acts like an entity but with persistent storage
    contract UserScore {
        persistent storage address: string
        persistent storage score: float = 0.0
        persistent storage tier: string = "BRONZE"
        persistent storage last_updated: integer
        
        action init(address: string) {
            this.address = address
            this.last_updated = datetime.now().timestamp()
        }
        
        action update_score(new_score: float) {
            this.score = new_score
            this.last_updated = datetime.now().timestamp()
        }
    }
    
    action init() {
        this.users = {}
    }
    
    action create_user(address: string) {
        let user = UserScore{address: address}
        this.users[address] = user
    }
}
```

## Protocols and Interfaces

### Protocol Definition

```zexus
protocol ProtocolName {
    action method_name(param: type) -> return_type
    action another_method(param1: type1, param2: type2) -> return_type
}
```

### Implementing Protocols

```zexus
contract MyContract implements ProtocolName {
    # Must implement all protocol methods
    action method_name(param: type) -> return_type {
        # implementation
    }
}
```

## Entities (Data Structures)

### Entity Definition

```zexus
entity User {
    name: string
    email: string
    age: integer
    active: boolean = true  # Default value
    metadata: map?  # Optional field (nullable)
}
```

### Creating Entity Instances

```zexus
let user = User{
    name: "Alice",
    email: "alice@example.com",
    age: 30
}

# Accessing entity fields
let name = user.name
user.active = false
```

## Storage

### Persistent Storage

```zexus
contract Example {
    # Different storage types
    persistent storage single_value: string
    persistent storage number_value: integer = 0
    persistent storage collection: list
    persistent storage mapping: map
    
    action update_storage() {
        this.single_value = "updated"
        this.number_value += 1
        this.collection.push("item")
        this.mapping["key"] = "value"
    }
}
```

## Module System

### Importing Modules

```zexus
# Import with alias
use "crypto" as crypto
use "datetime" as datetime
use "math" as math

# Import from relative path
use "../core/block.zx" as block
use "./network.zx" as network

# Import specific items
use { JSONRPCServer, JSONRPCProtocol } from "./rpc/server.zx"
use { WebSocketRPCServer } from "./rpc/websocket.zx"
```

### Using Imported Modules

```zexus
# Calling functions from imported modules
let hash = crypto.hash(data)
let timestamp = datetime.now().timestamp()
let random = math.random_int(0, 100)
```

## Advanced Features

### Error Handling

```zexus
# Throw exceptions
if error_condition {
    throw "Error message"
}

# Try-catch (implied from patterns)
if not validate_input(data) {
    print("❌ Validation failed")
    return false
}
```

### String Operations

```zexus
# Concatenation
let full_name = first_name + " " + last_name

# String interpolation
print("Value: " + string(number))
print("Balance: " + string(balance) + " tokens")

# Length
let length = len(text)
```

### Collection Operations

```zexus
# List operations
list.push(item)           # Add to end
list.pop()                # Remove from end
list.contains(item)       # Check if contains
len(list)                 # Get length
list[index]               # Access by index
list.is_empty()          # Check if empty

# Map operations
map["key"] = value        # Set value
map.get("key", default)   # Get with default
map.has("key")           # Check if key exists
map.is_empty()           # Check if empty

# Iteration
for each item in list {
    # process item
}

for each key, value in map {
    # process key-value pair
}
```

### Type Conversion

```zexus
# Convert to string
let text = string(number)
let text = string(datetime.now().timestamp())

# Convert to integer
let num = integer(text)
let num = integer(float_value)
```

### Mathematical Operations

```zexus
# Basic operators
let sum = a + b
let diff = a - b
let product = a * b
let quotient = a / b

# Comparison
a > b    # Greater than
a < b    # Less than
a >= b   # Greater than or equal
a <= b   # Less than or equal
a == b   # Equal
a != b   # Not equal

# Increment/Decrement
counter += 1
counter -= 1
value *= 2
```

### Conditional Operators

```zexus
# Boolean operators
if condition1 and condition2 {
    # Both true
}

if condition1 or condition2 {
    # At least one true
}

if not condition {
    # Negation
}
```

### Special Syntax Features

```zexus
# Optional types (nullable)
let optional_value: string?
let optional_number: integer?

# Object member access
object.property
object.method()
this.field  # In contracts

# Chained calls
result = object.method1().method2()

# Null-safe access (implied)
if value != null {
    use_value(value)
}
```

### Database Integration

```zexus
database Users {
    entity User {
        name: string
        email: string
        created: datetime
    }
}
```

### AI Integration

```zexus
use zenith_ai from "../zenith-ai/core/inference.zx"

action analyze_data(data: list) {
    let result = zenith_ai.analyze(data)
    print("Analysis: " + result)
}
```

## Common Patterns

### Constructor Pattern

```zexus
contract MyContract {
    persistent storage initialized: boolean = false
    
    action init() {
        if this.initialized {
            throw "Already initialized"
        }
        # initialization logic
        this.initialized = true
    }
}
```

### Validation Pattern

```zexus
action validate_and_process(data: any) -> boolean {
    # Validate
    if not is_valid_data(data) {
        print("❌ Invalid data")
        return false
    }
    
    # Process
    process_data(data)
    print("✅ Success")
    return true
}
```

### Iterator Pattern

```zexus
action process_all_items(items: list) {
    for each item in items {
        if should_process(item) {
            process_item(item)
        }
    }
}
```

## Conventions and Best Practices

1. **Naming Conventions**:
   - Use `snake_case` for variables and function names
   - Use `PascalCase` for contracts, entities, and protocols
   - Use `UPPER_CASE` for constants

2. **Comments**:
   - Use `#` for single-line comments
   - Use `"""` for docstrings and multi-line comments

3. **Error Messages**:
   - Use emoji prefixes for visual clarity (❌ for errors, ✅ for success, 💰 for transactions, etc.)

4. **Return Values**:
   - Explicitly declare return types with `-> type`
   - Return boolean for success/failure operations

5. **Storage**:
   - Always use `persistent storage` for contract state
   - Initialize with default values when possible

## Examples from Ziver-Chain

### Complete Wallet Contract Example

```zexus
contract ZiverWallet {
    name: "ZiverWallet"
    version: "1.0.0"
    
    persistent storage owner: string
    persistent storage balances: map
    
    action init(initial_owner: string) {
        this.owner = initial_owner
        this.balances = {
            "ZIVER": 0,
            "ETHEREUM": 0
        }
    }
    
    action deposit(chain: string, amount: integer) -> boolean {
        if amount <= 0 {
            return false
        }
        this.balances[chain] = this.balances.get(chain, 0) + amount
        return true
    }
}
```

### Complete Node Script Example

```zexus
use "../src/main.zx" as ziver

action async main() {
    let node = ziver.get_ziver_node()
    
    if node.initialize() {
        print("🚀 Node initialized")
        
        if node.start_full_node(3030) {
            print("✅ Node started on port 3030")
        }
    }
}
```

## Additional Resources

- **Main README**: `/README.md` - Overview of Ziver Chain
- **Zexus README**: `/zexus/README.md` - Zexus language introduction
- **Example Scripts**: `/scripts/*.zx` - Working examples
- **Test Files**: `/tests/**/*.zx` - Test examples
- **Source Code**: `/src/**/*.zx` - Implementation examples

## Note on Zexus-interpreter Repository

If you have a `Zexus-interpreter` repository that contains the formal language specification, parser, and interpreter implementation, please consider:

1. **Making it public** so it can be referenced
2. **Adding it as a submodule** to this repository
3. **Linking to it** in the documentation
4. **Contributing examples** from it to this reference

This would provide the complete picture of the Zexus language and enable better development on the Ziver-Chain platform.

## Contributing

If you find syntax patterns not documented here, please:
1. Add them to this reference
2. Include examples from the codebase
3. Update the table of contents

---

**Last Updated**: December 2025  
**Version**: 1.0.0  
**Based on**: Ziver-Chain repository code analysis
