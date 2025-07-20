# SIMPLE (Semi-Implicit Method for Pressure-Linked Equations)

## What is the SIMPLE Algorithm?

SIMPLE does not mean "simple" because it is easy or straightforward.  
In fact, the opposite is true. The author of this blog would like to applaud the person who named this algorithm.  
What a brilliant choice of name...  

As previously mentioned, CFD ultimately involves solving the continuity equation and the momentum equation.  
However, for incompressible flows, there is a significant problem:  
The continuity equation does not contain a pressure term.  
The primary goal of CFD is to solve for the pressure and velocity fields of a flow.  
But one of the unknowns we need to solve for does not even appear in the equation...  

The SIMPLE algorithm was developed to address this issue, and now we will explain it in detail.  

---

## Overview of the SIMPLE Algorithm

The SIMPLE algorithm is essentially an iterative procedure designed to achieve the following objectives:

1. Link the velocity field with the pressure field.
2. Correct the velocity field to satisfy the continuity equation (mass conservation).

Sounds simple, right? So why do CFD engineers find it so difficult?  
Because the iterative process itself is complex.  

![SIMPLE Algorithm](../image/SIMPLE.png)

Let’s dive deeper into how it works.

---

## Explanation of the SIMPLE Algorithm

The continuity and momentum equations are given as follows:

* **Continuity Equation**  
<center>**$\partial\rho\over\partial t$ + $\nabla\cdot(\rho U)$ = 0**</center>

    + $\rho$: Fluid density  
    + $t$: Time  
    + $U$: Fluid velocity  

* **Momentum Equation**  
<center>**$\rho$ ($\partial U\over\partial t$ + $U\cdot\nabla U$) = -$\nabla p + \mu\nabla^2 U + \rho g$**</center>

    + $U$: Fluid velocity  
    + $p$: Pressure  
    + $\mu$: Dynamic viscosity  
    + $g$: Gravitational acceleration vector  

For simplicity, assume steady-state, incompressible flow without gravity.  
The equations then simplify to:

* **Continuity Equation**  
<center>**$\nabla\cdot U = 0$**</center>

* **Momentum Equation**  
<center>**$U\cdot\nabla U = -\nabla p + \mu\nabla^2 U$**</center>

Next, move the viscous term to the left-hand side and express the momentum equation in matrix form:

<center>**$MU = -\nabla p$**</center>  

Where $M$ is a coefficient matrix.

$$
\begin{pmatrix}
M_{1,1} & M_{1,2} & M_{1,3} & \dots  & M_{1,n} \\
M_{2,1} & M_{2,2} & M_{2,3} & \dots  & M_{2,n} \\
M_{3,1} & M_{3,2} & M_{3,3} & \dots  & M_{3,n} \\
\vdots  & \vdots  & \vdots  & \ddots & \vdots  \\
M_{n,1} & M_{n,2} & M_{n,3} & \dots  & M_{n,n}^{sym}
\end{pmatrix}
\begin{pmatrix}
U_1 \\
U_2 \\
U_3 \\
\vdots \\
U_n
\end{pmatrix}
=
\begin{pmatrix}
\left( \dfrac{\partial p}{\partial x} \right)_1 \\
\left( \dfrac{\partial p}{\partial x} \right)_2 \\
\left( \dfrac{\partial p}{\partial x} \right)_3 \\
\vdots \\
\left( \dfrac{\partial p}{\partial x} \right)_n
\end{pmatrix}
$$

The complex momentum equation is now in a simple matrix form: $AX = B$.  
This applies to every cell, meaning if there are $N$ cells, $M$ is an $N \times N$ matrix.  

We then separate the left-hand side into diagonal and off-diagonal terms for approximation purposes in the correction step:

<center>**$AU - H = -\nabla p$**</center>

+ $A$: Diagonal coefficient matrix, expressed as:

$$
\mathcal{A} =
\begin{pmatrix}
A_{1,1} & 0       & 0       & \dots  & 0 \\
0       & A_{2,2} & 0       & \dots  & 0 \\
0       & 0       & A_{3,3} & \dots  & 0 \\
\vdots  & \vdots  & \vdots  & \ddots & \vdots \\
0       & 0       & 0       & \dots  & A_{n,n}
\end{pmatrix}
$$

The inverse of $A$ is easy to compute:

$$
\mathcal{A}^{-1} =
\begin{pmatrix}
\dfrac{1}{A_{1,1}} & 0 & 0 & \dots & 0 \\
0 & \dfrac{1}{A_{2,2}} & 0 & \dots & 0 \\
0 & 0 & \dfrac{1}{A_{3,3}} & \dots & 0 \\
\vdots & \vdots & \vdots & \ddots & \vdots \\
0 & 0 & 0 & \dots & \dfrac{1}{A_{n,n}}
\end{pmatrix}
$$

This greatly reduces computation in the correction step.

Here, $H$ represents off-diagonal terms and is expressed as:

<center>**$H = AU - MU$**</center>

Returning to the momentum equation:

<center>**$AU - H = -\nabla p$**</center>

The continuity equation remains:

<center>**$\nabla\cdot U = 0$**</center>

---

Now, derive the pressure equation:

From the momentum equation:

<center>**$U = A^{-1}H - A^{-1}\nabla p$**</center>

Substitute into the continuity equation:

<center>**$\nabla \cdot [A^{-1}H - A^{-1}\nabla p] = 0$**</center>

This gives the Poisson equation for pressure:

<center>**$\nabla \cdot (A^{-1}H) = \nabla \cdot (A^{-1}\nabla p)$**</center>

Now we have four equations for four unknowns ($U_X, U_Y, U_Z, p$):

<center>**$MU = -\nabla p$**</center>  
<center>**$\nabla \cdot (A^{-1}H) = \nabla \cdot (A^{-1}\nabla p)$**</center>

---

## SIMPLE Algorithm Steps

1. Solve the momentum equation for velocity (this velocity does not satisfy continuity yet):  
<center>**$MU = -\nabla p$**</center>

2. Solve the Poisson equation for pressure:  
<center>**$\nabla \cdot (A^{-1}H) = \nabla \cdot (A^{-1}\nabla p)$**</center>

3. Correct the velocity field using the pressure field:  
<center>**$U = A^{-1}H - A^{-1}\nabla p$**</center>

4. Repeat the process until both momentum and continuity equations are satisfied.