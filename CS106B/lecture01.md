# Programming Abstractions

## First C++ program (1.1)

```cpp
/*
 * hello.cpp
 * This program prints a welcome message to the user.
 */
#include <iostream>
#include "console.h" // Stanford-specific library
using namespace std;

int main() {
    cout << "Hello, world!" << endl;
    return 0;
}
```

- `Include` statements are like imports in
Java/Python. More on this in a moment.

- Every C++ program has a `main` function.
The program starts at `main` and
executes its statements in sequence.

- At program end, `main` returns 0 to indicate
successful completion. A non-zero return value is an
error code (we won’t use this method of error
reporting in this class so we will always return zero)

## C++ variables and types (1.5-1.8)

* **Strict Typing**: Unlike Python, where types are implicit and "just happen," the C++ compiler is very strict about variable types.
* **Mandatory Declaration**: You must "announce" (declare) a variable's type the first time you introduce it to the compiler.
* **Static Typing**: Once a variable is declared with a specific type, it **cannot** be changed later. A C++ variable can only hold data of its declared type.
* **Usage**: After the initial declaration, use only the variable name without repeating the type.

**Example Syntax:**
```
int x = 42;        // Declaration: type (int) + name (x)
x = x + 7;         // Usage: name only
```
### **More C++ Syntax Examples (1.5-1.8)**

```cpp
// for loops
for (int i = 0; i < 10; i++) { 
    if (i % 2 == 0) { // if statements
        x += i;
    } 
    /* two comment styles: 
       double slash for single line, 
       slash-star for blocks */
}

// while loops and logic
while (letter != 'Q' && !done) { 
    x = x / 2;
    if (x == 42) { 
        return 0; 
    }
}

binky(pi, 17);             // function call
winky("this is a string"); // string usage
```
