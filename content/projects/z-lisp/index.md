---
title: Z-Lisp Programming Language
summary: "A Lisp-like programming language interpreter written in C, following Build Your Own Lisp and extended with additional features, including WebAssembly support for in-browser execution."
description: "A Lisp-like programming language interpreter written in C, following Build Your Own Lisp and extended with additional features, including WebAssembly support for in-browser execution."
technologies: [C, Lisp, MPC Parser, WebAssembly]
date: 2024-12-19
draft: false
showauthor: false
---

{{< github repo="AzzamAlsharafi/Z-Lisp" >}}

<br/>

{{< button href="#try-it-live" >}}
Try it out!
{{< /button >}}


A Lisp-like programming language interpreter written in [C], following *[Build Your Own Lisp]* and extended with additional features, including [WebAssembly] support for in-browser execution.

## Overview

Z-Lisp is an interpreted Lisp-like programming language born from following the *[Build Your Own Lisp]* book, then extended with additional features and capabilities. The interpreter is implemented in [C] for performance and portability, with [WebAssembly] compilation support enabling execution directly in web browsers.

The language features a simple yet powerful syntax based on S-expressions, supporting fundamental data types including numbers, strings, symbols, lists, and functions. Built-in operators cover arithmetic operations, list manipulation, variable definition, control flow, and functions.

## Features

- **S-expression syntax** - Expressions use parentheses `()`, Lists use curly braces `{}`
- **Dynamic typing** - Supports integers, floats, strings, lists, and functions
- **Function definitions** - Anonymous functions with `fun` and variable binding with `def`
- **Built-in operations** - Arithmetic, comparison, logical, and list manipulation functions
- **Control flow** - Conditional statements with `if` and local variable binding with `=`
- **File loading** - Load and execute Z-Lisp code from files using `load`
- **REPL environment** - Interactive development and testing
- **WebAssembly support** - Compiled with [Emscripten] for in-browser execution

## Try It Live

Experience Z-Lisp directly in your browser with the interactive terminal:

{{< zlisp-terminal >}}

<figcaption>Interactive Z-Lisp interpreter running in your browser via WebAssembly.</figcaption>

## Language Syntax

### Basic Components

Z-Lisp syntax follows simple rules:

```zlisp
42                           ; Numbers - integers and floats
3.14159                      ; Floating point numbers
"Hello, World!"              ; Strings - enclosed in double quotes
x                            ; Symbols - names for variables and functions
{1 2 3 4 5}                  ; Lists - use curly braces, not evaluated
(+ 1 2 3)                    ; Expressions - use parentheses, evaluated
(fun {x} {* x x})            ; Anonymous function definition
; This is a comment          ; Comments - anything after ; is ignored
```

### Basic Operations

```zlisp
(+ 10 20 30)                 ; => 60.0 - Arithmetic addition
(- 100 25)                   ; => 75.0 - Subtraction
(* 5 5 5)                    ; => 125.0 - Multiplication
(/ 100 4)                    ; => 25.0 - Division
(% 7 3)                      ; => 1.0 - Modulo
(^ 2 3)                      ; => 8.0 - Power
(> 10 5)                     ; => 1 (true) - Greater than
(< 3 7)                      ; => 1 (true) - Less than
(== 5 5)                     ; => 1 (true) - Equality
(&& 1 0)                     ; => 0 (false) - Logical AND
(|| 0 1)                     ; => 1 (true) - Logical OR
(! 0)                        ; => 1 (true) - Logical NOT
```

### Variables and Functions

```zlisp
(def {x name} 10 "Z-Lisp")        ; Define variables globally
(= {y} 20)                        ; Define local variables
(fun {x} {* x x})                 ; Define anonymous functions
(def {square} (fun {x} {* x x}))  ; Define named functions
(def {factorial} (fun {n} {if (== n 0) {1} {* n (factorial (- n 1))}}))  ; Recursive function
(square 5)                        ; => 25.0 - Use functions
(factorial 5)                     ; => 120.0 - Call factorial
```

### Control Flow

```zlisp
(if (> x 0) {print "positive"} {print "non-positive"})     ; Conditional expressions
(= {x y} 10 20)              ; Local variable bindings
(+ x y)                      ; => 30.0 - Use local variables
(env)                        ; Returns current environment
```

### List Operations

```zlisp
(def {nums} {1 2 3 4 5})     ; Create lists
(list 1 2 3 4 5)             ; Creates {1 2 3 4 5}
(get nums 0)                 ; => 1 (get element at index)
(remove nums 0)              ; => {2 3 4 5} (remove element at index)
(len nums)                   ; => 5 (list length)
(+ {1 2} {3 4})              ; => {1 2 3 4} - List concatenation
(+ nums 6)                   ; => {1 2 3 4 5 6} - Add element to list
(+ "Hello " "World")         ; => "Hello World" - String concatenation
```

## Built-in Functions

Z-Lisp provides a comprehensive set of built-in functions covering arithmetic, list manipulation, control flow, and I/O operations:

| Function | Description | Example |
|----------|-------------|----------|
| `+` | Addition (numbers), concatenation (strings/lists) | `(+ 1 2 3)` → `6.0` |
| `-` | Subtraction or negation | `(- 10 3)` → `7.0` |
| `*` | Multiplication | `(* 2 3 4)` → `24.0` |
| `/` | Division | `(/ 10 2)` → `5.0` |
| `%` | Modulo | `(% 7 3)` → `1.0` |
| `^` | Power | `(^ 2 3)` → `8.0` |
| `>` | Greater than | `(> 5 3)` → `1` |
| `<` | Less than | `(< 3 5)` → `1` |
| `==` | Equality | `(== 5 5)` → `1` |
| `&&` | Logical AND | `(&& 1 1)` → `1` |
| `\|\|` | Logical OR | `(\|\| 0 1)` → `1` |
| `!` | Logical NOT | `(! 0)` → `1` |
| `list` | Create list | `(list 1 2 3)` → `{1 2 3}` |
| `get` | Get element by index | `(get {a b c} 1)` → `b` |
| `remove` | Remove element by index | `(remove {a b c} 1)` → `{a c}` |
| `len` | List length | `(len {1 2 3})` → `3` |
| `def` | Define global variables | `(def {x} 10)` |
| `=` | Define local variables | `(= {y} 20)` |
| `fun` | Define anonymous function | `(fun {x} {* x x})` |
| `if` | Conditional expression | `(if (> x 0) {"pos"} {"neg"})` |
| `eval` | Evaluate list as expression | `(eval {+ 1 2})` → `3.0` |
| `load` | Load and execute file | `(load "script.zl")` |
| `print` | Print to console | `(print "Hello")` |
| `error` | Print error message | `(error "Oops!")` |
| `exit` | Exit program | `(exit())` |
| `env` | Show current environment | `(env())` |
| `typeof` | Get type as string | `(typeof 42)` → `"Integer"` |
| `string` | Convert to string | `(string 42)` → `"42"` |
| `int` | Convert to integer | `(int "42")` → `42` |
| `float` | Convert to float | `(float "3.14")` → `3.14` |

## Implementation Details

### Architecture

The Z-Lisp interpreter is built in [C] using the [MPC] *(Micro Parser Combinators)* library for parsing. The architecture follows a classic interpreter design:

1. **Lexical Analysis** - Tokenizes input into meaningful symbols
2. **Parsing** - Grammar-based parsing using [MPC] combinators
3. **AST Evaluation** - Tree-walking interpreter with environment-based scoping
4. **Memory Management** - Manual memory management with proper cleanup

### WebAssembly Compilation

The interpreter compiles to [WebAssembly] using [Emscripten], enabling:

- **Browser execution** - Run Z-Lisp directly in web browsers
- **Portability** - No server dependencies required
- **Interactive REPL** - Full REPL functionality preserved in browser environment

## Building from Source

### Native Build

```bash
# Clone the repository
git clone https://github.com/AzzamAlsharafi/Z-Lisp.git
cd Z-Lisp

# Build the interpreter
make

# Run the REPL
./zlisp

# Run a Z-Lisp file
./zlisp program.zsp
```

### WebAssembly Build

```bash
# Install Emscripten
git clone https://github.com/emscripten-core/emsdk.git
cd emsdk
./emsdk install latest
./emsdk activate latest
source ./emsdk_env.sh

# Build WASM module
cd /path/to/Z-Lisp
make wasm

# Generated files: main.js, main.wasm, main.data
```

## Example Programs

### Fibonacci Sequence

```zlisp
(def {fib} (fun {n} {if (< n 2) {n} {+ (fib (- n 1)) (fib (- n 2))}}))   ; Define fibonacci function
(fib 0)                          ; => 0.0
(fib 1)                          ; => 1.0
(fib 5)                          ; => 5.0
(fib 10)                         ; => 55.0
```

### Factorial Function

```zlisp
(def {factorial} (fun {n} {if (== n 0) {1} {* n (factorial (- n 1))}}))  ; Define factorial function
(factorial 5)                    ; => 120.0
(factorial 0)                    ; => 1.0
```

### List Processing

```zlisp
(def {numbers} {1 2 3 4 5})      ; Define a list of numbers
(get numbers 0)                  ; => 1 - Get first element
(get numbers 2)                  ; => 3 - Get third element
(len numbers)                    ; => 5 - List length
(+ numbers 6)                    ; => {1 2 3 4 5 6} - Add element
(+ {0} numbers)                  ; => {0 1 2 3 4 5} - Prepend element
(remove numbers 0)               ; => {2 3 4 5} - Remove first element
```


[Build Your Own Lisp]: https://www.buildyourownlisp.com
[C]: https://en.wikipedia.org/wiki/C_(programming_language)
[WebAssembly]: https://webassembly.org
[Emscripten]: https://emscripten.org
[MPC]: https://github.com/orangeduck/mpc