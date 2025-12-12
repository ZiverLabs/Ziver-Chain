# Learning Zexus: A Comprehensive Guide for AI and Developers

## What is Zexus?

**Zexus** is a modern, blockchain-oriented programming language designed for building secure, scalable decentralized applications. The name "Zexus" represents the connection (nexus) at the foundation (Z) of blockchain systems.

### Core Philosophy

1. **Security First**: Default-deny security model with capability-based access control
2. **Blockchain Native**: Built-in smart contract primitives and persistent storage
3. **Multi-Chain**: Native support for cross-chain operations (Ethereum, BSC, TON, Polygon)
4. **Developer Friendly**: Clear syntax inspired by Python, JavaScript, and Solidity
5. **Performance Optimized**: Dual execution modes (interpreter + bytecode compiler)

### What Makes Zexus Unique?

- **Persistent Storage**: Built-in `persistent storage` for blockchain state
- **Multi-Chain Wallet**: Native multi-chain asset management
- **Event-Driven**: Real async/await with event emitters
- **Security Layers**: 9 layers of security (export, verify, protect, auth, middleware, etc.)
- **Smart Contract Ready**: Purpose-built for DeFi, NFTs, DAOs, and more

---

## Language Fundamentals

### 1. Variables and Data Types

Zexus is **dynamically typed** with optional type annotations.

```zexus
# Basic types
let name = "Alice"                    # String
let age = 25                          # Integer
let price = 99.99                     # Float
let active = true                     # Boolean
let data = null                       # Null

# Collections
let numbers = [1, 2, 3, 4, 5]        # List
let user = {                          # Map (Object)
    name: "Bob",
    email: "bob@example.com",
    age: 30
}

# Type annotations (optional)
let username: string = "charlie"
let count: integer = 100
let balance: float = 1000.50
let enabled: boolean = false
```

**Type Inference**: Zexus automatically infers types from values.

### 2. Functions (Actions)

Functions in Zexus are called **actions**.

```zexus
# Basic action
action greet(name) {
    print("Hello, " + name)
}

# Action with return type
action add(a: integer, b: integer) -> integer {
    return a + b
}

# Action with default parameters
action power(base, exponent = 2) {
    let result = base
    let i = 1
    while i < exponent {
        result = result * base
        i = i + 1
    }
    return result
}

# Function expression
let multiply = action(x, y) {
    return x * y
}

# Call actions
greet("Alice")                    # Output: Hello, Alice
let sum = add(5, 3)              # sum = 8
let squared = power(4)           # squared = 16
let product = multiply(6, 7)     # product = 42
```

### 3. Control Flow

```zexus
# If/Else
if temperature > 30 {
    print("Hot!")
} else if temperature < 10 {
    print("Cold!")
} else {
    print("Nice!")
}

# For loops
let numbers = [1, 2, 3, 4, 5]
for each num in numbers {
    print(num * 2)
}

# While loops
let count = 0
while count < 5 {
    print("Count: " + string(count))
    count = count + 1
}

# Try/Catch (error handling)
try {
    let result = risky_operation()
    print("Success: " + string(result))
} catch(error) {
    print("Error: " + string(error))
}
```

### 4. Comments

```zexus
# Single-line comment

// Also single-line (C-style)

"""
Multi-line comment
Documentation string
"""

action documented_function() {
    """
    This is a docstring explaining what the function does.
    Similar to Python docstrings.
    """
    return true
}
```

---

## Blockchain-Specific Features

### 1. Smart Contracts

The `contract` keyword defines blockchain smart contracts with persistent state.

```zexus
contract TokenContract {
    # Contract metadata
    name: "MyToken"
    version: "1.0.0"
    
    # Persistent storage (survives across calls)
    persistent storage balances: map
    persistent storage total_supply: integer
    persistent storage owner: string
    
    # Constructor
    action init(initial_owner: string, supply: integer) {
        this.owner = initial_owner
        this.total_supply = supply
        this.balances = {}
        this.balances[initial_owner] = supply
        
        print("💎 Token created with supply: " + string(supply))
    }
    
    # Transfer tokens
    action transfer(to: string, amount: integer) -> boolean {
        # Get sender balance
        let from_balance = this.balances.get(msg.sender, 0)
        
        # Validation
        if from_balance < amount {
            print("❌ Insufficient balance")
            return false
        }
        
        if amount <= 0 {
            print("❌ Invalid amount")
            return false
        }
        
        # Update balances
        this.balances[msg.sender] = from_balance - amount
        this.balances[to] = this.balances.get(to, 0) + amount
        
        print("✅ Transferred " + string(amount) + " tokens")
        return true
    }
    
    # Get balance
    action balance_of(address: string) -> integer {
        return this.balances.get(address, 0)
    }
}

# Deploy contract
let token = contract TokenContract()
token.init("0xAlice", 1000000)

# Use contract
token.transfer("0xBob", 100)
let bob_balance = token.balance_of("0xBob")
print("Bob's balance: " + string(bob_balance))  # Output: 100
```

**Key Contract Features:**
- `persistent storage`: State that persists across transactions
- `this`: Reference to contract instance
- `msg.sender`: Address of transaction sender (context variable)
- `block.timestamp`: Current block timestamp
- `block.number`: Current block number

### 2. Protocols (Interfaces)

Protocols define method contracts that contracts must implement.

```zexus
# Define protocol
protocol Wallet {
    action transfer(to: string, amount: integer) -> boolean
    action get_balance() -> integer
    action get_address() -> string
}

# Implement protocol
contract MyWallet implements Wallet {
    persistent storage balance: integer
    persistent storage address: string
    
    action init(addr: string) {
        this.address = addr
        this.balance = 0
    }
    
    # Must implement all protocol methods
    action transfer(to: string, amount: integer) -> boolean {
        if this.balance < amount {
            return false
        }
        this.balance = this.balance - amount
        return true
    }
    
    action get_balance() -> integer {
        return this.balance
    }
    
    action get_address() -> string {
        return this.address
    }
}
```

### 3. Entities (Type-Safe Data Structures)

Entities are typed data structures similar to classes/structs.

```zexus
# Define entity
entity User {
    username: string,
    email: string,
    age: integer = 18,          # Default value
    role: string = "user",      # Default value
    is_active: boolean = true
}

# Create instance
let alice = User{
    username: "alice",
    email: "alice@example.com",
    age: 25
}

# Access properties
print(alice.username)           # Output: alice
print(alice.role)               # Output: user (default)

# Update properties
alice.role = "admin"
alice.is_active = true

# Entity with optional types
entity Transaction {
    hash: string,
    from: string,
    to: string,
    amount: integer,
    data: string? = null,       # Optional (can be null)
    timestamp: integer
}

# Nested entities
entity Address {
    street: string,
    city: string,
    country: string
}

entity Person {
    name: string,
    address: Address            # Nested entity
}

let bob_addr = Address{ street: "123 Main St", city: "NYC", country: "USA" }
let bob = Person{ name: "Bob", address: bob_addr }
```

---

## Advanced Features

### 1. Async/Await (Concurrency)

Zexus supports true async/await for concurrent operations.

```zexus
# Define async action
action async fetch_price(symbol: string) {
    print("📊 Fetching price for " + symbol + "...")
    let data = await http_get("https://api.example.com/price/" + symbol)
    return parse_json(data)
}

# Async action with multiple awaits
action async process_transaction(tx) {
    try {
        # Send transaction
        let receipt = await send_to_network(tx)
        print("📤 Transaction sent: " + receipt.hash)
        
        # Wait for confirmation
        let confirmation = await wait_for_block(receipt.hash)
        print("✅ Transaction confirmed in block " + string(confirmation.block))
        
        return confirmation
    } catch(error) {
        print("❌ Transaction failed: " + string(error))
        return null
    }
}

# Concurrent operations
action async fetch_multiple_prices() {
    # Start all fetches concurrently
    let btc_future = spawn fetch_price("BTC")
    let eth_future = spawn fetch_price("ETH")
    let sol_future = spawn fetch_price("SOL")
    
    # Wait for all results
    let btc_price = await btc_future
    let eth_price = await eth_future
    let sol_price = await sol_future
    
    print("BTC: $" + string(btc_price))
    print("ETH: $" + string(eth_price))
    print("SOL: $" + string(sol_price))
}
```

### 2. Event System

Events enable reactive programming and blockchain event logging.

```zexus
# Define events
event TokenTransfer {
    from: string,
    to: string,
    amount: integer,
    timestamp: integer
}

event UserRegistered {
    user_id: string,
    email: string,
    plan: string
}

# Emit events
action transfer_tokens(from: string, to: string, amount: integer) {
    # ... transfer logic ...
    
    # Emit event
    emit TokenTransfer {
        from: from,
        to: to,
        amount: amount,
        timestamp: datetime_now().timestamp()
    }
}

# Register event handlers
register_event("token_transfer", action(event) {
    print("🔔 Transfer: " + event.from + " -> " + event.to)
    print("💰 Amount: " + string(event.amount))
    
    # Update analytics
    update_transfer_stats(event)
})

register_event("user_registered", action(event) {
    send_welcome_email(event.user_id, event.email)
    create_user_profile(event)
})
```

### 3. Module System

Import external modules and organize code.

```zexus
# Import built-in modules
use "crypto" as crypto
use "datetime" as datetime
use "math" as math

# Use imported modules
let hash = crypto.sha256("Hello World")
let timestamp = datetime.now().timestamp()
let random = math.random_int(1, 100)

# Import custom modules
use "token" as token_module
use "wallet" as wallet_module

let my_token = token_module.create_token("MyToken", 1000000)
let my_wallet = wallet_module.create_wallet("0x123")
```

### 4. Enums

Define enumerated types for better type safety.

```zexus
# Define enum
enum Status {
    PENDING,
    PROCESSING,
    COMPLETED,
    FAILED
}

enum ChainType {
    ZIVER,
    ETHEREUM,
    BSC,
    TON,
    POLYGON
}

# Use enums
let tx_status = Status.PENDING
let current_chain = ChainType.ETHEREUM

# In conditionals
if tx_status == Status.COMPLETED {
    print("Transaction completed!")
}

# In contracts
contract CrossChainBridge {
    persistent storage source_chain: ChainType
    persistent storage target_chain: ChainType
    persistent storage bridge_status: Status
    
    action init(from: ChainType, to: ChainType) {
        this.source_chain = from
        this.target_chain = to
        this.bridge_status = Status.PENDING
    }
    
    action execute_bridge() {
        this.bridge_status = Status.PROCESSING
        # ... bridge logic ...
        this.bridge_status = Status.COMPLETED
    }
}
```

### 5. Modifiers

Annotate functions with semantic modifiers.

```zexus
# Access modifiers
@public
action visible_to_all() {
    return "Everyone can call this"
}

@private
action internal_helper() {
    return "Internal use only"
}

@sealed
action final_implementation() {
    return "Cannot be overridden"
}

# Behavioral modifiers
@async
action async_operation() {
    # Runs asynchronously
}

@inline
action hot_path() {
    # Compiler inlines this function
    return 42
}

@pure
action calculate(x: integer) -> integer {
    # Pure function - no side effects
    return x * 2
}

@secure
action handle_password(password: string) {
    # Runs in secure context
}

# Combined modifiers
@public @async @secure
action secure_api_call() {
    # Public, async, and secure
}
```

---

## Security Features

Zexus provides **9 layers of security** for building production-grade applications.

### Layer 1: Export (File-Level Access Control)

Control which files can access specific functions/contracts.

```zexus
# Grant read-only access
export my_function to "other_file.zx" with "read_only"

# Grant read-write access
export TokenContract to "token_api.zx" with "read_write"

# Deny access (blacklist)
export sensitive_function to "untrusted.zx" with "deny"

# Export to multiple files
export shared_utility to ["file1.zx", "file2.zx"] with "read_only"
```

### Layer 2: Verify (Runtime Checks)

Add runtime validation before function execution.

```zexus
action transfer_funds(to: string, amount: integer) -> boolean {
    # Function implementation
    return true
}

# Add verification checks
verify(transfer_funds, [
    action() { return authenticated },              # Must be logged in
    action() { return balance >= amount },          # Sufficient balance
    action() { return amount > 0 },                 # Valid amount
    action() { return amount <= daily_limit },      # Within limits
    action() { return !is_blocked(to) }            # Recipient not blocked
])

# Verification with error handling
verify(delete_account, [
    action() { return has_admin_role },
    action() { return mfa_verified }
], action(error) {
    log_security_event("Unauthorized delete attempt")
    return false
})
```

### Layer 3: Protect (Security Guardrails)

Enforce security policies like rate limiting, authentication, HTTPS.

```zexus
action login(username: string, password: string) -> boolean {
    # Login logic
    return true
}

# Protect with security rules
protect(login, {
    rate_limit: 10,                          # Max 10 attempts per minute
    auth_required: false,                    # Pre-authentication not required
    require_https: true,                     # Only HTTPS connections
    min_password_strength: "strong",         # Password requirements
    session_timeout: 3600,                   # 1 hour sessions
    allowed_ips: ["10.0.0.0/8"],            # IP whitelist
    blocked_ips: ["192.168.1.100"],         # IP blacklist
    require_mfa: false                       # MFA not required for login
})

# Strict enforcement mode
protect(admin_panel, {
    auth_required: true,
    require_mfa: true,
    rate_limit: 50
}, "strict")  # Deny any violation

# Warn mode (log but allow)
protect(public_api, {
    rate_limit: 1000
}, "warn")

# Audit mode (allow but record)
protect(analytics, {
    rate_limit: 5000
}, "audit")
```

### Layer 4: Auth (Authentication Configuration)

Configure global authentication settings.

```zexus
auth {
    provider: "oauth2",                      # OAuth2, JWT, SAML, etc.
    scopes: ["read", "write", "admin"],
    token_expiry: 3600,                      # 1 hour tokens
    refresh_enabled: true,
    mfa_required: true,                      # Require MFA
    session_timeout: 7200,                   # 2 hour sessions
    password_min_length: 12,
    password_require_uppercase: true,
    password_require_symbols: true,
    max_login_attempts: 5,
    lockout_duration: 900                    # 15 minutes
}
```

### Layer 5: Middleware (Request Processing)

Process and validate requests in a pipeline.

```zexus
# Authentication middleware
middleware(authenticate, action(request, response) {
    let token = request.headers["Authorization"]
    
    if !verify_token(token) {
        response.status = 401
        response.body = "Unauthorized"
        return false  # Stop chain
    }
    
    request.user = decode_token(token)
    return true  # Continue chain
})

# Logging middleware
middleware(log_requests, action(request, response) {
    print("[" + datetime_now() + "] " + request.method + " " + request.path)
    log_to_file(request)
    return true
})

# CORS middleware
middleware(cors, action(request, response) {
    response.headers["Access-Control-Allow-Origin"] = "*"
    response.headers["Access-Control-Allow-Methods"] = "GET, POST, PUT, DELETE"
    return true
})

# Rate limiting middleware
middleware(rate_limit, action(request, response) {
    let client_ip = request.client_ip
    let request_count = get_request_count(client_ip)
    
    if request_count > 100 {
        response.status = 429
        response.body = "Too many requests"
        return false
    }
    
    increment_request_count(client_ip)
    return true
})
```

### Layer 6: Throttle (Rate Limiting)

Limit function execution to prevent abuse.

```zexus
action api_endpoint() {
    return { status: "ok", data: get_data() }
}

# Basic throttling
throttle(api_endpoint, {
    requests_per_minute: 100,
    burst_size: 10,
    per_user: true
})

# Advanced throttling
throttle(expensive_operation, {
    requests_per_minute: 5,
    requests_per_hour: 50,
    requests_per_day: 500,
    per_user: true,
    per_ip: false
})

# Different limits for different users
throttle(download_file, {
    requests_per_minute: 10,     # Free users
    premium_rpm: 100,             # Premium users
    enterprise_rpm: 1000          # Enterprise users
})
```

### Layer 7: Cache (Performance)

Cache expensive operations for better performance.

```zexus
action expensive_database_query(user_id: string) {
    # Slow database query
    return query_results
}

# Cache with TTL
cache(expensive_database_query, {
    ttl: 300,                                # Cache for 5 minutes
    key: "user_query",
    invalidate_on: ["user_updated", "data_changed"]
})

# Smart caching
cache(get_user_profile, {
    ttl: 3600,                               # 1 hour
    strategy: "lru",                         # Least Recently Used
    max_entries: 1000,
    invalidate_on: ["profile_updated"]
})

# Conditional caching
cache(get_market_data, {
    ttl: action() {
        # Dynamic TTL based on market volatility
        return is_volatile() ? 10 : 300
    },
    invalidate_on: ["price_alert"]
})
```

### Security Best Practices

```zexus
# Complete secure payment system
entity PaymentRequest {
    from: string,
    to: string,
    amount: integer,
    currency: string,
    timestamp: integer
}

contract SecurePaymentProcessor {
    persistent storage transactions: list
    persistent storage daily_limits: map
    persistent storage balances: map
    
    action process_payment(from: string, to: string, amount: integer) -> boolean {
        # Validation logic
        if this.balances.get(from, 0) < amount {
            return false
        }
        
        # Update balances
        this.balances[from] = this.balances[from] - amount
        this.balances[to] = this.balances.get(to, 0) + amount
        
        # Record transaction
        this.transactions.push({
            from: from,
            to: to,
            amount: amount,
            timestamp: datetime_now().timestamp()
        })
        
        # Emit event
        emit PaymentProcessed {
            from: from,
            to: to,
            amount: amount
        }
        
        return true
    }
}

# Layer 1: Export control
let processor = contract SecurePaymentProcessor()
export processor to "payment_api.zx" with "execute"

# Layer 2: Runtime verification
verify(processor.process_payment, [
    action() { return authenticated },
    action() { return kyc_verified },
    action() { return !suspended },
    action() { return amount <= 10000 }
])

# Layer 3: Protection rules
protect(processor.process_payment, {
    rate_limit: 100,
    auth_required: true,
    require_https: true,
    min_password_strength: "very_strong",
    session_timeout: 1800,
    require_mfa: true
}, "strict")

# Layer 4: Authentication
auth {
    provider: "oauth2",
    scopes: ["payments:process"],
    token_expiry: 3600,
    mfa_required: true
}

# Layer 5: Middleware
middleware(fraud_detection, action(request, response) {
    if request.amount > 50000 {
        flag_for_review(request)
    }
    return true
})

middleware(audit_log, action(request, response) {
    log_payment_attempt(request)
    return true
})

# Layer 6: Throttling
throttle(processor.process_payment, {
    requests_per_minute: 100,
    requests_per_hour: 500,
    per_user: true
})

# Layer 7: Caching
cache(get_exchange_rate, {
    ttl: 300,  # 5 minutes
    invalidate_on: ["rate_updated"]
})
```

---

## Built-in Functions

Zexus provides comprehensive built-in functions for common operations.

### Core Functions

```zexus
# Type conversion
let str = string(42)              # "42"
let num = integer("100")          # 100
let flt = float("3.14")          # 3.14

# Collection operations
let length = len([1, 2, 3])       # 3
let first_item = first([1, 2, 3]) # 1
let rest_items = rest([1, 2, 3])  # [2, 3]
let last_item = last([1, 2, 3])   # 3

# List operations
let doubled = map([1, 2, 3], action(x) { return x * 2 })  # [2, 4, 6]
let evens = filter([1, 2, 3, 4], action(x) { return x % 2 == 0 })  # [2, 4]
let sum = reduce([1, 2, 3, 4], action(acc, x) { return acc + x }, 0)  # 10

# String operations
let upper = "hello".to_upper()     # "HELLO"
let lower = "HELLO".to_lower()     # "hello"
let substr = "hello".slice(0, 3)   # "hel"
let parts = "a,b,c".split(",")     # ["a", "b", "c"]
let joined = ["a", "b"].join("-")  # "a-b"
```

### Crypto Functions

```zexus
use "crypto" as crypto

# Hashing
let hash = crypto.sha256("data")
let keccak = crypto.keccak256("data")
let ripemd = crypto.ripemd160("data")

# Signing
let private_key = crypto.generate_private_key()
let public_key = crypto.derive_public_key(private_key)
let signature = crypto.sign(message, private_key)
let valid = crypto.verify_signature(message, signature, public_key)

# Address generation
let address = crypto.create_address(public_key)
let checksum_addr = crypto.to_checksum_address(address)

# Random generation
let random_bytes = crypto.random_bytes(32)
let random_int = crypto.random_int(0, 100)
```

### DateTime Functions

```zexus
use "datetime" as datetime

# Current time
let now = datetime.now()
let timestamp = now.timestamp()
let iso_string = now.to_iso_string()

# Time operations
let future = now.add_days(7)
let past = now.subtract_hours(2)
let diff = future.diff(past)

# Formatting
let formatted = now.format("YYYY-MM-DD HH:mm:ss")
let date_only = now.format("YYYY-MM-DD")
```

### Math Functions

```zexus
use "math" as math

# Basic math
let abs_val = math.abs(-5)         # 5
let max_val = math.max(3, 7)       # 7
let min_val = math.min(3, 7)       # 3
let power = math.pow(2, 8)         # 256
let sqrt = math.sqrt(16)           # 4

# Rounding
let rounded = math.round(3.7)      # 4
let floor = math.floor(3.7)        # 3
let ceil = math.ceil(3.2)          # 4

# Random
let random = math.random()         # 0.0 to 1.0
let random_int = math.random_int(1, 100)  # 1 to 100
```

---

## Real-World Examples

### Example 1: Token Contract

```zexus
contract ZiverToken {
    name: "Ziver Token"
    version: "1.0.0"
    symbol: "ZVR"
    
    persistent storage balances: map
    persistent storage allowances: map
    persistent storage total_supply: integer
    persistent storage owner: string
    
    event Transfer {
        from: string,
        to: string,
        amount: integer
    }
    
    event Approval {
        owner: string,
        spender: string,
        amount: integer
    }
    
    action init(initial_supply: integer) {
        this.owner = msg.sender
        this.total_supply = initial_supply
        this.balances = {}
        this.balances[msg.sender] = initial_supply
        this.allowances = {}
    }
    
    action balance_of(account: string) -> integer {
        return this.balances.get(account, 0)
    }
    
    action transfer(to: string, amount: integer) -> boolean {
        let from_balance = this.balances.get(msg.sender, 0)
        
        if from_balance < amount {
            return false
        }
        
        if amount <= 0 {
            return false
        }
        
        this.balances[msg.sender] = from_balance - amount
        this.balances[to] = this.balances.get(to, 0) + amount
        
        emit Transfer {
            from: msg.sender,
            to: to,
            amount: amount
        }
        
        return true
    }
    
    action approve(spender: string, amount: integer) -> boolean {
        let owner_allowances = this.allowances.get(msg.sender, {})
        owner_allowances[spender] = amount
        this.allowances[msg.sender] = owner_allowances
        
        emit Approval {
            owner: msg.sender,
            spender: spender,
            amount: amount
        }
        
        return true
    }
    
    action transfer_from(from: string, to: string, amount: integer) -> boolean {
        let from_balance = this.balances.get(from, 0)
        let allowance = this.allowances.get(from, {}).get(msg.sender, 0)
        
        if from_balance < amount || allowance < amount {
            return false
        }
        
        this.balances[from] = from_balance - amount
        this.balances[to] = this.balances.get(to, 0) + amount
        
        let from_allowances = this.allowances.get(from, {})
        from_allowances[msg.sender] = allowance - amount
        this.allowances[from] = from_allowances
        
        emit Transfer {
            from: from,
            to: to,
            amount: amount
        }
        
        return true
    }
}
```

### Example 2: Multi-Chain Wallet

```zexus
use "crypto" as crypto

enum Chain {
    ZIVER,
    ETHEREUM,
    BSC,
    TON,
    POLYGON
}

entity ChainBalance {
    chain: Chain,
    balance: integer,
    pending: integer = 0
}

contract MultiChainWallet {
    name: "Multi-Chain Wallet"
    version: "2.0.0"
    
    persistent storage owner: string
    persistent storage balances: map
    persistent storage guardians: list
    persistent storage recovery_threshold: integer
    
    event Deposit {
        chain: Chain,
        amount: integer,
        timestamp: integer
    }
    
    event Withdrawal {
        chain: Chain,
        to: string,
        amount: integer,
        timestamp: integer
    }
    
    action init(owner_address: string) {
        this.owner = owner_address
        this.balances = {}
        this.guardians = []
        this.recovery_threshold = 2
        
        # Initialize chain balances
        this.balances[Chain.ZIVER] = 0
        this.balances[Chain.ETHEREUM] = 0
        this.balances[Chain.BSC] = 0
        this.balances[Chain.TON] = 0
        this.balances[Chain.POLYGON] = 0
    }
    
    action deposit(chain: Chain, amount: integer) -> boolean {
        if amount <= 0 {
            return false
        }
        
        let current = this.balances.get(chain, 0)
        this.balances[chain] = current + amount
        
        emit Deposit {
            chain: chain,
            amount: amount,
            timestamp: datetime_now().timestamp()
        }
        
        return true
    }
    
    action withdraw(chain: Chain, to: string, amount: integer) -> boolean {
        # Verify caller is owner
        if msg.sender != this.owner {
            return false
        }
        
        let current = this.balances.get(chain, 0)
        if current < amount {
            return false
        }
        
        this.balances[chain] = current - amount
        
        emit Withdrawal {
            chain: chain,
            to: to,
            amount: amount,
            timestamp: datetime_now().timestamp()
        }
        
        return true
    }
    
    action get_total_balance() -> integer {
        let total = 0
        total = total + this.balances.get(Chain.ZIVER, 0)
        total = total + this.balances.get(Chain.ETHEREUM, 0)
        total = total + this.balances.get(Chain.BSC, 0)
        total = total + this.balances.get(Chain.TON, 0)
        total = total + this.balances.get(Chain.POLYGON, 0)
        return total
    }
    
    action add_guardian(guardian: string) -> boolean {
        if msg.sender != this.owner {
            return false
        }
        
        this.guardians.push(guardian)
        return true
    }
}
```

### Example 3: P2P Network Node

```zexus
use "crypto" as crypto
use "datetime" as datetime

protocol NetworkNode {
    action connect_peer(peer_address: string) -> boolean
    action send_message(peer: string, message: map) -> boolean
    action broadcast(message: map) -> boolean
    action get_peers() -> list
}

entity Peer {
    address: string,
    public_key: string,
    connected_at: integer,
    last_seen: integer,
    message_count: integer = 0
}

contract P2PNode implements NetworkNode {
    persistent storage node_id: string
    persistent storage peers: map
    persistent storage message_buffer: list
    persistent storage max_peers: integer = 50
    
    action init(node_address: string) {
        this.node_id = node_address
        this.peers = {}
        this.message_buffer = []
    }
    
    action connect_peer(peer_address: string) -> boolean {
        # Check max peers limit
        if len(this.peers) >= this.max_peers {
            print("❌ Max peers reached")
            return false
        }
        
        # Check if already connected
        if this.peers.has(peer_address) {
            print("ℹ️  Already connected to " + peer_address)
            return true
        }
        
        # Create peer entry
        let peer = Peer{
            address: peer_address,
            public_key: "",  # Would exchange keys in real implementation
            connected_at: datetime_now().timestamp(),
            last_seen: datetime_now().timestamp(),
            message_count: 0
        }
        
        this.peers[peer_address] = peer
        print("✅ Connected to peer: " + peer_address)
        
        return true
    }
    
    action send_message(peer: string, message: map) -> boolean {
        if !this.peers.has(peer) {
            print("❌ Not connected to peer: " + peer)
            return false
        }
        
        # Add metadata
        let full_message = {
            payload: message,
            from: this.node_id,
            timestamp: datetime_now().timestamp(),
            message_id: crypto.keccak256(string(message))
        }
        
        # Update peer stats
        let peer_obj = this.peers[peer]
        peer_obj.message_count = peer_obj.message_count + 1
        peer_obj.last_seen = datetime_now().timestamp()
        
        print("📤 Sent message to " + peer)
        return true
    }
    
    action broadcast(message: map) -> boolean {
        let sent_count = 0
        
        for each peer_address in this.peers {
            if this.send_message(peer_address, message) {
                sent_count = sent_count + 1
            }
        }
        
        print("📡 Broadcast to " + string(sent_count) + " peers")
        return sent_count > 0
    }
    
    action get_peers() -> list {
        return this.peers.keys()
    }
    
    action disconnect_peer(peer_address: string) -> boolean {
        if !this.peers.has(peer_address) {
            return false
        }
        
        this.peers.delete(peer_address)
        print("👋 Disconnected from " + peer_address)
        return true
    }
}
```

---

## What's Currently Available

Based on analysis of the Zexus interpreter source code and Ziver-Chain examples:

### ✅ Fully Implemented

1. **Core Language**
   - Variables (let)
   - Actions (functions)
   - Control flow (if/else, for, while)
   - Try/catch error handling
   - Comments (# and //)

2. **Data Types**
   - Primitives: integer, float, string, boolean, null
   - Collections: list, map
   - Optional types: type?

3. **Smart Contracts**
   - contract keyword
   - persistent storage
   - Contract methods (actions)
   - Contract instantiation

4. **Protocols & Entities**
   - protocol definitions (interfaces)
   - entity definitions (typed structs)
   - Protocol implementation

5. **Async/Await**
   - async action definitions
   - await expressions
   - spawn for concurrency

6. **Events**
   - event definitions
   - emit statements
   - register_event handlers

7. **Modules**
   - use/import statements
   - Built-in modules (crypto, datetime, math)
   - Custom module imports

8. **Enums**
   - enum definitions
   - Enum value access

9. **Modifiers**
   - @public, @private, @sealed
   - @async, @inline, @pure
   - @secure, @native

10. **Security Features**
    - export (access control)
    - verify (runtime checks)
    - protect (security rules)
    - auth (authentication config)
    - middleware (request processing)
    - throttle (rate limiting)
    - cache (performance)

11. **Built-in Functions**
    - Type conversion: string(), integer(), float()
    - Collections: len(), first(), rest(), last()
    - Higher-order: map(), filter(), reduce()
    - Crypto: sha256(), keccak256(), sign(), verify()
    - DateTime: now(), timestamp(), format()
    - Math: abs(), max(), min(), pow(), sqrt()

12. **Virtual Machine**
    - Stack-based bytecode execution
    - Bytecode compilation
    - Optimization passes
    - Closure support

13. **Renderer System**
    - Screen definitions
    - Component definitions
    - Theme system
    - Canvas drawing

### 🚧 Partially Implemented / In Progress

1. **Type System**
   - Basic type inference ✅
   - Type annotations ✅
   - Generic types 🚧
   - Trait system 🚧

2. **Standard Library**
   - Core modules ✅
   - Extended utilities 🚧
   - Blockchain integrations 🚧

3. **Tooling**
   - Basic REPL ✅
   - Syntax validator ✅
   - Debugger 🚧
   - Profiler 🚧

### ❌ Not Yet Implemented

1. **Advanced Features**
   - Pattern matching
   - Destructuring assignment
   - Operator overloading
   - Macros (beyond @modifiers)

2. **Concurrency**
   - Channels
   - Mutexes/Locks
   - Actor model

3. **Error Handling**
   - Result types
   - Option types
   - Custom error types

4. **Testing**
   - Built-in test framework
   - Assertion library
   - Mock/stub utilities

---

## Discovered Patterns from Codebase Analysis

### Pattern 1: Nested Contracts

Zexus supports nested contract definitions:

```zexus
contract ParentContract {
    persistent storage parent_data: map
    
    # Nested contract
    contract ChildContract {
        persistent storage child_data: string
        
        action child_action() {
            return "From child"
        }
    }
    
    action parent_action() {
        let child = contract ChildContract()
        return child.child_action()
    }
}
```

### Pattern 2: Ternary-like Expressions

```zexus
let result = condition ? value_if_true : value_if_false

# Example
let encrypted = connection.is_secure ? 
    encrypt_message(msg, key) : msg
```

### Pattern 3: Method Chaining

```zexus
let result = data
    .filter(action(x) { return x > 10 })
    .map(action(x) { return x * 2 })
    .reduce(action(acc, x) { return acc + x }, 0)

let formatted_date = datetime
    .now()
    .add_days(7)
    .format("YYYY-MM-DD")
```

### Pattern 4: Map/Object Methods

```zexus
let user = {name: "Alice", age: 30}

# Check key existence
if user.has("name") {
    print("Has name")
}

# Get with default
let role = user.get("role", "user")

# Get keys/values
let keys = user.keys()      # ["name", "age"]
let values = user.values()  # ["Alice", 30]

# Delete key
user.delete("age")
```

### Pattern 5: List Methods

```zexus
let numbers = [1, 2, 3]

# Add elements
numbers.push(4)              # [1, 2, 3, 4]
numbers.unshift(0)           # [0, 1, 2, 3, 4]

# Remove elements
let last = numbers.pop()     # 4, numbers = [0, 1, 2, 3]
let first = numbers.shift()  # 0, numbers = [1, 2, 3]

# Check membership
if numbers.contains(2) {
    print("Has 2")
}

# Slice
let subset = numbers.slice(0, 2)  # [1, 2]
```

---

## What Could Be Added (Future Enhancements)

### 1. Pattern Matching

```zexus
# Proposed syntax
match value {
    Status.PENDING -> print("Waiting")
    Status.COMPLETED -> print("Done")
    Status.FAILED -> print("Error")
    _ -> print("Unknown")
}

# With value extraction
match response {
    { status: 200, data: d } -> process_data(d)
    { status: 404 } -> print("Not found")
    { status: s } -> print("Status: " + string(s))
}
```

### 2. Destructuring

```zexus
# Proposed syntax
let {name, age} = user
let [first, second, ...rest] = numbers

# In function parameters
action process_user({name, age, email}) {
    print(name + " is " + string(age))
}
```

### 3. Pipe Operator

```zexus
# Proposed syntax
let result = data
    |> filter(is_positive)
    |> map(double)
    |> reduce(sum, 0)
```

### 4. Range Expressions

```zexus
# Proposed syntax
for i in 0..10 {
    print(i)
}

let slice = numbers[1..5]    # Elements 1-4
let slice2 = numbers[1..]    # Elements 1 to end
```

### 5. Null-Safe Operations

```zexus
# Proposed syntax
let email = user?.contact?.email  # Returns null if any part is null
let name = user?.name ?? "Anonymous"  # Null coalescing
```

---

## Current Use Cases

Zexus is currently being used for:

1. **Blockchain Development**
   - Smart contracts
   - Token standards (ERC-20 style)
   - Multi-chain wallets
   - Cross-chain bridges

2. **Decentralized Applications**
   - P2P networking
   - Consensus mechanisms
   - State management
   - RPC servers

3. **Security Applications**
   - Access control systems
   - Authentication/authorization
   - Rate limiting
   - Audit logging

4. **UI/Graphics**
   - Terminal-based UIs
   - Component systems
   - Theme management
   - Data visualization

---

## Learning Path

### Beginner (Week 1-2)
1. Learn basic syntax (variables, actions, control flow)
2. Practice with collections (lists, maps)
3. Understand error handling (try/catch)
4. Write simple scripts

### Intermediate (Week 3-4)
1. Learn smart contracts (contract, persistent storage)
2. Understand protocols and entities
3. Practice with events
4. Build a simple token contract

### Advanced (Week 5-6)
1. Master async/await
2. Implement security layers
3. Build complex contracts
4. Create multi-chain applications

### Expert (Week 7+)
1. Contribute to interpreter
2. Optimize performance
3. Build production applications
4. Write language extensions

---

## Common Pitfalls & How to Avoid Them

### 1. Forgetting `this` in contracts

```zexus
# ❌ Wrong
contract MyContract {
    persistent storage balance: integer
    
    action add(amount) {
        balance = balance + amount  # Error! balance not defined
    }
}

# ✅ Correct
contract MyContract {
    persistent storage balance: integer
    
    action add(amount) {
        this.balance = this.balance + amount  # Use this.balance
    }
}
```

### 2. Not handling null values

```zexus
# ❌ Risky
let balance = balances[user]  # Could be null!
let new_balance = balance + 100  # Error if balance is null

# ✅ Safe
let balance = balances.get(user, 0)  # Default to 0
let new_balance = balance + 100  # Always safe
```

### 3. Mixing synchronous and asynchronous code

```zexus
# ❌ Wrong
action fetch_data() {
    let result = await http_get(url)  # Error! Not in async action
    return result
}

# ✅ Correct
action async fetch_data() {
    let result = await http_get(url)
    return result
}
```

### 4. Not validating inputs

```zexus
# ❌ Dangerous
action transfer(to, amount) {
    this.balances[to] = amount  # No validation!
}

# ✅ Safe
action transfer(to, amount) {
    if amount <= 0 {
        return false
    }
    if !is_valid_address(to) {
        return false
    }
    this.balances[to] = amount
    return true
}
```

---

## Summary

**Zexus** is a powerful, blockchain-native language that combines:
- **Easy-to-learn syntax** (Python/JavaScript-like)
- **Strong blockchain primitives** (contracts, persistent storage, events)
- **Comprehensive security** (9 layers of protection)
- **Modern features** (async/await, modules, type system)
- **Production-ready** (VM with optimizations)

**Key Strengths:**
- Built for blockchain from the ground up
- Multi-chain support out of the box
- Security-first design philosophy
- Rich built-in functionality
- Dual execution modes (interpreter + compiler)

**Best For:**
- Smart contract development
- DeFi applications
- Multi-chain wallets
- Decentralized applications
- Secure backend services

**Official Resources:**
- 🔗 Interpreter: https://github.com/Zaidux/zexus-interpreter
- 🔗 Ziver Chain: https://github.com/ZiverLabs/Ziver-Chain
- 📚 Syntax Reference: docs/ZEXUS_SYNTAX_REFERENCE.md
- 📚 Quick Reference: docs/ZEXUS_QUICK_REFERENCE.md

---

**Version**: 1.0.0  
**Last Updated**: December 2025  
**Based on**: Zexus-interpreter (e187fb7) + Ziver-Chain codebase analysis
