# Large Steps in Cloth Simulation

Authors: David Baraff; Andrew Witkin

## Summary

- **Objective:** The paper addresses the challenge of efficient cloth simulation, proposing a **quasi-implicit** integration scheme for large time steps while maintaining stability.

- **Main Contribution:** Introduces a novel quasi-implicit method allowing for larger time steps in cloth simulation, significantly reducing computational costs compared to traditional explicit methods.

- **Cloth Representation:** Models cloth as a **particle mesh**, leveraging elastic forces to simulate dynamic behavior.

- **Advantages:** Enables real-time or interactive simulations, making it particularly useful for applications where computational efficiency is critical.

- **Impact:** The paper has had a substantial impact on cloth simulation, influencing subsequent research in physics-based animation and simulation.

## Introduction

Physically-based cloth animation is formulated as a time-varying partical differential equation which, after discretization, is numerically solved as an ordinary differential equation:

$$
\begin{align}
\ddot {\mathbf x} = \mathbf M^{-1}(-\frac{\partial E}{\partial {\mathbf x}} + \mathbf F)
\end{align}
$$

In this equation the vector ${\mathbf x}$ and diagonal matrix $\mathbf M$ represent the geometric state and mass distribution of the cloth, $E$ — a scalar function of ${\mathbf x}$ - yields the cloth’s internal energy, and $\mathbf F$ (a function of $\mathbf x$ and $\mathbf {\dot x}$) describes other forces (air-drag, contact and constraint forces, internal damping, etc.) acting on the cloth.

In this paper, we demonstrate that implicit methods for cloth overcome the performance limits inherent in explicit simulation methods. We describe a simulation system that uses a triangular mesh for cloth surfaces, eliminating topological restrictions of rectangular meshes, and a simple but versatile formulation of the internal cloth energy forces.

- A key step in our simulation process is the solution of an $O(n) \times O(n)$ sparse linear system, which arises from the implicit integration method.
- Regardless of simulation size, we treat all forces as part of the implicit formulation.

## Simulation Overview

In this paper, the simulator models cloth as a triangular mesh of particles. Given a mesh of $n$ particles, the position in world-space of the $i^{th}$ particle is $\mathbf x_i \in \mathbb R^3$. The geometric state of all the particles is simply $\mathbf x \in \mathbb R^{3n}$. The same component notation applies to forces: a force $\mathbf f \in \mathbb R^{3n}$ acting on the cloth exerts a force $\mathbf f_i$ on the $i^{th}$ particle. Real-world cloth is cut from flat sheets of material and tends to resist deformations away from this initial flat state (creases and pleats not withstanding). We capture the rest state of cloth by assigning each particle an unchanging coordinate $(u_i, v_i)$ in the plane.

**Handling Collisions with Solid Objects:**

- Solid objects are modeled as triangularly faced polyhedra.
- Each face of the polyhedron has an associated thickness and orientation.
- The simulation prevents cloth particles from interpenetrating solid objects.
- A simple test is used: if a cloth particle is found to be sufficiently near a face of the solid object and on the wrong side of the face, a collision is deemed to have occurred.
- Particles involved in collisions become subject to a contact constraint.

**Handling Collisions between Cloth and Itself:**

- Cloth/cloth collisions are detected in two ways:
  - Face-vertex collisions: Cloth particles colliding with triangles.
  - Edge/edge collisions: Collisions between portions of the cloth.
- Close proximity or actual intersection of cloth with itself triggers the initiation of contact handling.

**Considerations for High Relative Velocities:**

- In cases of extremely high relative velocities, the simple test for solid object collisions may miss some collisions.
- To address this, the simulation can analytically check for intersection between previous and current positions, ensuring that no collisions are overlooked.

### Energy and Forces

The internal cloth forces, including **shear and bend forces**, are fundamental components modeled on a **per-triangle** and **per-edge** basis, respectively.
The **stretch force**, resisting in-plane stretching or compression. is also formulated **per-triangle**. While the shear and bend force stiffness coefficients depend on the material being simulated, the stretch coefficient is essentially the same (large) value for all simulations.

**Damping forces** are introduced to subdue oscillations related to stretching, shearing, and bending motions of the cloth, contributing to the stability of the simulation. Additionally, **external forces** such as air drag, gravity, and user-generated mouse forces for interactive simulations are considered. Cloth-to-cloth contacts result in strong repulsive **linear-spring forces** between cloth particles.

Combining all forces into a net force vector $\mathbf f$, the acceleration $\ddot{\mathbf x_i}$ of the $i^{th}$ particle is simply $\ddot {\mathbf x_i } = \mathbf f_i / m_i$, where $m_i$ is the $i^{th}$ particle's mass. The mass $m_i$ is determined by summing one third the mass of all triangles containing the $i^{th}$ particle. (A triangle's mass is the product of the cloth's density and the triangle's fixed area in the $uv$ coordinate system.) Defining the diagonal mass matrix $\mathbf M \in \mathbb R^{3n\times 3n}$ by $\diag(\mathbf M) = (m_1, m_1, m_1, m_2, m_2, m_2, ..., m_n, m_n, m_n)$, we can write simply that:

$$
\begin{align}
\ddot {\mathbf x} = \mathbf M^{-1}\mathbf f(\mathbf x, \dot{\mathbf x})
\end{align}
$$

### Sparse Matrices

The use of an implicit integration method generates large unbanded sparse linear systems. A modified conjugate gradient (CG) iterative method is introduced to solve these systems.

The sparsity of the matrix generated by the implicit integrator is best represented in block-fashion: for a system with $n$ particles, we deal with an $n \times n$ matrix, whose non-zero entries are represented as dense $3 \times 3$ matrices of scalars. The matrix is represented as an array of $n$ rows; each row is a **linked list** of the non-zero elements of that row, to accommodate possible run-time changes in the sparsity pattern, due to cloth/cloth contact. The (dense) vectors that are multiplied against this matrix are stored simply as n element arrays of threecomponent vectors.

### Constraints

Constraints are either user-defined (the time period that a constraint is active is user-controlled) or automatically generated, in the case of contact constraints between cloth and solids. We use **penalty forces** to deal with cloth/cloth contacts: whenever a particle is near a cloth triangle or is detected to have passed through a cloth triangle, we add a stiff spring with damping to pull the particle back to the correct side of the triangle.

## Implicit Integration

Given the known position $\mathbf x(t_0)$ and velocity $\mathbf {\dot x}(t_0)$ of the system at time $t_0$, our goal is to determine a new position $\mathbf x(t_0 + h)$ and velocity $\mathbf {\dot x}(t_0 + h)$ at time $t_0 + h$. To compute the new state and velocity using an implicit technique, we must first transform equation (2) into a first-order differential equation. This is accomplished simply by defining the system’s velocity $\mathbf v$ as $\mathbf v = \dot {\mathbf x}$ and then writing

$$
\begin{align}
\frac{d}{dt}\begin{pmatrix}
\mathbf x \\
\dot {\mathbf x}
\end{pmatrix} = \frac{d}{dt}\begin{pmatrix}
\mathbf x \\
\mathbf v
\end{pmatrix} = \begin{pmatrix}
\mathbf v \\
\mathbf M^{-1}\mathbf f(\mathbf x, \mathbf v)
\end{pmatrix}
\end{align}
$$

To simplify notation, we will define $\mathbf x_0 = \mathbf x(t_0)$ and $\mathbf v_0 = \mathbf v(t_0)$.We also define $\Delta\mathbf x = \mathbf x(t_0 + h) - \mathbf x(t_0)$ and $\Delta\mathbf v = \mathbf v(t_0 + h) - \mathbf v(t_0)$.

The **explicit forward** Euler method applied to equation (3) approximates $\Delta\mathbf x$ and $\Delta\mathbf v$ as

$$
\begin{pmatrix}
\Delta\mathbf x \\
\Delta\mathbf v
\end{pmatrix} = h \begin{pmatrix}
\mathbf v_0 \\
\mathbf M^{-1}\mathbf f_0
\end{pmatrix}
$$

where the force $\mathbf f_0$ is defined by $\mathbf f_0 = \mathbf f(\mathbf x_0, \mathbf v_0)$. As previously discussed, the step size $h$ must be quite small to ensure stability when using this method.

The **implicit backward** Euler method appears similar at first: $\Delta\mathbf x$ and $\Delta\mathbf v$ are approximated by

$$
\begin{align}
\begin{pmatrix}
\Delta\mathbf x \\
\Delta\mathbf v
\end{pmatrix} = h \begin{pmatrix}
\mathbf v_0 + \Delta \mathbf v \\
\mathbf M^{-1}\mathbf f(\mathbf x_0 + \Delta \mathbf x, \mathbf v_0 + \Delta \mathbf v)
\end{pmatrix}
\end{align}
$$

The forward method requires only an evaluation of the function $\mathbf f$ but the backward method requires that we solve for values of $\Delta\mathbf x$ and $\Delta\mathbf v$ that satisfy equation (4). Equation (4) is a nonlinear equation: rather than solve this equation exactly (which would require iteration) we apply a **Taylor series expansion** to $\mathbf f$ and make the firstorder approximation:

$$
\mathbf f(\mathbf x_0 + \Delta \mathbf x, \mathbf v_0 + \Delta \mathbf v) = \mathbf f_0 + \frac{\partial \mathbf f}{\partial \mathbf x}\Delta \mathbf x + \frac{\partial \mathbf f}{\partial \mathbf v}\Delta \mathbf v
$$

In this equation, the derivative $\frac{\partial \mathbf f}{\partial \mathbf x}$ is evaluated for the state $(\mathbf x_0, \mathbf v_0)$ and similarly for $\frac{\partial \mathbf f}{\partial \mathbf v}$. Substituting this approximation into equation (4) yields the linear system

$$
\begin{align}
\begin{pmatrix}
\Delta\mathbf x \\
\Delta\mathbf v
\end{pmatrix} = h \begin{pmatrix}
\mathbf v_0 + \Delta \mathbf v \\
\mathbf M^{-1} (\mathbf f_0 + \frac{\partial \mathbf f}{\partial \mathbf x}\Delta \mathbf x + \frac{\partial \mathbf f}{\partial \mathbf v}\Delta \mathbf v)
\end{pmatrix}
\end{align}
$$

Taking the bottom row of equation (5) and substituting $\Delta \mathbf x = h(\mathbf v_0 + \Delta \mathbf v)$ yields

$$
\Delta\mathbf v = h \mathbf M^{-1} (\mathbf f_0 + \frac{\partial \mathbf f}{\partial \mathbf x}h(\mathbf v_0 + \Delta \mathbf v) + \frac{\partial \mathbf f}{\partial \mathbf v}\Delta \mathbf v)
$$

Letting $\mathbf I$ denote the identity matrix, and regrouping, we obtain

$$
\begin{align}
(\mathbf I - h \mathbf M^{-1}\frac{\partial \mathbf f}{\partial \mathbf v} - h^2\mathbf M^{-1}\frac{\partial \mathbf f}{\partial \mathbf x})\Delta\mathbf v = h \mathbf M^{-1} (\mathbf f_0 + h\frac{\partial \mathbf f}{\partial \mathbf x}\mathbf v_0)
\end{align}
$$

which we then solve for $\Delta\mathbf v$. Given $\Delta\mathbf v$, we trivially compute $\Delta\mathbf x = h(\mathbf v_0 + \Delta \mathbf v)$.

Thus, the backward Euler step consists of evaluating $\mathbf f_0, \partial \mathbf f /\partial \mathbf x$ and $\partial \mathbf f /\partial \mathbf v$; forming the system in equation (6); solving the system for $\Delta\mathbf v$; and then updating $\Delta\mathbf x$ and $\Delta\mathbf v$.

## Forces

Cloth’s material behavior is customarily described in terms of a **scalar potential energy** function $ E(\mathbf x)$; the force $\mathbf f$ arising from this energy is $\mathbf f = - \partial E /\partial \mathbf x$. Equation (6) requires both the vector $\mathbf f$ and the matrix $\partial \mathbf f /\partial \mathbf x$. A better approach is decompose E into a sum of sparse energy functions; that is, to write $E(\mathbf x) = \sum_{\alpha} E_{\alpha}(\mathbf x)$ where each $E_{\alpha}$ depends on as few elements of $\mathbf x$—as few particles—as possible.

However, even decomposing $E$ into sparse energy functions is not enough. Energy functions are an undesirable starting point because sensible damping functions cannot be derived from energy functions. Instead, we define internal behavior by formulating a vector condition $\mathbf C(\mathbf x)$ which we want to be zero, and then defining the associated energy as $\frac{k}{2}\mathbf C(\mathbf x)^T\mathbf C(\mathbf x)$ where $k$ is a stiffness constant.

### Forces and Force Derivatives

Given a condition $\mathbf C(\mathbf x)$ which we want to be zero, we associate an energy function $E_C$ with $\mathbf C$ by writing $E_C(\mathbf x) = \frac{k}{2}\mathbf C(\mathbf x)^T\mathbf C(\mathbf x) $ where $k$ is a stiffness constant of our choice. Assuming that $\mathbf C$ depends on only a few particle, $\mathbf C$ gives rise to a sparse force vector $\mathbf f$. View the vector $\mathbf f$ in block form; each element $\mathbf f_i$ is a vector in $\mathbb R^3$. For each particle $i$ that $\mathbf C$ depends on,

$$
\begin{align}
\mathbf f_i = -\frac{\partial E_C}{\partial \mathbf x_i} = -k \frac{\partial \mathbf C(\mathbf x)}{\partial \mathbf x_i}\mathbf C(\mathbf x)
\end{align}
$$

all the other elements of $\mathbf f$ are zero.

Similarly, the derivative of $\mathbf f$ is also sparse. Defining the derivative matrix $\mathbf K = \partial \mathbf f/\partial \mathbf x$, the nonzero entries of $\mathbf K$ are $\mathbf K_{ij}$ for all pairs of particles $i$ and $j$ that $\mathbf C$ depends on. Again, we treat $\mathbf K$ in block fashion: $\mathbf K \in \mathbb R^{3x\times 3n}$,so an element $\mathbf K_{ij}$ is a $3\times3$ matrix. From equation (7), we have

$$
\begin{align}
\mathbf K_{ij} = \frac{\mathbf f_i}{\partial \mathbf x_j} = -k(\frac{\partial \mathbf C(\mathbf x)}{\partial \mathbf x_i}\frac{\partial \mathbf C(\mathbf x)^T}{\partial \mathbf x_j} + \frac{\partial^2 \mathbf C(\mathbf x)}{\partial \mathbf x_i \partial \mathbf x_j}\mathbf C(\mathbf x) )
\end{align}
$$

Additionally, since $\mathbf K_{ij}$ is a second derivative—that is, $\mathbf K_{ij} = \partial \mathbf f_i / \partial \mathbf x_j = \partial^2 E/ \partial x_i\partial x_j$—we have $\mathbf K_{ij} = \mathbf K_{ji}^T$ so $\mathbf K$ is symmetric. Note that since $\mathbf C$ does not depend on $\mathbf v$, the matrix $\partial \mathbf f / \partial \mathbf v$ is zero.

### Stretch Forces

### Shear and Bend Forces

### Additional Forces

### Damping

## Constraints

### Reduced Coordinates

### Mass Modification

### Implementation

### The Modified Conjugate Gradient Method

### Determing the Constraint Forces

## Collisions

### Constraint Initiation

### Position Alteration

## Adaptive Time Stepping
