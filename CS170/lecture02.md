# 📚 Lecture: Fibonacci Algorithms & Complexity Analysis

This repository provides a comprehensive analysis of the Fibonacci sequence through various algorithmic lenses, ranging from naive recursion to logarithmic matrix powering, followed by a review of asymptotic notations.

------

## 1. Fibonacci Sequence Algorithms

The Fibonacci sequence is defined as:

$F_0 = 0, F_1 = 1, F_n = F_{n-1} + F_{n-2}$ for $n \ge 2$.

### 🔹 Algorithm 1: Naive Recursion

The most direct implementation based on the mathematical definition.

Python

```
def fib(n):
    if n <= 1: return n
    return fib(n-1) + fib(n-2)
```

- **Logic:** $T(n) = T(n-1) + T(n-2) + 1$ (for the addition).
- **Complexity:** $O(\phi^n)$, where $\phi \approx 1.618$.
- **FLOPs:** $T(n) \ge 2^{n/2}$. This is highly inefficient due to redundant calculations.

------

### 🔹 Algorithm 2: Iteration (Bottom-Up)

A linear approach using dynamic programming principles (storing only the last two values).

Python

```
def faster_fib(n):
    if n <= 1: return n
    a, b = 0, 1
    for i in range(2, n + 1):
        a, b = b, a + b
    return b
```

- **Logic:** Computes $F_n$ in a single pass.
- **Complexity:** $O(n)$ additions.
- **Space:** $O(1)$ (only two variables).

------

### 🔹 Algorithm 3: Matrix Powering

Using linear algebra to achieve logarithmic time.

The recurrence can be expressed as:

$$\begin{pmatrix} F_{n+1} \\ F_n \end{pmatrix} = \begin{pmatrix} 1 & 1 \\ 1 & 0 \end{pmatrix} \begin{pmatrix} F_n \\ F_{n-1} \end{pmatrix} \implies \begin{pmatrix} F_{n+1} \\ F_n \end{pmatrix} = \begin{pmatrix} 1 & 1 \\ 1 & 0 \end{pmatrix}^n \begin{pmatrix} F_1 \\ F_0 \end{pmatrix}$$

- **Optimization:** **Repeated Squaring** (Binary Exponentiation).
- **FLOPs:** $O(\log n)$ matrix multiplications.

------

### 🔹 Algorithm 4: Diagonalization (Binet's Formula)

Finding the closed-form solution by decomposing the matrix $A = \begin{pmatrix} 1 & 1 \\ 1 & 0 \end{pmatrix}$.

1. **Eigen-decomposition:** $A = Q \Lambda Q^{-1}$, where $\Lambda = \text{diag}(\lambda_1, \lambda_2)$.
2. **Eigenvalues:** $\lambda_1 = \frac{1+\sqrt{5}}{2}$ (Golden Ratio), $\lambda_2 = \frac{1-\sqrt{5}}{2}$.
3. **Powering:** $A^n = Q \Lambda^n Q^{-1}$.
4. **Result:** $F_n = \frac{\lambda_1^n - \lambda_2^n}{\sqrt{5}}$.

> [!NOTE]
>
> While this looks like $O(1)$, the precision required for floating-point calculations grows with $n$, making it practically $O(\log n)$ or more.

------

## 2. Comparison Summary

| **Algorithm**       | **FLOPs (Arithmetic Ops)** | **Real-world Time Complexity**                   |
| ------------------- | -------------------------- | ------------------------------------------------ |
| **Recursion**       | $O(\phi^n)$                | Exponential (Unusable for large $n$)             |
| **Iteration**       | $O(n)$                     | $O(n^2)$ (Considering $n$-bit addition)          |
| **Matrix Powering** | $O(\log n)$                | $O(n^{1.58} \log n)$ (Using fast multiplication) |

------

## 3. Asymptotic Notation

Let $f, g$ be functions mapping $\mathbb{Z}^+ \to \mathbb{Z}^+$:

- **Big-O ($O$):** $f = O(g)$ if $\exists c, n_0$ s.t. $f(n) \le cg(n)$ for all $n \ge n_0$. (Upper bound)
- **Little-o ($o$):** $f = o(g)$ if $\lim_{n \to \infty} \frac{f(n)}{g(n)} = 0$. (Loose upper bound)
- **Big-Omega ($\Omega$):** $f = \Omega(g)$ if $g = O(f)$. (Lower bound)
- **Little-omega ($\omega$):** $f = \omega(g)$ if $g = o(f)$. (Loose lower bound)
- **Theta ($\Theta$):** $f = \Theta(g)$ if $f = O(g)$ and $f = \Omega(g)$. (Exact/Tight bound)

> [!IMPORTANT]
>
> **Mathematical Rigor: Why $\limsup$?**
>
> Formally, $f(n) = O(g(n))$ is defined using the **limit superior** rather than a simple limit:
>
> $$\limsup_{n \to \infty} \frac{|f(n)|}{g(n)} < \infty$$
>
> This is crucial because a standard limit $\lim_{n \to \infty}$ might not exist if the function oscillates.
>
> **Example:** Consider $f(n) = n(2 + (-1)^n)$ and $g(n) = n$.
>
> - The ratio $\frac{f(n)}{g(n)} = 2 + (-1)^n$ oscillates between $1$ and $3$.
> - $\lim_{n \to \infty} (2 + (-1)^n)$ **does not exist**.
> - However, $\limsup_{n \to \infty} (2 + (-1)^n) = 3$, which is finite.
> - Thus, $f(n) = O(n)$ is mathematically valid under the $\limsup$ definition.

------

## Matrix Multi

Alg1 Navie

Alg2 Divide  & Conquer

## 4. Master Theorem (Divide & Conquer)

The Master Theorem provides a "cookbook" solution for recurrences of the form:

$$T(n) = aT(n/b) + f(n)$$

Compare $f(n)$ with $n^{\log_b a}$:

1. **Case 1:** If $f(n) = O(n^{\log_b a - \epsilon})$, then $T(n) = \Theta(n^{\log_b a})$.
2. **Case 2:** If $f(n) = \Theta(n^{\log_b a})$, then $T(n) = \Theta(n^{\log_b a} \log n)$.
3. **Case 3:** If $f(n) = \Omega(n^{\log_b a + \epsilon})$ and $af(n/b) \le cf(n)$, then $T(n) = \Theta(f(n))$.

------

### 🛡️ License

This project is licensed under the MIT License - see the [LICENSE](https://opensource.org/licenses/MIT) page for details.
