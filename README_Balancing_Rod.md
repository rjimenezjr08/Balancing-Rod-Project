# Self-Balancing Rod Project

## Project Overview

This project is a mechatronics system designed to balance a vertical rod by moving its base horizontally. The carriage travels along an aluminum V-slot rail using a motor-driven GT2 timing belt and pulley system. The project combines mechanical design, dynamics, sensors, electronics, MATLAB/Simulink modeling, and feedback control.

The long-term goal is to transfer a controller developed in simulation to the physical prototype and maintain the rod near its unstable upright equilibrium while also keeping the carriage near the center of the rail.

---

## Main Objectives

- Design and manufacture a carriage capable of translating smoothly along a linear guide rail.
- Create a low-friction pivot that allows the rod to rotate freely.
- Model the coupled cart-and-rod dynamics.
- Measure rod angle and cart motion using sensors.
- Develop and tune a feedback controller in MATLAB/Simulink.
- Stabilize the rod near the upright position.
- Add cart-position control so the carriage does not drift along the rail.
- Transfer the simulated controller to the physical system.

---

## Mechanical Design

The current mechanical concept uses:

- 2040 V-slot aluminum extrusion as the linear guide rail
- V-wheels to constrain and guide the carriage
- GT2 timing belt for linear actuation
- GT2 drive and idler pulleys
- NEMA 17 stepper motor
- Custom motor and idler mounts
- Custom carriage and belt attachment
- Bearing-supported rod pivot
- Vertical balancing rod mounted to the moving carriage

The timing-belt system replaced an earlier rack-and-pinion concept because it simplifies the carriage drivetrain and reduces mechanical interference.

---

## Drive System

The drive system converts motor rotation into horizontal carriage motion:

`Motor → GT2 Drive Pulley → Timing Belt → Carriage`

The carriage motion changes the location of the rod pivot. By accelerating the pivot beneath the rod's center of mass, the controller can generate the corrective motion required to stabilize the inverted rod.

---

## Sensors and Electronics

Planned/current sensing and control hardware includes:

- Microcontroller for real-time feedback control
- Magnetic rotary encoder for rod-angle measurement
- Diametrically magnetized permanent magnet for the angle sensor
- Motor driver for the NEMA 17 stepper motor
- Cart-position measurement using motor motion and/or a dedicated position sensor

Sensor measurements will provide the states required by the controller, including rod angle, angular velocity, cart position, and cart velocity.

---

## MATLAB / Simulink Model

A dynamic model of the balancing system is currently being developed and tested using MATLAB, Simulink, and Simscape Multibody.

The simulated system includes:

- Translating carriage
- Revolute rod pivot
- Gravity
- Rod rotational dynamics
- Horizontally applied control force
- Rod-angle sensing
- Cart-position sensing
- Closed-loop feedback controller

The upright rod reference is

\[
\theta_{\mathrm{ref}} = \frac{\pi}{2}
\]

and the angular error is calculated from the difference between the desired and measured rod angles.

Because revolute-joint measurements can accumulate through multiple rotations, the angular error is wrapped using

\[
e_\theta =
\operatorname{atan2}
\left(
\sin(\theta_{\mathrm{ref}}-\theta),
\cos(\theta_{\mathrm{ref}}-\theta)
\right)
\]

which keeps the controller error between \(-\pi\) and \(+\pi\).

---

## PID Control Development

A PID-based controller is currently being tuned for rod stabilization.

Current experimental gains are approximately:

```text
Kp = 2.2
Ki = 0.4
Kd = 0.1
```

These values are still being refined and are not considered final hardware gains.

### Current Result

The controller can now damp the rod-angle oscillation and drive the wrapped angular error close to zero. This represents a major improvement over earlier simulations in which the rod continuously rotated or the simulation became unstable.

The current limitation is **cart-position drift**.

While the rod remains close to upright,

\[
\theta \rightarrow \frac{\pi}{2},
\]

the carriage position can continue increasing or decreasing because the current control loop primarily regulates rod angle and does not independently force

\[
x \rightarrow 0.
\]

This means that rod stabilization has been demonstrated in simulation, but the complete controller still requires position regulation.

---

## Next Control Stage: Cart Position Feedback

The next controller revision will introduce a second feedback objective for cart position.

Define

\[
e_x = x_{\mathrm{ref}} - x
\]

and use the position error and cart velocity to generate a small correction to the desired rod angle:

\[
\theta_{\mathrm{cmd}}
=
\frac{\pi}{2}
+
\Delta\theta
\]

where \(\Delta\theta\) depends on cart position and velocity.

The existing rod-angle controller can then stabilize the rod about this slightly adjusted reference. This should allow the system to balance the rod while gradually returning the carriage toward the center of the track.

The control architecture will therefore progress toward:

`Cart Position Loop → Desired Rod Angle → Rod Angle Controller → Motor/Force Command`

---

## Mathematical Model

The system is treated as a coupled cart-and-inverted-pendulum system.

Important state variables include:

- \(x\): carriage position
- \(\dot{x}\): carriage velocity
- \(\theta\): rod angle
- \(\dot{\theta}\): rod angular velocity

The cart acceleration affects rod rotation, while the rod dynamics affect the force required from the carriage actuator. These coupled dynamics form the basis for the Simulink model and controller design.

---

## Current Project Status

### Mechanical Design
- [x] Timing belt and pulley drive selected
- [x] Aluminum guide rail selected
- [x] Drive pulley modeled
- [x] Idler pulley/mount developed
- [x] Motor mount developed
- [x] Carriage concept developed
- [x] Rod geometry developed
- [x] Bearing-supported rod pivot developed
- [ ] Finalize complete physical assembly
- [ ] Manufacture/print remaining mounts and carriage components
- [ ] Assemble and tension timing belt

### Simulation and Controls
- [x] Simscape Multibody model created
- [x] Cart translation implemented
- [x] Rod revolute motion implemented
- [x] Control force applied to carriage
- [x] Upright angle reference established
- [x] Wrapped angular error implemented
- [x] PID controller implemented
- [x] Initial PID tuning performed
- [x] Rod-angle oscillations successfully damped in simulation
- [ ] Reduce/refine remaining angle response
- [ ] Add cart-position feedback
- [ ] Keep cart near the center of the rail
- [ ] Add realistic actuator limits
- [ ] Validate controller robustness

### Electronics
- [ ] Integrate rod-angle encoder
- [ ] Integrate motor driver
- [ ] Implement cart-position measurement
- [ ] Connect microcontroller
- [ ] Implement real-time controller

### Testing
- [ ] Bench-test sensors
- [ ] Characterize motor and carriage response
- [ ] Compare physical behavior with Simulink model
- [ ] Transfer controller gains to hardware
- [ ] Tune physical balancing controller
- [ ] Perform full-system balancing tests

---

## Development Progress

The project has progressed through several design stages:

1. Initial inverted-pendulum concept and equations of motion
2. Rack-and-pinion drivetrain exploration
3. Transition to a GT2 timing-belt drive
4. Carriage, guide rail, motor mount, and rod-pivot CAD
5. Simscape Multibody dynamic model
6. Angle-feedback controller implementation
7. PID tuning and angular-error wrapping
8. Successful damping of rod-angle oscillations
9. **Current stage: adding cart-position regulation**
10. Future hardware implementation and testing

---

## Engineering Areas Used

- Mechanical design
- CAD
- Dynamics
- Vibrations
- Control systems
- MATLAB
- Simulink
- Simscape Multibody
- Sensors and instrumentation
- Embedded systems
- Electronics
- Rapid prototyping / 3D printing

---

## Project Goal

The final system will combine the mechanical prototype, sensors, motor drive, and feedback controller into a physical inverted-pendulum platform capable of balancing the rod while maintaining controlled carriage position.

The project is currently in the **simulation and control-development stage**, with rod-angle stabilization demonstrated and cart-position control as the next major milestone.
