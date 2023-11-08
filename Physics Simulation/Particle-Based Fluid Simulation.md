## Particle-Based Fluid Simulation

### Overview

In this paper, the author propose **a particle-based method based on Smoothed Particle Hydrodynamics(SPH) to simulate fluids with free surfaces.**

- Deriving the force density fields directly from the Navier-Stokes equation
- Adding a term to model surface tension effects.

### Smoothed Particle Hydrodynamics

SPH is an **interpolation** method for particle systems. According to SPH, a scalar quantity $\bold A$ is interpolated at location $\bold r$ by a weighted sum of contributions from all particles:

$$
\begin{align}
A_S(\bold r) = \sum_j m_j\frac{A_j}{\rho_j}W(\bold r - \bold r_j, h)
\end{align}
$$

where $j$ iterates over all particles, $m_j$ is the mass of particle $j$, $\bold r_j$ its position, $ρ_j$ the density and $A_j$ the field quantity at $\bold r_j$. The function $W(\bold r, h)$ is called the smoothing kernel with core radius $h$.

Through substitution into Eqn. (1) we get for the density at location $\bold r$:

$$
\begin{align}
\rho_S(\bold r) = \sum_j m_j\frac{\rho_j}{\rho_j}W(\bold r - \bold r_j, h) = \sum_j m_jW(\bold r - \bold r_j, h)
\end{align}
$$

With the SPH approach, such derivatives only affect the smoothing kernel. The gradient of A is simply:

$$
\begin{align}
\nabla A_S(\bold r) = \sum_j m_j\frac{A_j}{\rho_j}\nabla W(\bold r - \bold r_j, h)
\end{align}
$$

while the Laplacian of A evaluates to

$$
\begin{align}
\nabla^2 A_S(\bold r) = \sum_j m_j\frac{A_j}{\rho_j}\nabla^2 W(\bold r - \bold r_j, h)
\end{align}
$$

**Notice**: When using SPH to derive fluid equations for particles, these equations are not guaranteed to satisfy certain physical principals such as symmetry of forces and conservation of momentum.

### Modelling Fluids with Particles

In the Eulerian(grid based) formulation, fluids are described by

- a velocity field $\bold v$
- a density filed $\rho$
- a pressure filed $p$

The first equation assures conservation of mass:

$$
\begin{align}
\frac{\partial \rho}{\partial t} + \nabla \cdot (\rho \bold v) = 0
\end{align}
$$

while the Navier-Stokes equation formulates conservation of momentum:

$$
\begin{align}
\rho(\frac{\partial \bold v}{\partial t} + \bold v\cdot\nabla \bold v) = -\nabla p + \rho \bold g + \mu \nabla^2 \bold v
\end{align}
$$

where $\bold g$ is an external force density field and $μ$ the viscosity of the fluid.

In this paper, we use particles instead of a stationary grid simplifies these two equations.

First, because the number of particles is constant and each particle has a constant mass, mass conservation is guaranteed and Eqn. (5) can be omitted completely.

Second, Second, the expression $\partial \bold v/\partial t + \bold v\cdot\nabla \bold v$ on the left hand side of Eqn. (6) can be replaced by the substantial derivative $D\bold v / D t$. Since the particles move with the fluid, the substantial derivative of the velocity field is simply the time derivative of the velocity of the particles meaning that the convective term $\bold v\cdot\nabla \bold v$ is not needed for particle systems.

There are three force density fields left on the right hand side of Eqn. (6) modeling pressure $(-\nabla p)$, external forces $(\rho \bold g)$ and viscosity $(\mu \nabla^2 \bold v)$. The sum of these force density fields $f = -\nabla p + \rho \bold g + \mu \nabla^2 \bold v$ determines the change of momentum $\rho\frac{D\bold v}{ Dt}$ of the particles on the left hand side. For the acceleration of particle $i$ :

$$
\begin{align}
\bold a_i = \frac{d \bold v_i}{d t} = \frac{\bold f_i}{\rho_i}
\end{align}
$$

where $\bold v_i$ is the velocity of particle $i$ and $f_i$ and $ρ_i$ are the force density field and the density field evaluated at the location of particle $i$, repectively.

#### Pressure

Application of the SPH rule described in Eqn. (1) to the pressure term $(-\nabla p)$ yields:

$$
\begin{align}
\bold f_i^{pressure} =-\nabla p(\bold r_i) = \sum_j m_j\frac{p_j}{\rho_j}\nabla W(\bold r_i - \bold r_j, h)
\end{align}
$$

Unfortunately, this force is not symmetric. To make Eqn.(7) symmetric, we suggest a very simple solution:

$$
\begin{align}
\bold f_i^{pressure} = \sum_j m_j\frac{p_i + p_j}{2\rho_j}\nabla W(\bold r_i - \bold r_j, h)
\end{align}
$$

Eqn. (2) yields the density at the location of the particle. Then, the pressure can be computed via the ideal gas state equation

$$
\begin{align}
p = k\rho
\end{align}
$$

In this paper, the author used a modified version

$$
\begin{align}
p = k(\rho - \rho_0)
\end{align}
$$

#### Viscosity

Application of the SPH rule to the viscosity term $(\mu \nabla^2 \bold v)$ again yields asymmetric forces:

$$
\begin{align}
\bold f_i^{viscosity} = \mu \nabla^2 \bold v(\bold r_a) = \mu\sum_j m_j\frac{\bold v_j}{\rho_j}\nabla^2 W(\bold r_i - \bold r_j, h)
\end{align}
$$

Since viscosity forces are only dependent on velocity differences and not on absolute velocities, there is a natural way to symmetrize the viscosity forces by using velocity differences:

$$
\begin{align}
\bold f_i^{viscosity} = \mu\sum_j m_j\frac{\bold v_j - \bold v_i}{\rho_j}\nabla^2 W(\bold r_i - \bold r_j, h)
\end{align}
$$

#### Surface Tension

Molecules in a fluid are subject to attractive forces from neighboring molecules. Inside the fluid these intermolecular forces are equal in all directions and balance each other. In contrast, the forces acting on molecules at the free surface are unbalanced. The net forces (i.e. surface tension forces) act in the direction of the surface normal towards the fluid. They also tend to minimize the curvature of the surface. The larger the curvature, the higher the force. Surface tension also depends on a tension coefficient $\sigma$ which depends on the two fluids that form the surface.

The surface of the fluid can be found by using an additional field quantity which is 1 at particle locations and 0 everywhere else. This field is called <em>color field</em> in the literature. For the smoothed color field we get:

$$
\begin{align}
c_S(\bold r) = \sum_j m_j\frac{1}{\rho_j} W(\bold r - \bold r_j, h)
\end{align}
$$

The gradient field of the smoothed color field:

$$
\begin{align}
\bold n = \nabla c_S
\end{align}
$$

yields the surface normal field pointing into the fluid and the divergence of n measures the curvature of the surface

$$
\begin{align}
\kappa = \frac{-\nabla^2 c_S}{|\bold n|}
\end{align}
$$

The minus is necessary to get positive curvature for convex fluid volumes. Putting it all together, we get for the surface traction:

$$
\begin{align}
\bold t^{surface} = \sigma \kappa  \frac{\bold n}{|\bold n|}
\end{align}
$$

To distribute the surface traction among particles near the surface and to get a force density we multiply by a normalized scalar field $\delta_s = |\bold n|$ which is non-zero only near the surface. For the force density acting near the surface we get

$$
\begin{align}
\bold f^{surface} = \sigma \kappa \bold n = -\sigma\nabla^2 c_S  \frac{\bold n}{|\bold n|}
\end{align}
$$

Evaluating $\frac{\bold n}{|\bold n|}$ at locations where $|\bold n|$ is small causes numerical problems. We only evaluate the force if $|\bold n|$ exceeds a certain threshold.

#### External Forces

Our simulator supports external forces such as gravity, collision forces and forces caused by user interaction. These forces are applied directly to the particles without the use of SPH. When particles collide with solid objects such as the glass in our examples, we simply push them out of the object and reflect the velocity component that is perpendicular to the object’s surface.

#### Smoothing Kernels

We designed the following kernel:

$$
\begin{align}
W_{poly6}(\bold r, h) = \frac{315}{64\pi h^9}
\begin{cases}
(h^2 - r^2)^3, & 0 \leq r \leq h \\
0, & \text{ otherwise}
\end{cases}
\end{align}
$$

For pressure computations:

$$
\begin{align}
W_{spiky}(\bold r, h) = \frac{15}{\pi h^6}
\begin{cases}
(h - r)^3, & 0 \leq r \leq h \\
0, & \text{ otherwise}
\end{cases}
\end{align}
$$

For viscosity forces compuration:

$$
\begin{align}
W_{viscosity}(\bold r, h) = \frac{15}{2\pi h^3}
\begin{cases}
-\frac{r^3}{2h^3} + \frac{r^2}{h^2} + \frac{h}{2r} - 1, & 0 \leq r \leq h \\
0, & \text{ otherwise}
\end{cases}
\end{align}
$$

#### Simulation

For the integration of the Eqn. (7) we use the Leap-Frog scheme. For the examples we used constant time steps. We expect even better performance if adaptive time steps are used based on the Courant-Friedrichs-Lewy condition.

### Surface Tracking and Visualization

From the color field $c_s$ and its gradient field $\bold n = \nabla c_S$, we identify a particle i as a surface particle if

$$
\begin{align}
|\bold n(\bold r_i)| > l
\end{align}
$$

where $l$ is a threshold parameter. The direction of the surface normal at the location of particle $i$ is given by

$$
\begin{align}
-\bold n(\bold r_i)
\end{align}
$$

#### Point Splatting

#### Marching Cubes
