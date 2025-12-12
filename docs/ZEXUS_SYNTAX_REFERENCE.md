# Zexus Language Syntax Reference

This document provides a comprehensive reference for the Zexus programming language syntax, compiled from both the Ziver-Chain codebase and the official Zexus-interpreter repository.

## About Zexus-interpreter Repository

**✅ Official Interpreter**: The complete Zexus language specification and interpreter implementation is now publicly available at:

🔗 **https://github.com/Zaidux/zexus-interpreter**

This reference combines:
- Syntax patterns from Ziver-Chain's `.zx` files
- Official documentation from the Zexus-interpreter repository
- Security features and advanced syntax from the 10-phase implementation
- Best practices from both codebases

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
10. [Security Features](#security-features)
11. [Advanced Features](#advanced-features)
12. [Modifiers](#modifiers)

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

# Accessing and modifying entity fields
let name = user.name
user.active = false  # Entities are mutable
user.age += 1        # Can modify fields directly
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

## Security Features

Zexus provides comprehensive security features for protecting code execution, enforcing access control, and validating operations.

### Entity Types

Define type-safe data structures:

```zexus
entity User {
    username: string,
    email: string,
    role: string = "user",
    is_active: boolean = true
}

let john = User.create({
    username: "john",
    email: "john@example.com",
    role: "admin"
})

print(john.username)  # Output: "john"
```

### Export (Access Control)

Control which files can access functions:

```zexus
# Grant read-only access
export function_name to "other_file.zx" with "read_only"

# Grant read-write access
export contract_name to "client.zx" with "read_write"

# Deny all access
export sensitive_function to "untrusted.zx" with "deny"
```

### Verify (Runtime Checks)

Add runtime validation to functions:

```zexus
action transfer_money(to_user: User, amount: integer) -> boolean {
    print("Transferring " + string(amount) + " to " + to_user.username)
    return true
}

# Add verification checks
verify(transfer_money, [
    action() { return balance >= amount },      # Check balance
    action() { return to_user != null },        # User exists
    action() { return amount > 0 }             # Valid amount
])
```

### Protect (Security Rules)

Enforce security policies on functions:

```zexus
protect(api_function, {
    rate_limit: 100,                # Max 100 calls per minute
    auth_required: true,            # Must be authenticated
    require_https: true,            # Only over HTTPS
    min_password_strength: "strong",
    session_timeout: 3600,          # 1 hour timeout
    allowed_ips: ["10.0.0.0/8"],
    blocked_ips: []
})
```

### Auth (Authentication)

Configure global authentication:

```zexus
auth {
    provider: "oauth2",
    scopes: ["read", "write", "delete"],
    token_expiry: 3600,
    mfa_required: true,
    session_timeout: 1800
}
```

### Middleware (Request Processing)

Add request/response middleware:

```zexus
middleware(log_requests, action(request, response) {
    print("Incoming: " + request.method + " " + request.path)
    return true  # Continue to next handler
})

middleware(check_token, action(request, response) {
    let token = request.headers["Authorization"]
    if token == null {
        response.status = 401
        return false  # Stop chain
    }
    return true
})
```

### Throttle (Rate Limiting)

Prevent abuse with rate limiting:

```zexus
throttle(api_endpoint, {
    requests_per_minute: 100,
    burst_size: 10,
    per_user: true
})
```

### Cache (Performance)

Cache expensive operations:

```zexus
cache(expensive_query, {
    ttl: 300,                      # Cache for 5 minutes
    invalidate_on: ["data_changed"]
})
```

### Security Layers

Zexus security works in layers:

1. **File Level** (`export`) - Who can access
2. **Runtime** (`verify`) - If they should be allowed
3. **Rules** (`protect`) - Enforce security policies
4. **Process** (`middleware`) - Process & validate requests
5. **Auth** (`auth`) - Handle authentication
6. **Throttle** (`throttle`) - Prevent abuse
7. **Cache** (`cache`) - Optimize performance
8. **State** (`contract`) - Persistent storage
9. **Types** (`entity`) - Type safety

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

## Modifiers

Zexus uses modifiers to annotate functions and types with semantic meaning and behavioral hints.

### Access Modifiers

```zexus
@public
action visible_function() {
    # Accessible from anywhere
}

@private
action _internal_helper() {
    # Private by convention - enforced through tools
}

@sealed
action final_method() {
    # Cannot be overridden in subclasses
}
```

### Behavioral Modifiers

```zexus
@async
action async_operation() {
    # Runs asynchronously
}

@inline
action hot_loop() {
    # Inlined by compiler for performance
}

@native
action system_call() {
    # Calls native/system functionality
}
```

### Semantic Modifiers

```zexus
@secure
action handle_password() {
    # Runs in secure context with extra validation
}

@pure
action calculate(x: integer) -> integer {
    # Pure function - no side effects
    return x * 2
}
```

### Optimization Hints

```zexus
@optimize
action critical_path() {
    # Compiler applies aggressive optimization
}

@once
action singleton() {
    # Runs only once, subsequent calls return cached result
}
```

### Modifier Combinations

```zexus
@public @async @secure
action secure_api_call() {
    # Public, asynchronous, and secure
}

@private @inline @pure
action fast_helper(x: integer) -> integer {
    # Private, inlined, and pure
    return x + 1
}
```

## Additional Resources

### Ziver-Chain Resources
- **Main README**: `/README.md` - Overview of Ziver Chain
- **Zexus README**: `/zexus/README.md` - Zexus language introduction
- **Example Scripts**: `/scripts/*.zx` - Working examples
- **Test Files**: `/tests/**/*.zx` - Test examples
- **Source Code**: `/src/**/*.zx` - Implementation examples

### Official Zexus-interpreter Repository

✅ **Now Publicly Available**: https://github.com/Zaidux/zexus-interpreter

The official interpreter repository contains:
- **Complete language specification** - Formal syntax and semantics
- **Full interpreter implementation** - Python-based interpreter
- **10-phase feature system** - All advanced features documented
- **Comprehensive test suites** - 224+ test cases
- **Security documentation** - Capability system, sandboxing, etc.
- **Plugin system guide** - Extensibility framework
- **Performance optimization** - Bytecode compilation and optimization

#### Key Documentation Files in Interpreter:
- `STATUS.md` - Implementation status (all 10 phases complete)
- `PHILOSOPHY.md` - Design principles and architecture
- `docs/QUICK_START.md` - 5-minute getting started guide
- `docs/SECURITY_FEATURES.md` - Complete security guide
- `docs/PLUGIN_SYSTEM.md` - Plugin development guide
- `docs/ARCHITECTURE.md` - System architecture details

## Contributing

If you find syntax patterns not documented here, please:
1. Add them to this reference
2. Include examples from the codebase
3. Update the table of contents

---

**Last Updated**: December 2025  
**Version**: 2.0.0  
**Based on**: 
- Ziver-Chain repository code analysis (6,801 lines of Zexus code)
- Official Zexus-interpreter repository (https://github.com/Zaidux/zexus-interpreter)
- 10-phase implementation documentation
