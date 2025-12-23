# Zexus Quick Reference Card

A one-page reference for the most commonly used Zexus features.

## 📝 Variables & Constants

```zexus
let name = "Alice"           # Mutable variable
const PI = 3.14159           # Immutable constant
let age: integer = 25        # With type annotation
```

## 🔧 Functions

```zexus
# Named function
action greet(name: string) -> string {
    return "Hello, " + name
}

# Lambda (anonymous)
let add = lambda(a, b) { return a + b }

# Async function
async action fetchData() -> string {
    let data = await apiCall()
    return data
}
```

## 🎯 Control Flow

```zexus
# If/else
if (condition) {
    # code
} elif (other) {
    # code
} else {
    # code
}

# While loop
while (x < 10) {
    x = x + 1
}

# For loop
for (item in list) {
    print(item)
}

# Pattern matching
match value {
    case 1: result = "one"
    case 2: result = "two"
    default: result = "other"
}
```

## 📦 Data Types

```zexus
# Collections
let numbers: list = [1, 2, 3, 4, 5]
let config: map = {"debug": true, "port": 8080}
let unique: set = {1, 2, 3}

# Entity (struct)
entity User {
    name: string,
    email: string,
    age: integer = 0
}

let user = User.create({
    name: "Alice",
    email: "alice@example.com"
})
```

## ⛓️ Smart Contracts

```zexus
contract Token {
    persistent storage balances: Map<Address, integer>
    
    action transfer(to: Address, amount: integer) {
        require(balances[msg.sender] >= amount, "Insufficient")
        balances[msg.sender] = balances[msg.sender] - amount
        balances[to] = balances.get(to, 0) + amount
        emit Transfer(msg.sender, to, amount)
    }
}

let token = Token.deploy({balances: {}})
```

## 🔐 Security

```zexus
# Access control
export myFunction to "allowed.zx" with "read_write"

# Runtime verification
verify(user.authenticated) {
    log_error("Unauthorized")
    block_request()
}

# Security rules
protect(sensitiveFunc, {
    rate_limit: 100,
    auth_required: true,
    require_https: true
}, "strict")

# Data validation
restrict(amount, {
    range: [0, 10000],
    type: "integer"
})
```

## 👀 Reactive State

```zexus
let count = 0
watch count {
    print("Count changed to: " + string(count))
}
count = 5  # Triggers watch callback
```

## 🔌 Dependency Injection

```zexus
# Register
register_dependency("db", ProductionDB())

# Inject
inject db

# Mock for testing
test_mode(true)
mock_dependency("db", MockDB())
```

## 📤 Module System

```zexus
# Import
import "module_name"
import {func1, func2} from "module"

# Export
export myFunction
export {func1, func2}
```

## 🛠️ Common Built-ins

```zexus
# I/O
print("Hello")              # No newline
println("Hello")            # With newline
let input = input("Name: ") # User input

# Type conversion
string(42)                  # "42"
int("42")                   # 42
float("3.14")               # 3.14

# Collections
len(list)                   # Length
push(list, item)            # Add to list
filter(list, predicate)     # Filter items
map(list, transformer)      # Transform items
reduce(list, reducer, 0)    # Reduce to value

# Strings
join(["a", "b"], ",")       # "a,b"
split("a,b", ",")           # ["a", "b"]
uppercase("hello")          # "HELLO"
lowercase("HELLO")          # "hello"

# Math
abs(-5)                     # 5
min(3, 5)                   # 3
max(3, 5)                   # 5
sqrt(16)                    # 4
pow(2, 3)                   # 8

# Blockchain
balance(address)            # Get balance
transfer(to, amount)        # Transfer tokens
emit(event)                 # Emit event
require(condition, "msg")   # Require condition
```

## ⚡ Performance

```zexus
# Inline optimization
inline action fastCalc(x: integer) -> integer {
    return x * 2
}

# Deferred cleanup
defer {
    closeConnection()
    freeResources()
}

# Memory tracking
track_memory()
```

## 🔄 Async/Await

```zexus
async action fetchUser(id: string) -> User {
    let data = await database.query(id)
    return User.create(data)
}

let user = await fetchUser("123")
```

## 💾 Persistent Storage

```zexus
# Store data
persist_set("user_prefs", preferences)

# Retrieve data
let prefs = persist_get("user_prefs")

# Clear storage
persist_clear("user_prefs")
```

## 🔗 Pipeline Operator

```zexus
let result = data
    |> transform1
    |> transform2
    |> transform3
```

## 🎨 String Interpolation

```zexus
let name = "Alice"
let age = 25
print("Name: ${name}, Age: ${age}")
```

## 🚨 Error Handling

```zexus
try {
    riskyOperation()
} catch (error) {
    print("Error: " + error.message)
}
```

## 📊 Common Patterns

### Secure API Endpoint
```zexus
action processPayment(user: User, amount: integer) -> boolean {
    # Implementation
}

protect(processPayment, {
    rate_limit: 100,
    auth_required: true,
    require_https: true
}, "strict")

verify(processPayment, [
    action() { return user.authenticated }
    action() { return amount > 0 }
    action() { return amount <= user.balance }
])
```

### Smart Contract Pattern
```zexus
contract Vault {
    persistent storage owner: Address
    persistent storage balance: integer = 0
    
    action deposit(amount: integer) {
        require(amount > 0, "Amount must be positive")
        balance = balance + amount
        emit Deposit(msg.sender, amount)
    }
    
    action withdraw(amount: integer) {
        require(msg.sender == owner, "Not owner")
        require(balance >= amount, "Insufficient funds")
        balance = balance - amount
        emit Withdrawal(msg.sender, amount)
    }
}
```

### Reactive State Pattern
```zexus
let cart = []
let total = 0

watch cart {
    total = reduce(cart, lambda(sum, item) { 
        return sum + item.price 
    }, 0)
    print("Total updated: " + string(total))
}

push(cart, {name: "Book", price: 15})  # Triggers update
```

---

## 🔑 Key Concepts

| Concept | Syntax | Use Case |
|---------|--------|----------|
| **Variables** | `let x = 5` | Mutable data |
| **Constants** | `const X = 5` | Immutable data |
| **Functions** | `action f() {}` | Named functions |
| **Entities** | `entity T {}` | Structured data |
| **Contracts** | `contract C {}` | Smart contracts |
| **Security** | `protect()`, `verify()` | Access control |
| **Reactive** | `watch x {}` | State reactions |
| **Async** | `async`/`await` | Concurrency |
| **Modules** | `import`, `export` | Code organization |

---

## 📚 Where to Learn More

- **Full docs**: [00_START_HERE.md](00_START_HERE.md)
- **Language ref**: [LANGUAGE_REFERENCE.md](LANGUAGE_REFERENCE.md)
- **Examples**: [examples/](examples/) directory
- **Quick start**: [QUICK_START.md](QUICK_START.md)

---

**Keep this card handy for quick reference while coding in Zexus!**
