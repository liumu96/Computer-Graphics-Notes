# The Basics of Matrix

## Basics

$$
\begin{align}
(AB)^{-1}     &= B^{-1}A^{-1} \\
(ABC...)^{-1} &= ...C^{-1}B^{-1}A^{-1} \\
(A^T)^{-1}    &= (A^{-1})^T \\
(A+B)^T       &= A^T + B^T \\
(AB)^T        &= B^TA^T \\
(ABC...)^T    &= ...C^TB^TA^T \\
(A^H)^{-1}    &= (A^{-1})^H \\
(A+B)^H       &= A^H + B^H \\
(AB)^H        &= B^HA^H \\
(ABC...)^H    &= ...C^HB^HA^H \\
\end{align}
$$

### Trace

$$
\begin{align}
Tr(A) = \sum_i A_{ii} \\
Tr(A) = \sum_i \lambda_i, \text{ } \lambda_i = eig(A) \\
Tr(A) = Tr(A^T) \\
Tr(AB) = Tr(BA) \\
Tr(A+B) = Tr(A) + Tr(B) \\
Tr(ABC) = Tr(BCA) = Tr(CAB) \\
a^Ta = Tr(aa^T)
\end{align}
$$

### Determinant

Let $A$ be an $n\times n$ matrix: 🍀

$$
\begin{align}
det(A) = \prod_i \lambda_i, \text{ } \lambda_i = eig(A) \\
det(cA) = c^ndet(A), \text{if } A \in \mathbb R^{n\times n} \\
det(A^T) = det(A) \\
det(AB) = det(A)det(B) \\
det(A^{-1}) = 1 / det(A) \\
det(A^n) = det(A)^n \\
det(I + uv^T) = 1 + u^Tv \\
\end{align}
$$

For $n = 2$:

$$
\begin{align}
det(I+A) = 1 + det(A) + Tr(A)
\end{align}
$$

For $n = 3$:

$$
\begin{align}
det(I+A) = 1 + det(A) + Tr(A) + \frac{1}{2} Tr(A)^2 - \frac{1}{2} Tr(A^2)
\end{align}
$$

For $n = 4$:

$$
\begin{align}
det(I+A) = 1 + det(A) + Tr(A) + \frac{1}{2} + Tr(A)^2 - \frac{1}{2} Tr(A^2) + \frac{1}{6} Tr(A)^3 - \frac{1}{2} Tr(A)Tr(A^2) + \frac{1}{3} Tr(A^3)
\end{align}
$$

For small $\epsilon$, the following approximation holds:

$$
\begin{align}
det(I+\epsilon A) \cong 1 + det(A) + Tr(A) + \epsilon Tr(A) + \frac{1}{2}\epsilon^2 Tr(A)^2 - \frac{1}{2} \epsilon^2Tr(A^2)
\end{align}
$$

### The Special Case 2x2

Consider the matrix $A$

$$
A = \begin{bmatrix}
A_{11} & A_{12} \\
A_{21} & A_{22}
\end{bmatrix}
$$

🍀 Determinant and trace:

$$
\begin{align}
det(A) = A_{11}A_{22} - A_{12}A_{21} \\
Tr(A) = A_{11} + A_{22}
\end{align}
$$

Eigenvalues

$$
\lambda^2 - \lambda \cdot Tr(A) + det(A) = 0 \\
\lambda_1 = \frac{Tr(A) + \sqrt{Tr(A)^2 - 4det(A)}}{2} \text{ }\text{ } \lambda_2 = \frac{Tr(A) - \sqrt{Tr(A)^2 - 4det(A)}}{2} \\
\lambda_1 + \lambda_2 = Tr(A) \text{ }\text{ } \lambda_1\lambda_2 = det(A)
$$

EigenVectors:

$$
v_1 \propto \begin{bmatrix}
A_{12} \\
\lambda_1 - A_{11}
\end{bmatrix} \text{ }\text{ } v_2 \propto \begin{bmatrix}
A_{12} \\
\lambda_2 - A_{11}
\end{bmatrix}
$$

Inverse:

$$
\begin{align}
A^{-1} = \frac{1}{det(A)}\begin{bmatrix}
A_{22} & -A_{12} \\
-A_{21} & A_{11}
\end{bmatrix}
\end{align}
$$
