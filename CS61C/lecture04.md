# 🧠 C Pointers, Arrays, and Strings

## 1. The Memory Model: A Giant Array

In the eyes of a C programmer, memory is nothing more than a single, massive, byte-addressable 1D array. Each "index" in this array is a **memory address**.

- **Byte-Addressable**: Every byte has its own unique address.
- **Word Alignment**: While we can address bytes, modern CPUs often fetch data in "words" (e.g., 4 or 8 bytes). Data must often be aligned to these word boundaries for performance or hardware requirements.

------

## 2. Pointers: Variables for Addresses

A pointer is a variable that stores a memory address rather than a direct value.

C

```
int x = 42;
int *p = &x; // p stores the address of x
```

### Pass-by-Value vs. Pass-by-Pointer

Both C and Java use **Pass-by-Value**. This means the function always receives a **copy** of the argument.

- **The Problem**: If you pass a large vector or struct, copying the whole thing is slow. If you want to change the original variable, you can't—you only change the copy.
- **The Solution**: Pass the **address** (the pointer) by value. Copying a 64-bit address is cheap, and it allows the function to "reach back" and modify the original memory.

### Special Pointer Types

- **`void \*` (Generic Pointer)**: Can point to any data type. It must be cast before dereferencing.
- **Function Pointers**: Pointers that store the starting address of executable code.
  - *Mapping*: Useful for callbacks and implementing polymorphism in C.
- **`NULL` Pointer**: A pointer that points to nothing (usually address `0`).
  - **Note**: In some systems, address `0` can actually be written to, but in modern OSs, dereferencing `NULL` triggers a **Segmentation Fault**.

------

## 3. Arrays: Primitive and Powerful

In C, arrays are very "thin" abstractions over memory.

### Initialization

C

```
int arr[2];           // Allocated but uninitialized (contains garbage)
int arr[] = {111, 222}; // Size inferred as 2
```

### Pointer Arithmetic

The name of an array is essentially a constant pointer to its first element.

- `arr[0]` is exactly the same as `*arr`.
- `arr[i]` is exactly the same as `*(arr + i)`.

**The Pitfall**: The array name is **not** a variable. You cannot do `arr = p;`. It is a label for a specific memory location.

### Array Safety & Bounds

C arrays are **primitive**:

- **No Bounds Checking**: C will not stop you from accessing `arr[10]` on a size-5 array. This leads to **Buffer Overflows**.
- **Heap Buffer Overflow**: Occurs when you write past the allocated space in dynamically managed memory (`malloc`).
- **Decay**: When passed to a function, an array "decays" into a pointer. The function loses information about the array's size.
  - *Best Practice*: Always pass the size explicitly: `void func(int *arr, size_t size)`.

------

## 4. Strings: The Null-Terminated Sequence

In C, a string is simply a `char` array that ends with a special byte: `\0` (the null terminator).

C

```
char s[] = "Hi"; // Memory: ['H', 'i', '\0']
```

**Why the `\0`?** Since arrays don't store their own size, functions like `printf` or `strlen` keep reading memory until they hit a `0` byte. If you forget the terminator, the program will keep reading into adjacent memory until it crashes.

------

## 5. Structs and Memory Alignment

### Structs

A `struct` allows you to group different data types into a single block of memory.

### Padding & Alignment

Modern machines prefer data to be aligned (e.g., a 4-byte `int` should start at an address divisible by 4).

- **Padding**: The compiler inserts "invisible" empty bytes between struct members to ensure alignment.
- **Ordering Matters**: To minimize wasted space, place the largest members first in the struct definition.

### Endianness

How multi-byte data (like an `int`) is stored in bytes:

- **Little Endian**: The "Little End" (least significant byte) comes first. (Used by x86/Intel).
- **Big Endian**: The "Big End" (most significant byte) comes first. (Common in networking).

------

## 6. Common Memory Errors

### Segmentation Fault (Segfault)

Occurs when your program tries to access memory that "doesn't belong to it," such as dereferencing a `NULL` pointer or writing to read-only memory.

### Bus Error

Occurs when the hardware cannot physically execute the memory access, often due to **unaligned access** (trying to read a 4-byte int from an odd-numbered address on a strict architecture).

### Scope and Lifetime

- **Stack Allocation**: Local arrays are only valid while the scope (the `{ }` block) is active.
- **The Trap**: Returning a pointer to a local array from a function is a fatal error, as that memory is reclaimed once the function returns.

------

## 📝 Knowledge Checklist

- [ ] Do you understand that `p + 1` moves the pointer by `sizeof(type)` bytes, not just 1 byte?
- [ ] Can you explain why `sizeof(my_struct)` might be larger than the sum of its members?
- [ ] Do you know how to calculate the length of a string without using `strlen`?
- [ ] Can you identify the difference between `char *s = "Hello"` (read-only) and `char s[] = "Hello"` (writable)?
