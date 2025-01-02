# CFD (Computational Fluid Dynamics)

## CFD?

CFD is abbreviation "Computational Fluid Dynamics".<br>
Normally, CFD purposes describing the movement or behavior of flow with Navier-Stokes Equation and Continuity Equation.<br>
Then, why engineers try to describe or model the movement or behavior of flow?<br>

## The purpose of CFD/ CAE

This questions could be corresponded to not only CFD but also CAE (Computer Aided Engineering).<br>
CAE means that previous review or advance review for the product or process design using computer technology.<br> [wiki](https://en.wikipedia.org/wiki/Computer-aided_engineering)

Engineers can't easily test or review the product and process before it is manufactured.<br>
Lots of tests and experiments are needed. And it increases the development period.<br>
With CAE, engineers can easily do advance-test, review and then do the real test or experiment about the optimized product and process.<br>
CFD is one of the CAE field and the purpose of CFD is analyzing the fluid dynamics, heat transfer about product and process.<br>

## CFD governing equation

In CAE field, there are lots of simulations like FEA, CFD, mold. Every simulation fields need own governing equation.<br>
Also CFD needs governing equation and generally computer solves **Continuity Equation** and **Navier-Stokes Equation**.

* Continuity Equation<br>
<center>**$\partial\rho\over\partial t$+ $\nabla\cdot(\rho U)$ = 0 <br>**</center>

    + $\rho$ : Fluid density<br>
    + $t$ : Time<br>
    + $U$ : Velocity vector of fluid<br>

And if fluid density is not changed (incompressible flow), **Continuity Equation** looks like this.<br>

<center>**$\nabla\cdot U$ = 0 <br>**</center>

It means that divergence of velocity is zero for whole flow field.

This equation is called continuity equation or mass conservation equation.<br>
It means that mass (mass flowrate) is constant for the whole flow field.<br>

* Navier-Stokes Equation
<center>**$\rho$ ($\partial U\over\partial t$+ $U\cdot\nabla U$) = $\nabla p+ \mu\nabla^2 u + \rho g$ <br>**</center>

    + $U$ : Velocity vector of fluid
    + $p$ : Pressure
    + $\mu$ : Viscosity
    + $g$ : Gravity acceleration vector

This equation is induced from Newton's 2nd law ($F=ma$).<br>
And I'll write the meaning of each terms in next time.<br>
In conclusion, we can get the acceleration of fluid (if we know acceleration, we can also know the velocity and coordinates) from the force in eqaution.<br>

## Governing equation Decision and models

There are more governing equations for the various models like turbulence model, heat transfer, multi-phase.<br>
The purpose of the governing equation is simple. To describe or model the movement of flow and to control this.<br>
However, the models are different from the situation or phenomenon that engineers should treat or control.<br>
So, engineers should easily assume their own system, problem and decide which governing equation to solve.