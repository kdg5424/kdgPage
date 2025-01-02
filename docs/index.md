# Home

## Introduction

**Whatever for CFD and CFD-DEM**

This blog is operated by kdg who is an CFD / CFD-DEM engineer.
I will share the various technical knowledges about CFD, CFD-DEM and OpenFOAM etc. Please have lots of attention and interests.

+ CFD?

CFD stands for Computational Fluid Dynamics.
It refers to a group of techniques and studies that numerically describe the movement of fluids based on computers and calculations.
It simulates flow, heat transfer, mass transfer, chemical reaction using governing eqaution based on Navier-Stokes equation.

+ DEM?

DEM stands for Discrete Element Method.
It calculates particle's momentum and deformation using contact force based on particle collision.
It means that DEM uses soft-sphere method which assumes that particle is a rigid body which is allowable the deformation.

+ CFD-DEM

CFD-DEM is called interaction simulation between fluid-particle, it simulates the particle behavior in flow field.
In this method, particle behavior is calculated by DEM and the flow is described by CFD based on Navier-Stokes equation. And interaction between fluid and particle is modeled as Newton's 3rd law.
