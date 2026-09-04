# ECO — Automated Calibration using Model-based Optimization

![MATLAB](https://img.shields.io/badge/MATLAB-R2023b-orange?logo=Mathworks&logoColor=s://www.mathworks.com/products/matlab.html)
[![Simulink](https://img.shields.io/badge/Simulink-R2023b-blue)](https://www.mathworks.imulink.html)
![acados](https://img.shields.io/badge/acados-v0.1.9-orange)
![CasADi](https://img.shields.io/badge/CasADi-v3.4.5-green)
[![DOI](https://img.shields.io/badge/DOI-10.1016%2Fj.conengprac.2024.105848-blue)](https://doi.org/10.1016/j.conengprac.2024.105848)

ECO stands for **E**conomic **C**ombustion **O**ptimization and solves for the optimal injector inputs of a direct-injection compression-ignition engine. Using the software package acados, a continuous-time optimal control problem (OCP) is formulated and solved by direct method (multiple shooting). The resulting nonlinear program (NLP) is solved by sequential quadratic programming (SQP) using the solver HPIPM. The resulting Hessian is regularized using Levenberg-Marquardt regularization.

ECO was implemented on a real engine test bench using a rapid prototyping system and embedded controllers. A demonstration is available here: https://vimeo.com/933704668

## 1. Summary

The OCP uses an economic cost function over one high pressure cycle of a cylinder with states $x$. The independent variable is the crank angle $\varphi$; the decision
variables are the injection inputs $u$, which are constant over the horizon.

$$
\begin{aligned}
\underset{{u,\;x(\cdot)}}{\mathrm{min}} \quad & J(u) && \text{(injected fuel energy)}\\
\text{s.t.}\quad
& \frac{\mathrm{d}x}{\mathrm{d}\varphi} = f\big(x(\varphi),u,\varphi\big),
  \qquad \varphi \in [\varphi_0,\varphi_{\mathrm{EVO}}]
  && \text{(cylinder dynamics)}\\
& x(\varphi_0) = x_0 && \text{(initial state)}\\
& h\big(x(\varphi),u,\varphi\big) \le 0
  && \text{(path constraints: } p_{\mathrm{max}},\ \mathrm{d}p/\mathrm{d}\varphi,\ \text{CoC)}\\
& h_e\big(x(\varphi_\mathrm{EVO}),u\big) \le 0
  && \text{(terminal: IMEP, } T_\mathrm{EVO},\ \mathrm{NO}_x,\ \Phi,\ \text{inj. spacing)}\\
& u_{\mathrm{min}} \le u \le u_{\mathrm{max}} && \text{(actuator limits)}
\end{aligned}
$$

Minimizing injected fuel at requested IMEP is equivalent to maximizing indicated
efficiency, so the solution is the most fuel-efficient injection strategy that
still respects the mechanical (peak pressure, pressure rise rate) and emission
(NOx, exhaust temperature, equivalence ratio) limits.


## Additional Software Packages

Please install additional software in the folder `04_external`:

- [casadi](https://web.casadi.org/) v3.4.5
- [acados](https://github.com/acados/acados) v0.1.9 or later

## Exemplary Scripts

In order to run the exemplary scripts, please do the following:

1. Add all folders and subfolders to the working path:

   ```matlab
   run path_set.m
   ```

2. Create the s-functions necessary to run ECO in Simulink:

   ```matlab
   run main_InjOpt.m
   ```

3. Run the exemplary file for use of ECO in MATLAB:

   ```matlab
   run mainMATLAB.m
   ```

4. Run the exemplary file for use of ECO in Simulink:

   ```matlab
   run mainSimulink.m
   ```

The exemplary file for MATLAB highlights how ECO can be used to obtain optimal injector inputs under varying constraints which are set by the user. Make sure to initialize the solver with initial values which are close to the solution previously obtained. Otherwise, robust convergence to a feasible solution is not guaranteed.

The exemplary file for Simulink highlights how the solutions of ECO can be used in a real-time controller as feedforward control inputs. The bounds, which are not met, can be adapted by a simple PI-controller. The Simulink model presented can be used for compilation on embedded systems.
