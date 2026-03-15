# Programming Abstractions

## First C++ program (1.1)

- `Include` statements are like imports in
Java/Python. More on this in a moment.

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
