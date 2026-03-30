# 🔢 Number Representation: From Analog to Digital

> **Core Concept**: In a computer, everything is a bit. Whether it is a pixel in a VLM (Vision-Language Model), a control signal for a robotic arm, or a financial record, it must be mapped to a binary representation.

------

## 1. The Bridge: Analog to Digital Conversion (ADC)

Physical signals (sound, light, voltage) are **Analog**—continuous in both time and amplitude. To process them, we must convert them into **Digital** form.

### The Two-Step Process:

1. **Sampling (Discretization in Time)**:
   - Measuring the analog signal at discrete time intervals.
   - *Nyquist-Shannon Theorem*: To perfectly reconstruct a signal, the sampling rate must be at least twice the highest frequency of the signal.
2. **Quantization (Discretization in Value)**:
   - Mapping the continuous amplitude of the sample to a finite set of digital values (bits).
   - **Quantization Error**: The difference between the actual analog value and the nearest digital representative.

------

## 2. Information Theory: Bits Represent Everything

A **Bit** (Binary Digit) is the smallest unit of information ($0$ or $1$).

- **The Power of $N$**: With $n$ bits, you can represent $2^n$ unique items.
- **Universal Encoding**:
  - **Numbers**: Integers, Floating point.
  - **Text**: ASCII, Unicode (UTF-8).
  - **Instructions**: OpCodes in an ISA.

------

## 3. Number vs. Numeral

- **Number**: An abstract mathematical concept representing a quantity (e.g., the "concept" of seven).
- **Numeral**: A symbol or string of symbols used to represent a number (e.g., `7`, `VII`, `0111`, `Seven`).

------

## 4. Positional Notation & Bases

The value of a digit depends on its **position** relative to the radix point.

### Base 10: Decimal

Standard human representation using digits $\{0, 1, ..., 9\}$.

- **Example**: $123_{10} = (1 \times 10^2) + (2 \times 10^1) + (3 \times 10^0)$

### Base 2: Binary

The native language of hardware (Voltage High/Low). Digits: $\{0, 1\}$.

- **Example**: $1011_2 = (1 \times 2^3) + (0 \times 2^2) + (1 \times 2^1) + (1 \times 2^0) = 11_{10}$

### Base 16: Hexadecimal

A shorthand for binary. One Hex digit represents exactly **4 bits**. Digits: $\{0-9, A-F\}$.

- **Why Hex?** It is much easier for humans to read `0xDEADBEEF` than a string of 32 ones and zeros.

------

## 5. Data Units

| **Unit**   | **Size**   | **Description**                                           |
| ---------- | ---------- | --------------------------------------------------------- |
| **Bit**    | 1 bit      | A single $0$ or $1$.                                      |
| **Nibble** | **4 bits** | Half a byte; exactly one Hex digit.                       |
| **Byte**   | 8 bits     | The standard addressable unit of memory.                  |
| **Word**   | 32/64 bits | The natural size of data used by the CPU (ISA-dependent). |

------

## 6. Integer Representation

### Unsigned Integers

Used for values that are never negative (e.g., memory addresses).

- **Range for $n$ bits**: $[0, 2^n - 1]$
- **Example (8-bit)**: $0$ to $255$.

### Signed Integers: Two's Complement

The standard way to represent negative numbers in modern computers.

**Why Two's Complement?**

1. Only one representation for zero (unlike Sign-Magnitude).
2. Hardware addition and subtraction can use the same circuit.

#### How to find the Negative ($X \rightarrow -X$):

1. **Invert** all bits ($0 \rightarrow 1, 1 \rightarrow 0$).
2. **Add 1** to the result.

**The Weight of the MSB**: In an $n$-bit Two's Complement number, the Most Significant Bit (MSB) has a negative weight of $-2^{n-1}$.

$$Value = -d_{n-1}2^{n-1} + \sum_{i=0}^{n-2} d_i 2^i$$

- **Range for $n$ bits**: $[-2^{n-1}, 2^{n-1} - 1]$
- **Example (8-bit)**: $-128$ to $+127$.

------

## 7. Arithmetic Errors: Overflow

**Overflow** occurs when the result of an arithmetic operation is too large (or too small) to fit in the allocated number of bits.

- **Unsigned Overflow**: Occurs when there is a **carry out** of the MSB during addition.
- **Signed (Two's Complement) Overflow**: Occurs when:
  - `Positive + Positive = Negative`
  - `Negative + Negative = Positive`
  - *Note: Adding numbers with different signs can never overflow.*

------

## 💻 Quick Reference Table

| **Decimal** | **Binary (4-bit)** | **Hex** | **Note**                   |
| ----------- | ------------------ | ------- | -------------------------- |
| 0           | `0000`             | `0x0`   |                            |
| 7           | `0111`             | `0x7`   | Max positive (Signed)      |
| 8           | `1000`             | `0x8`   | Min negative (Signed: -8)  |
| 10          | `1010`             | `0xA`   |                            |
| 15          | `1111`             | `0xF`   | Max Unsigned / -1 (Signed) |
