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
