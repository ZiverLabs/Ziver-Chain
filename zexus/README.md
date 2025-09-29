# Zexus: The Intent-Based Programming Language

Zexus is a revolutionary programming language designed for readability, security, and performance. It enables developers to describe what they want to achieve, not how to achieve it.

## 🎯 Philosophy

Zexus is built on four core principles:

1. **Natural Language First**: Readable syntax that resembles English
2. **Declarative & Intent-Based**: Focus on what, not how
3. **Context is King**: The compiler understands your intent
4. **Everything is a Module**: Clean, organized, and reusable code

## ✨ Features

- **📝 Readable Syntax**: Code that reads like English
- **🔒 Built-in Security**: Mathematical verification of correctness
- **⚡ High Performance**: Compiled to native code or WASM
- **🌐 Multi-Paradigm**: Supports OOP, functional, and imperative styles
- **📦 Comprehensive Stdlib**: Batteries included approach

## 🏗️ Architecture

```

compiler/
├──src/
│├── lexer.zx        # Lexical analysis
│├── parser.zx       # Syntax analysis
│├── semantic.zx     # Semantic analysis
│└── codegen/        # Code generation
├──standard_lib/       # Standard library
├──examples/          # Example programs
└──documentation/     # Language specification

```

## 🚀 Getting Started

### Installation

```bash
# Install Zexus compiler
curl -fsSL https://install.zexus.lang | bash

# Or build from source
git clone https://github.com/ZiverLabs/ZiverLabs.git
cd ZiverLabs/zexus
zpm install
zpm build --release
```

Your First Zexus Program

Create hello.zx:

```zexus
screen HelloWorld:
  on "load":
    print "Hello, World!"
```

Run it:

```bash
zx hello.zx
```

📚 Learning Zexus

Basic Syntax

```zexus
# Variables
let message = "Hello, Zexus!"
let count = 42

# Functions
action greet(name: text):
  print "Hello, {name}!"

# Control flow
if count > 10:
  print "Count is large"
else:
  print "Count is small"

# Loops
for each number in [1, 2, 3]:
  print number
```

Advanced Features

```zexus
# Database integration
database Users:
  entity User:
    name: text
    email: email
    created: datetime

# AI integration
use zenith_ai from "../zenith-ai/core/inference.zx"

action analyze_data(data: List<number>):
  let result = zenith_ai.analyze(data)
  print "Analysis: {result}"
```

🔧 Development Tools

Zexus Package Manager (zpm)

```bash
# Create new project
zpm init my_project

# Install dependencies
zpm install http database

# Build project
zpm build

# Run tests
zpm test
```

Zexus Runner (zx)

```bash
# Run a Zexus file
zx program.zx

# Run with arguments
zx program.zx --input data.txt

# Run in debug mode
zx --debug program.zx
```

📖 Documentation

· Language Specification
· Standard Library Reference
· Tutorials
· API Documentation

🎓 Examples

Check out our examples directory for:

· Web applications
· Blockchain smart contracts
· AI integration examples
· Database management
· Game development

🤝 Contributing

We welcome contributions to Zexus! Please see:

· Contributing Guide
· Language Specification Issues
· Compiler Development Guide

📄 License

Zexus is licensed under Apache 2.0. See LICENSE for details.

🔗 Resources

· Official Website
· Online Playground
· Community Forum
· Documentation Portal

