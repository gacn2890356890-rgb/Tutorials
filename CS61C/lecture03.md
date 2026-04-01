# 🚀 C Basics: The Bedrock of Systems Programming

> C is not a big language, and it is not well served by a big book. Its power comes from its closeness to the hardware and its minimal abstraction. 

---

## 1. The Systems Programming Landscape: Why C?

Before diving into C, it is essential to understand where it sits compared to modern alternatives.

* **C**: The undisputed king of systems programming. It offers raw, unfiltered access to memory and hardware. It is fast, ubiquitous, and assumes the programmer knows exactly what they are doing (even when they don't).
* **Rust**: The modern challenger. It offers the same bare-metal performance as C, but introduces a strict compiler (the Borrow Checker) to guarantee memory safety and thread safety, preventing entire classes of bugs (like segfaults) at compile time.
* **Go (Golang)**: Designed by Google for scalable network services. It compiles to machine code but includes a Garbage Collector (GC) and a heavy runtime. It prioritizes developer velocity and concurrency (Goroutines) over bare-metal memory control.

---

## 2. Execution Models: Compile vs. Interpret

How does human-readable code become machine-executable?

* **Interpreted (e.g., Python):** The source code is translated and executed line-by-line by an interpreter at runtime. It is highly portable but generally slower.
* **Compiled (e.g., C):** The entire source code is translated into target machine code (binary) *before* execution. 
* **Hybrid / Bytecode (e.g., Java):** Java source code is compiled down to an intermediate "Bytecode" (`.class` files), which is then interpreted/JIT-compiled by the Java Virtual Machine (JVM) at runtime. 

**C vs. Java:** C compiles directly to native hardware instructions (fast, but platform-dependent). Java compiles to a virtual machine (slower startup, but "Write Once, Run Anywhere").

### Advantages of AOT (Ahead-of-Time) Compilation
* **Maximum Performance**: The compiler has time to heavily optimize the code.
* **No Runtime Overhead**: No interpreter or garbage collector stealing CPU cycles.
* **Source Code Protection**: You distribute the binary executable (`a.out`), not your proprietary source code.

---

## 3. The Compilation Pipeline (C.A.L.)

When you run `gcc myProgram.c`, a complex multi-step pipeline is triggered.

1.  **Preprocessor (`.c` $\rightarrow$ `.i`)**: Handles text substitution (macros, `#include`, `#define`).
2.  **Compiler (`.i` $\rightarrow$ `.s`)**: Translates the C code into Assembly language.
3.  **Assembler (`.s` $\rightarrow$ `.o`)**: Translates Assembly into machine-level Object Code.
4.  **Linker (`.o` $\rightarrow$ executable)**: Stitches multiple object files and external libraries together.

### The Linker in Action
Imagine a project with two source files and a math library:

```text
foo.c --[Compiler/Assembler]--> foo.o \
                                       \
bar.c --[Compiler/Assembler]--> bar.o ---[ Linker ]---> a.out (Executable)
                                       /
Standard Library -------------> lib.o /

```

## 4. The Preprocessor & The Macro Trap

The C Preprocessor does not understand C syntax; it only understands **text replacement**.

### Macros vs. Functions

You will often see macros used to create small "functions" to avoid the overhead of a real function call:

C

```
#define min(X, Y) ((X) < (Y) ? (X) : (Y))
```

**⚠️ The Side-Effect Warning:**

Because macros are blind string replacements, they can cause disastrous bugs if the arguments have side effects (like `i++` or a function that modifies a variable).

C

```
// Assume foo() increments a global counter and returns a value
int next = min(w, foo(z));

// The preprocessor expands this to:
int next = ((w) < (foo(z)) ? (w) : (foo(z)));
```

*If `w` is greater than `foo(z)`, the function `foo(z)` will be executed **twice**—once during the condition check, and once to return the value. Always use `static inline` functions in modern C to avoid this!*

------

## 5. Anatomy of a C Program: `main`

The entry point of every C program is the `main` function. It takes two arguments from the operating system to handle command-line inputs.

C

```
int main(int argc, char *argv[]) {
    // Program logic
    return 0;
}
```

### Understanding `argc` and `argv`

Assume you compile your program to an executable named `sort` and run the following UNIX command:

```
unix% ./sort myFile
```

- `argc` (Argument Count): The number of strings passed. Here, `argc = 2`.
- `argv` (Argument Vector): An array of string pointers.
  - `argv[0]` $\rightarrow$ `"./sort"` (The name of the program itself)
  - `argv[1]` $\rightarrow$ `"myFile"` (The first argument)

------

## 6. Truth, Types, and Memory

### The Concept of "False"

Historically, C did not have a built-in boolean type. Instead, truth is evaluated based on memory contents:

- **`0`**: The integer zero is `false`.
- **`NULL`**: A pointer to address 0 is `false`.
- **`false`**: (Introduced later in `<stdbool.h>`), evaluates to 0.
- **True**: *Absolutely any non-zero value* (1, -1, 42, a valid memory address) evaluates to `true`.

### Core Data Types

*Note: Exact sizes depend on the architecture (e.g., 32-bit vs. 64-bit systems).*

| **Type**       | **Description**                   | **Typical Size (64-bit OS)**    |
| -------------- | --------------------------------- | ------------------------------- |
| `char`         | Single character (ASCII)          | 1 byte                          |
| `int`          | Standard integer                  | 4 bytes                         |
| `unsigned int` | Non-negative integer              | 4 bytes                         |
| `long`         | Extended integer                  | 8 bytes                         |
| `long long`    | Guaranteed $\ge$ 64-bit int       | 8 bytes                         |
| `float`        | Single-precision floating point   | 4 bytes                         |
| `double`       | Double-precision floating point   | 8 bytes                         |
| `enum`         | User-defined enumerated constants | Usually 4 bytes (acts as `int`) |

------

## 7. Control Flows & The Notorious `goto`

C provides standard control flow mechanisms: `if-else`, `for`, `while`, `do-while`, and `switch`.

### The `goto` Statement

`goto` allows you to jump unconditionally to another label within the same function.

C

```
    if (error_occurred) {
        goto cleanup;
    }
    // ... normal execution ...

cleanup:
    free(memory);
    return -1;
```

**Best Practice:** In modern software engineering, `goto` is heavily discouraged because it creates "spaghetti code" that is impossible to read or debug. The **only** generally accepted use case for `goto` in C is for jumping to a unified error-handling and memory-cleanup block at the end of a function (as seen in the Linux Kernel source code).
