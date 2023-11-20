# XPBD: Position-Based Simulation of Compliant Constrained Dynamics

**Key words**: physics simulation, constrained dynamics, position based dynamics

## Background

Recall the core of the PBD algorithm: PBD can be thought of as a semi-implicit integration step using the $St\ddot omer-Verlet$ method, followed by a number of constraint projection steps.The projection step is performed using local linearizations of each constraint function with mass weighted corrections. The main step in PBD’s constraint solver is the calculation of the per-constraint position delta, given by:

$$
\begin{align}
\Delta x = k_js_j\mathbf M^{-1}\nabla C_j(x_i)
\end{align}
$$

Here the subscript $i$ denotes the iteration index, $j$ is the constraint index, and $k ∈ [0, 1]$ is the constraint stiffness applied simply as a multiplier on each constraint correction. The scaling factor, $s$, is given by the following equation, derived from a single Newton step of the constraint function:

$$
\begin{align}
s_j = \frac{-C_j(x_i)}{\nabla C_j M^{-1}\nabla C_j^T}
\end{align}
$$

An undesirable side-effect of simply scaling the position change by $k$ is that the effective constraint stiffness is now dependent on both the time step and the number of constraint projections performed.

$M\ddot uller$ et al. [2007] attempted to address this by exponential scaling of the stiffness coefficient. However this does not take into account the time step, and does not converge to a well-defined solution in the presence of multiple constraints.

In the following section we develop a scheme using regularized constraints that have a direct correspondence to well-defined energy potentials, and show how to solve these in a time step and iteration count independent manner.

## Algorithm

Starting with Newton’s equations of motion subject to forces derived from an energy potential $U (x)$:

$$
\begin{align}
M\ddot x = -\nabla U^T(x)
\end{align}
$$

Here $x = [x_1, x_2, · · · , x_n]^T$ is the system state. In PBD this is often simply particle positions, however it may represent any generalized coordinate model, e.g.: rigid body transforms.

We perform an implicit position-level time discretization of our equations of motion (3), where the superscript $n$ denotes the time step index:

$$
\begin{align}
M(\frac{x^{n+1} - 2x^n + x^{n-1}}{\Delta t^2}) = -\nabla U^T(x^{n+1})
\end{align}
$$

The energy potential $U (x)$ may be further specified in terms of a vector of constraint functions $C = [C_1(x), C_2(x), · · · , C_m(x)]^T$ as

$$
\begin{align}
U(x) = \frac{1}{2}C(x)^T\alpha ^{-1} C(x)
\end{align}
$$

where $α$ is a block diagonal compliance matrix corresponding to inverse stiffness. The force from an elastic potential is then given by the **negative gradient** of $U$ with respect to $x$,

$$
\begin{align}
f_{elastic} = -\nabla_x U^T = \nabla C(x)^T\alpha ^{-1} C(x)
\end{align}
$$

We convert this to a compliant constraint formulation and decomposing the force into its direction and scalar components by introducing the Lagrange multiplier,

$$
\begin{align}
\lambda_{elastic} = -\hat \alpha ^{-1} C(x)
\end{align}
$$

Here $λ_{elastic} = [λ_1, λ_2, · · · λ_m]^T$ is a vector of constraint multipliers. Note that we have included the time step from the left-hand side of Equation (4) by folding it into our compliance matrix and defining $\hat α= \frac{α}{∆t^2} $.

Substituting in our expression for $λ$ we have the discrete constrained equations of motion:

$$
\begin{align}
M(x^{n+1} - \hat x) - \nabla C(x^{n+1})^T\lambda ^{n+1} &= 0 \\
C(x^{n+1}) + \hat \alpha \lambda^{n+1} &= 0
\end{align}
$$

where $ \hat x = 2x^n − x^{n−1} = x^n + ∆tv^n$ is called the predicted, or inertial, position.

To solve this non-linear system we design a fixed point iteration based on Newton’s method. In the following, we omit the time step superscript $(n + 1)$ to emphasize the per-iteration unknown, indicated by the subscript $(i + 1)$.

We label equations (8, 9) as $\mathbf g$ and $\mathbf h$ respectively. Our goal is to find an $x$ and $λ$ that satisfies:

$$
\begin{align}
\mathbf g(x, \lambda) &= 0 \\
\mathbf h(x, \lambda) &= 0
\end{align}
$$

Linearizing equations (10, 11) we obtain the following linear Newton subproblem:

$$
\begin{align}
\begin{bmatrix}
K & \nabla C^T(x_i) \\
\nabla C(x_i) & \hat \alpha \\
\end{bmatrix}
\begin{bmatrix}
\Delta x \\
\Delta \lambda \\
\end{bmatrix} = -\begin{bmatrix}
g(x_i, \lambda_i) \\
h(x_i, \lambda_i) \\
\end{bmatrix}
\end{align}
$$

where $K = \frac{∂g}{∂x}$ . This system may be solved for $∆x$ and $∆λ$ and the positions and multipliers updated accordingly:

$$
\begin{align}
\lambda_{i+1} = \lambda_i + \Delta \lambda \\
x_{i+1} = x_i + \Delta x \\
\end{align}
$$

This is a fixed-point iteration that satisfies our implicit equations of motion (8, 9) for any sequence such that $|x_{i+1} − x_i| → 0$ and $|λ_{i+1} − λ_i| → 0$.

In particular, we would like to avoid computing $K$, which requires the evaluation of constraint Hessians.

First, we use the approximation that $K ≈ M$. This omits the geometric stiffness and constraint Hessian terms, and introduces a local error on the order of $O(∆t^2)$. We note that this approximation may change the rate of convergence, but it does not change the global error or solution to the fixed-point iteration. Specifically, it can be seen as a quasi-Newton method.

Next, we assume that $g(x_i, λ_i) = 0$. This assumption is justified by noting that it is trivially true for the first Newton iteration when initialized with $x_0 =  \hat x$ and $λ_0 = 0$. In addition, if the constraint gradients change slowly then it will remain small, and will go to zero when they are constant. Furthermore, the modified linear system now corresponds to the optimality conditions for a mass-weighted minimization to the constraint manifold starting from the current iterate $x_i$.

Including these approximations, our updated linear subproblem is given by

$$
\begin{align}
\begin{bmatrix}
M & \nabla C^T(x_i) \\
\nabla C(x_i) & \hat \alpha \\
\end{bmatrix}
\begin{bmatrix}
\Delta x \\
\Delta \lambda \\
\end{bmatrix} = -\begin{bmatrix}
0 \\
h(x_i, \lambda_i) \\
\end{bmatrix}
\end{align}
$$

we may then take the Schur complement with respect to $M$ to obtain the following reduced system in terms of the unknown $∆λ$:

$$
\begin{align}
[\nabla C(x_i)M^{-1}\nabla C(x_i)^T + \hat \alpha]\Delta \lambda = -C(x_i) - \hat \alpha \lambda_i
\end{align}
$$

The position update is then given directly by evaluating

$$
\begin{align}
\Delta x = M^{-1}\nabla C(x_i)^T\Delta \lambda
\end{align}
$$

### A Gauss-Seidel Update

We now make a connection back to PBD by considering a GaussSeidel solution for our linear system of equations (16). If we take a single constraint equation with index j, we can directly compute its Lagrange multiplier change by evaluating

$$
\begin{align}
\Delta \lambda_j = \frac{-C_j(x_i) - \hat \alpha_j\lambda_{ij}}{\nabla C_jM^{-1}\nabla C_j^T + \hat \alpha_j}
\end{align}
$$

This equation forms the core of our method. During constraint solving, we first calculate $∆λ_j$ for a single constraint, then we update the system positions and multipliers using Equations (13, 14).

## **Core XPBD Algorithm**

1. predict position $x^{\prime} = x^n + \Delta t v^n + \Delta t^2 M^{-1} f_{ext}(x^n)$
2.
3. initialize solve $x_0 = x^{\prime}$
4. initialize multipliers $λ_0 = 0$
5. while $i < solverIterations$ do
6. &emsp;&emsp;for all constraints do
7. &emsp;&emsp;&emsp;&emsp; compute $∆λ$ using Eq (18)
8. &emsp;&emsp;&emsp;&emsp; compute $∆x$ using Eq (17)
9. &emsp;&emsp;&emsp;&emsp; update $λ_{i+1} = λ_i + ∆λ$
10. &emsp;&emsp;&emsp;&emsp; update $x_{i+1} = x_i + ∆x$
11. &emsp;&emsp; end for
12. &emsp;&emsp; $i = i + 1; $
13. end while
14.
15. update positions $x^{n+1} = x_i$
16. update velocities $v^{n+1} = \frac{1}{\Delta t}(x^{n+1} - x^n)$

## Damping
