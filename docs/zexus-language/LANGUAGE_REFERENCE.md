# Zexus Language Reference - Complete Feature & Keyword List

This document provides a comprehensive list of all Zexus language features, keywords, commands, and built-in functions.

## 📋 Table of Contents

1. [Core Keywords](#core-keywords)
2. [Data Types](#data-types)
3. [Control Flow](#control-flow)
4. [Functions & Actions](#functions--actions)
5. [Entities & Contracts](#entities--contracts)
6. [Security Features](#security-features)
7. [Blockchain Features](#blockchain-features)
8. [Performance Features](#performance-features)
9. [Advanced Features](#advanced-features)
10. [Built-in Functions](#built-in-functions)
11. [Operators](#operators)
12. [Module System](#module-system)
13. [Concurrency](#concurrency)

---

## Core Keywords

### Variable Declarations
- `let` - Mutable variable declaration
- `const` - Immutable constant declaration
- `var` - Alternative variable declaration

### Basic Types
- `integer` / `int` - Integer numbers
- `float` - Floating-point numbers
- `string` - Text strings
- `boolean` / `bool` - True/false values
- `list` - Ordered collections
- `map` - Key-value pairs
- `set` - Unique value collections
- `null` - Null/undefined value

---

## Data Types

### Primitive Types
```zexus
let num: integer = 42
let pi: float = 3.14159
let name: string = "Zexus"
let active: boolean = true
```

### Collection Types
```zexus
let numbers: list = [1, 2, 3, 4, 5]
let config: map = {"debug": true, "port": 8080}
let unique_ids: set = {1, 2, 3}
```

### Complex Types
- `entity` - Structured data types (like structs)
- `contract` - Blockchain smart contracts
- `enum` - Enumerations
- `Address` - Blockchain addresses
- `protocol` - Interface definitions

---

## Control Flow

### Conditionals
```zexus
if (condition) {
    # code
}

elif (other_condition) {
    # code
}

else {
    # code
}
```

### Loops
```zexus
# While loop
while (condition) {
    # code
}

# For loop
for (item in collection) {
    # code
}

# Each loop
each (item in collection) {
    # code
}
```

### Pattern Matching
```zexus
match value {
    case 1: result = "one"
    case 2: result = "two"
    default: result = "other"
}
```

### Error Handling
```zexus
try {
    # code that might fail
} catch (error) {
    # handle error
}
```

---

## Functions & Actions

### Action (Named Function)
```zexus
action greet(name: string) -> string {
    return "Hello, " + name
}
```

### Lambda (Anonymous Function)
```zexus
let add = lambda(a, b) { return a + b }
```

### Function Modifiers
- `async` - Asynchronous function
- `inline` - Hint for inline optimization
- `native` - Native code integration

---

## Entities & Contracts

### Entity Definition
```zexus
entity User {
    id: string,
    name: string,
    email: string,
    age: integer = 0,
    active: boolean = true
}

# Create instance
let user = User.create({
    id: "123",
    name: "Alice",
    email: "alice@example.com"
})
```

### Contract Definition
```zexus
contract BankAccount {
    persistent storage owner: User
    persistent storage balance: integer = 0
    
    action deposit(amount: integer) -> boolean {
        balance = balance + amount
        return true
    }
    
    action withdraw(amount: integer) -> boolean {
        if (balance >= amount) {
            balance = balance - amount
            return true
        }
        return false
    }
}

# Deploy contract
let account = BankAccount.deploy({
    owner: user,
    balance: 1000
})
```

---

## Security Features

### Access Control
```zexus
# Export with restrictions
export function_name to "allowed_file.zx" with "read_write"

# Restrict data
restrict(amount, {
    range: [0, 10000],
    type: "integer"
})
```

### Runtime Verification
```zexus
# Verify with conditions
verify(condition) {
    log_error("Verification failed")
    block_request()
}

# Verify with validators
verify(function_name, [
    validator1,
    validator2
])

# Database verification
verify:db userId exists_in "users", "User not found"

# Environment verification
verify:env "API_KEY" is_set, "API_KEY required"
```

### Protection Rules
```zexus
protect(sensitive_function, {
    rate_limit: 100,
    auth_required: true,
    require_https: true,
    min_password_strength: "strong",
    session_timeout: 3600,
    allowed_ips: ["10.0.0.0/8"],
    blocked_ips: []
}, "strict")
```

### Authentication
```zexus
auth {
    provider: "oauth2",
    scopes: ["read", "write"],
    token_expiry: 3600,
    mfa_required: true,
    session_timeout: 1800
}
```

### Middleware
```zexus
middleware(handler_name, action(request, response) {
    # Process request
    return true  # Continue chain
})
```

### Rate Limiting
```zexus
throttle(api_function, {
    requests_per_minute: 100,
    burst_size: 10,
    per_user: true
})
```

### Caching
```zexus
cache(expensive_function, {
    ttl: 300,
    invalidate_on: ["data_changed"]
})
```

### Audit Logging
```zexus
audit {
    enabled: true,
    log_level: "info",
    destination: "audit.log"
}
```

### Sandboxing
```zexus
sandbox(untrusted_code, {
    max_memory: 1024,
    max_cpu_time: 5000,
    allow_network: false
})
```

---

## Blockchain Features

### Transactions
```zexus
transaction {
    from: sender_address,
    to: receiver_address,
    amount: 100,
    data: {}
}
```

### Events
```zexus
# Emit event
emit Transfer(from, to, amount)

# Listen for event
on Transfer(from, to, amount) {
    print("Transfer: " + amount)
}
```

### Requirements
```zexus
require(condition, "Error message")
assert(condition, "Assertion failed")
```

### Blockchain Functions
- `balance(address)` - Get address balance
- `transfer(to, amount)` - Transfer tokens
- `emit(event)` - Emit event
- `now()` - Current timestamp
- `block_number()` - Current block
- `msg.sender` - Transaction sender
- `msg.value` - Transaction value

### Storage Modifiers
- `persistent storage` - Cross-session storage
- `temporary storage` - Session-only storage

---

## Performance Features

### Native Code Integration
```zexus
native {
    language: "C",
    code: "/* C code here */",
    function: "native_function"
}
```

### Inline Optimization
```zexus
inline action fast_math(x: integer) -> integer {
    return x * 2
}
```

### SIMD Operations
```zexus
simd {
    operation: "vector_add",
    data: array_data,
    width: 4
}
```

### Garbage Collection Control
```zexus
gc {
    mode: "manual",
    collect_now: true
}
```

### Buffer Management
```zexus
buffer {
    size: 1024,
    type: "circular",
    data: input_data
}
```

### Deferred Execution
```zexus
defer {
    # Cleanup code - runs at scope exit
    close_connection()
    release_resources()
}
```

### Memory Tracking
```zexus
track_memory()  # Enable memory leak detection
```

---

## Advanced Features

### Reactive State (Watch)
```zexus
watch variable {
    print("Variable changed to: " + string(variable))
}
```

### Dependency Injection
```zexus
# Register dependency
register_dependency("service_name", ServiceImpl())

# Inject dependency
inject service_name

# Mock for testing
test_mode(true)
mock_dependency("service_name", MockService())
```

### Persistent Memory
```zexus
# Store data
persist_set("key", value)

# Retrieve data
let value = persist_get("key")

# Clear storage
persist_clear("key")
```

### Stream Processing
```zexus
stream {
    source: data_source,
    transform: transformer,
    sink: output
}
```

### Enumerations
```zexus
enum Status {
    PENDING,
    APPROVED,
    REJECTED
}

let status = Status.PENDING
```

### Protocol (Interface)
```zexus
protocol Serializable {
    action serialize() -> string
    action deserialize(data: string) -> object
}
```

---

## Built-in Functions

### I/O Functions
- `print(value)` - Print without newline
- `println(value)` - Print with newline
- `input(prompt)` - Read user input
- `read_file(path)` - Read file contents
- `write_file(path, data)` - Write to file

### Type Conversion
- `string(value)` - Convert to string
- `int(value)` - Convert to integer
- `float(value)` - Convert to float
- `bool(value)` - Convert to boolean

### Collection Functions
- `len(collection)` - Get length
- `list(items)` - Create list
- `map(items)` - Create map
- `set(items)` - Create set
- `range(start, end)` - Create range
- `push(list, item)` - Add to list
- `pop(list)` - Remove from list
- `get(map, key, default)` - Get map value
- `keys(map)` - Get map keys
- `values(map)` - Get map values

### Functional Programming
- `filter(collection, predicate)` - Filter items
- `map(collection, transformer)` - Transform items
- `reduce(collection, reducer, initial)` - Reduce to single value
- `sort(collection)` - Sort collection
- `reverse(collection)` - Reverse collection

### String Functions
- `join(list, separator)` - Join strings
- `split(string, delimiter)` - Split string
- `replace(string, old, new)` - Replace substring
- `uppercase(string)` - Convert to uppercase
- `lowercase(string)` - Convert to lowercase
- `trim(string)` - Remove whitespace
- `substring(string, start, end)` - Extract substring
- `starts_with(string, prefix)` - Check prefix
- `ends_with(string, suffix)` - Check suffix
- `contains(string, substring)` - Check contains

### Math Functions
- `abs(number)` - Absolute value
- `ceil(number)` - Round up
- `floor(number)` - Round down
- `round(number)` - Round to nearest
- `min(a, b)` - Minimum value
- `max(a, b)` - Maximum value
- `sum(list)` - Sum of values
- `sqrt(number)` - Square root
- `pow(base, exponent)` - Power
- `random()` - Random number 0-1

### Time Functions
- `now()` - Current timestamp
- `sleep(seconds)` - Pause execution
- `date()` - Current date
- `time()` - Current time

### Security Functions
- `hash(data)` - Hash data
- `encrypt(data, key)` - Encrypt data
- `decrypt(data, key)` - Decrypt data
- `verify_signature(data, signature, key)` - Verify signature

### Memory Functions
- `persist_set(key, value)` - Store persistent data
- `persist_get(key)` - Retrieve persistent data
- `persist_clear(key)` - Clear persistent data
- `track_memory()` - Enable memory tracking

### Policy Functions
- `create_policy(name, rules)` - Create security policy
- `enforce_policy(policy, data)` - Enforce policy
- `verify_condition(condition)` - Verify condition

### Dependency Injection
- `register_dependency(name, impl)` - Register service
- `inject_dependency(name)` - Inject service
- `mock_dependency(name, mock)` - Mock service
- `test_mode(enabled)` - Enable test mode

### Blockchain Functions
- `transaction(params)` - Create transaction
- `emit(event)` - Emit blockchain event
- `require(condition, message)` - Require condition
- `assert(condition, message)` - Assert condition
- `balance(address)` - Get balance
- `transfer(to, amount)` - Transfer value

---

## Operators

### Arithmetic Operators
- `+` - Addition
- `-` - Subtraction
- `*` - Multiplication
- `/` - Division
- `%` - Modulo
- `**` - Exponentiation

### Comparison Operators
- `==` - Equal to
- `!=` - Not equal to
- `<` - Less than
- `>` - Greater than
- `<=` - Less than or equal
- `>=` - Greater than or equal

### Logical Operators
- `&&` / `and` - Logical AND
- `||` / `or` - Logical OR
- `!` / `not` - Logical NOT

### Assignment Operators
- `=` - Assignment
- `+=` - Add and assign
- `-=` - Subtract and assign
- `*=` - Multiply and assign
- `/=` - Divide and assign

### Pipeline Operator
- `|>` - Pipe value to function
  ```zexus
  value |> transform1 |> transform2
  ```

### Spread Operator
- `...` - Spread collection items
  ```zexus
  let combined = [...list1, ...list2]
  ```

---

## Module System

### Import
```zexus
import "module_name"
import "path/to/module.zx"
import {function1, function2} from "module"
```

### Export
```zexus
export function_name
export {function1, function2}
export function_name to "allowed_file.zx" with "read_write"
```

### Access Levels
- `public` - Accessible everywhere
- `private` - Internal only
- `protected` - Module and submodules
- Custom file-level access with `export ... to`

---

## Concurrency

### Async/Await
```zexus
async action fetch_data() -> string {
    let data = await api_call()
    return data
}

# Call async function
let result = await fetch_data()
```

### Spawn
```zexus
spawn {
    # Concurrent task
}
```

### Coroutines
```zexus
coroutine worker() {
    while (true) {
        yield process_item()
    }
}
```

### Channels
```zexus
let channel = create_channel()
send(channel, data)
let received = receive(channel)
```

---

## Special Features

### Comments
```zexus
# Single line comment
// Alternative single line

/* 
   Multi-line
   comment
*/
```

### String Interpolation
```zexus
let name = "World"
print("Hello, ${name}!")
```

### Destructuring
```zexus
let {name, age} = user
let [first, second] = numbers
```

### Default Parameters
```zexus
action greet(name: string = "World") {
    print("Hello, " + name)
}
```

### Rest Parameters
```zexus
action sum(...numbers) {
    return reduce(numbers, lambda(a, b) { return a + b }, 0)
}
```

---

## Command Reference Summary

All commands starting with `COMMAND_`:
- `audit` - Audit logging and compliance
- `buffer` - Buffer management
- `const` - Constant declarations
- `defer` - Deferred execution
- `elif` - Else-if conditions
- `enum` - Enumeration types
- `gc` - Garbage collection
- `inline` - Inline optimization
- `native` - Native code integration
- `pattern` - Pattern matching
- `restrict` - Access restrictions
- `sandbox` - Sandboxed execution
- `simd` - SIMD vectorization
- `stream` - Stream processing
- `trail` - Execution tracing
- `watch` - Reactive state

---

## Documentation Coverage

This language reference covers:
✅ All core keywords and syntax
✅ All data types (primitive, collection, complex)
✅ All control flow structures
✅ All function types and modifiers
✅ Security features (verify, protect, restrict, audit, etc.)
✅ Blockchain features (contracts, transactions, events)
✅ Performance features (native, inline, SIMD, gc, buffer)
✅ Advanced features (watch, DI, persistent memory, streams)
✅ 70+ built-in functions
✅ All operators
✅ Module system
✅ Concurrency primitives
✅ Special language features

**Total Features Documented**: 100+ keywords, commands, and features

---

## Additional Resources

For detailed documentation on each feature, see:
- [00_START_HERE.md](00_START_HERE.md) - Complete navigation guide
- [ZEXUS_README.md](ZEXUS_README.md) - Main overview
- [QUICK_START.md](QUICK_START.md) - Quick start guide
- Individual keyword files in [keywords/](keywords/)
- Individual command files (COMMAND_*.md)
- Feature guides in [features/](features/)

---

**This is a complete reference covering all documented Zexus language features.**
