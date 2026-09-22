---
title: "Cart pole Simulation"
date: 2026-06-01
summary: "Modelled and simulated a cart-pole system with 4 different controllers"
cover:
  image: "model.jpg"
math: true
---

**Solo project · Dynamics Modelling, Control systems, MuJoCo, Simulation**

 <a href="https://github.com/sanjayramkumar27/cart-pole" class="gh-link">
  <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 16 16" fill="currentColor">
    <path d="M8 0C3.58 0 0 3.58 0 8c0 3.54 2.29 6.53 5.47 7.59.4.07.55-.17.55-.38
    0-.19-.01-.82-.01-1.49-2.01.37-2.53-.49-2.69-.94-.09-.23-.48-.94-.82-1.13
    -.28-.15-.68-.52-.01-.53.63-.01 1.08.58 1.23.82.72 1.21 1.87.87 2.33.66
    .07-.52.28-.87.51-1.07-1.78-.2-3.64-.89-3.64-3.95 0-.87.31-1.59.82-2.15
    -.08-.2-.36-1.02.08-2.12 0 0 .67-.21 2.2.82.64-.18 1.32-.27 2-.27.68 0
    1.36.09 2 .27 1.53-1.04 2.2-.82 2.2-.82.44 1.1.16 1.92.08 2.12.51.56.82
    1.27.82 2.15 0 3.07-1.87 3.75-3.65 3.95.29.25.54.73.54 1.48 0 1.07-.01
    1.93-.01 2.2 0 .21.15.46.55.38A8.01 8.01 0 0 0 16 8c0-4.42-3.58-8-8-8z"/>
  </svg>
  GitHub Repository
</a>

## Objective 
Model and simulate a basic cart pole system in MuJoCo using 4 different controllers - PID, Pole Placement, LQR and MPC. 


## Dynamics Modelling
- Cart modelled as a capsule of size 0.08 x 0.25 m with mass 1 Kg.
- Pole modelled as a cylinder of size 0.03 x 1 m with mass 0.1 Kg.
- Equations of motions were derived by hand using lagrangian approach.

The generalized coordinates are - 
$$
q =
\begin{bmatrix}
x \\
\theta
\end{bmatrix}
$$

thus the equations of motion are - 

$$
\begin{aligned}
(M+m)\ddot{x}
+ ml\cos\theta\,\ddot{\theta}
- ml\sin\theta\,\dot{\theta}^2
&= u, \\[4pt]
ml\cos\theta\,\ddot{x}
+ ml^2\ddot{\theta}
- mgl\sin\theta
&= 0.
\end{aligned}
$$

## State space model

For controller design, the nonlinear dynamics were linearized around the upright equilibrium:

$$
\theta = 0,\qquad
\dot{x}=0,\qquad
\dot{\theta}=0
$$

with the state vector

$$
\mathbf{x} =
\begin{bmatrix}
x & \dot{x} & \theta & \dot{\theta}
\end{bmatrix}^{T}.
$$

The resulting linear state-space representation is

$$
\dot{\mathbf{x}} = A\mathbf{x}+B u
$$

where

$$
A =
\begin{bmatrix}
0 & 1 & 0 & 0 \\
0 & 0 & -0.7178 & 0 \\
0 & 0 & 0 & 1 \\
0 & 0 & 15.792 & 0
\end{bmatrix}
$$

and

$$
B =
\begin{bmatrix}
0 \\
0.9756 \\
0 \\
-1.4634
\end{bmatrix}.
$$

{{< figure src="model.jpg" alt="Model of Robot" caption="Model in MuJoCo" >}}


## Controller

### Proportional-Integral-Derivative
A simple cascaded PID controller was built to stabilize the pole in the upright position and at the same time keep the cart position fixed. Although this cascaded PID works well in stabilizing the pole, the cart position keeps oscillating and never settles. 

{{< figure src="pid.png" alt="PID block diagram" caption="PID block diagram" >}}

### Pole Placement
The dynamics equations were linearized about the upright position and converted to state space form. Now assuming the control input to be u = -kx, where x is the state vector, it is possible to find the vector K given the desired location of closed loop poles. This was implemented using the scipy's place_poles function. It is observed that both the pole position and cart position is stabilized as per the chosen closed loop poles.

### Linear-Quadratic-Regulator
The same linearized state space model was used to find the optimal values for the K vector which minimizes a quadratic cost on the state and control effort.The Q matrix is used to decide the penalty on the states and R matrix for the control effort. scipy's solve_continuous_are function is used to solve the continuous time riccati equation to obtain the optimum k vector. It is observed that the both cart and pole position is stabilized.

## Comparison

### Region of Attraction (max angle recovered)
### Peak force (Impulse disturbance)
### Control effort
### Settling time

