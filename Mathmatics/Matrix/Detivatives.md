# Derivatives

This section is covering differentiation of a number of expressions with respect to
a matrix $X$.

The basic assumptions can be written in a formula as:

$$
\begin{align}
\frac{\partial X_{kl}}{\partial X_{ij}} = \delta_{ik}\delta_{lj}
\end{align}
$$

The following rules are general and very useful when deriving the differential of
an expression:

$$
\begin{align}
\partial A = 0 \text{ }A\text{ is a constant } \\
\partial (\alpha X) = \alpha\partial X \\
\partial (X + Y) = \partial X + \partial Y \\
\partial (Tr(X)) = Tr(\partial A) \\
\partial (XY) = (\partial X)Y + X(\partial Y) \\
\partial (X \circ Y) =  (∂X) \circ Y + X \circ (∂Y) \\
∂(X ⊗ Y) = (∂X) ⊗ Y + X ⊗ (∂Y) \\
∂(X^{−1}) = −X^{−1}(∂X)X^{−1} \\
∂(det(X)) = Tr(adj(X)∂X) \\
∂(det(X)) = det(X)Tr(X^{−1}∂X) \\
∂(ln(det(X))) = Tr(X^{−1}∂X)
∂X^T = (∂X)^T \\
∂X^H = (∂X)^H
\end{align}
$$

## Derivatives of a Determinant

### General form

$$
\begin{align}
\frac{∂det(Y)}{∂x} = det(Y)Tr\left[Y^{−1}\frac{∂Y}{∂x}\right] \\
\sum_k \frac{∂det(X)}{∂X_{ik}}X_{jk} = \delta_{ij}det(X)
\end{align}
$$
