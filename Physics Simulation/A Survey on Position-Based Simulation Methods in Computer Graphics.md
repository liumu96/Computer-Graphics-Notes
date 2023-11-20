# A Survey on Position-Based Simulation Methods in Computer Graphics

## Abstract

The classical methods in this field discretize **Newton’s second law** in a variety of Lagrangian or Eulerian ways, and formulate forces appropriate for each mechanical effect: joints for rigid bodies; stretching, shearing, or bending for deformable bodies; and pressure, or viscosity for fluids, to mention just a few.

In the last years the class of **position-based methods** has become popular in the graphics community. These kinds of methods are fast, stable and controllable which make them well-suited for use in interactive environments. Position-based methods are **not as accurate as** force-based methods in general but they provide **visual plausibility**. Therefore, the main application areas of these approaches are virtual reality, computer games and special effects in movies.

## Position Based Dynamics

The objects to be simulated are represented by a set of $N$ particles and a set of $M$ constraints. Each particle $i$ has three attributes, namely

- $m_i$: mass
- $\mathbf x_i $ : position
- $\mathbf v_i $ : velocity

A constraint $j$ is defined by the five attributes:

- $n_i$: &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp; cardinality
- $\mathbf C_j: \mathbb R^{3n_j} \rightarrow \mathbb R $ : &emsp; &emsp;&emsp;&emsp;&emsp; scalar constraint function
- $\{i_1, ..., i_n \}, i_k \in [1, ..., N]$:&emsp;set of indices
- $k_j \in [0 ... 1]$: &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;stiffness parameter
- unilateral of bilateral: &emsp;&emsp;&emsp;&emsp; type

### Algorithm 1 Position-based dynamics

1: **for all** vertices $i$ **do**  
2: &emsp;&emsp;initialize $\mathbf x_i = \mathbf x_i^0, \mathbf v_i = \mathbf v_i^0, \mathbf w_i = 1/\mathbf m_i$  
3: **end for**  
4: **loop**  
5: &emsp;&emsp;**for all** vertices i **do** $\mathbf x_i ← \mathbf x_i + ∆t\mathbf w_if_{ext}(\mathbf x_i)$  
6: &emsp;&emsp;**for all** vertices i **do** $\mathbf p_i ← \mathbf x_i + ∆t\mathbf v_i$  
7: &emsp;&emsp;**for all** vertices i **do** genCollConstraints($\mathbf x_i → \mathbf p_i$)  
8: &emsp;&emsp;**loop** solverIteration **times**  
9: &emsp;&emsp;&emsp;&emsp;projectConstraints($C_1, . . . ,C_{M+M_{Coll}} , \mathbf p_1, . . . , \mathbf p_N$)  
10:&emsp;&emsp;**end loop**  
11:&emsp;&emsp;**for all** vertices $i$ **do**  
12:&emsp;&emsp;&emsp;&emsp;$\mathbf v_i ← (\mathbf p_i − \mathbf x_i)/∆t$  
13:&emsp;&emsp;&emsp;&emsp;$\mathbf x_i ← \mathbf p_i$  
14:&emsp;&emsp;**end for**  
15:&emsp;&emsp;velocityUpdate($\mathbf v_1, . . . , \mathbf v_N $)  
16: **end loop**

## Shape Matching

Shape matching is a meshless approach which is able to simulate visually plausible elastic and plastic deformations

## Data-Driven Upsampling Methods
