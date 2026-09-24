# Self-Balancing Rod Project

## Overview

This project is a self-balancing inverted pendulum system designed to keep a vertical rod upright by moving its base horizontally along a rail.

The system combines:

- Mechanical design
- CAD
- Dynamics
- MATLAB
- Simulink
- Simscape Multibody
- Feedback control
- Sensors
- Embedded electronics
- 3D printing
- Motor control

The final goal is to create a physical system capable of balancing the rod near its unstable upright equilibrium while also keeping the carriage near the center of the track.

---

## Project Concept

The rod is mounted to a moving carriage using a low-friction pivot.

When the rod begins to fall, the carriage moves underneath the rod's center of mass. By continuously measuring the rod angle and adjusting the carriage position, the system can stabilize the rod around its upright position.

The basic control concept is:

```text
Rod begins to fall
        ↓
Angle sensor measures rod position
        ↓
Controller calculates angle error
        ↓
Motor moves carriage
        ↓
Pivot moves underneath rod
        ↓
Rod returns toward upright
````

---

# Mechanical Design

## Linear Motion System

The carriage moves along a **2040 V-slot aluminum extrusion**.

The carriage is constrained using V-wheels that ride along the extrusion and allow low-friction horizontal motion.

The main mechanical components include:

* 2040 V-slot aluminum guide rail
* V-wheel carriage
* GT2 timing belt
* GT2 drive pulley
* GT2 idler pulley
* NEMA 17 stepper motor
* Motor mount
* Idler mount
* Belt attachment
* Rod pivot
* Vertical balancing rod

---

## Timing Belt Drive

The project originally explored a rack-and-pinion drivetrain.

The design was later changed to a **GT2 timing-belt system** because it provides a simpler drivetrain and reduces mechanical interference around the carriage.

The current drivetrain is:

```text
NEMA 17 Motor
      ↓
GT2 Drive Pulley
      ↓
GT2 Timing Belt
      ↓
Moving Carriage
```

The timing belt is attached directly to the carriage so that pulley rotation produces linear motion.

---

## Motor

The current motor selection is a NEMA 17 stepper motor.

Approximate specifications:

```text
Motor Type: Bipolar NEMA 17 Stepper
Holding Torque: ~59 N·cm
Rated Current: ~2.0 A
Shaft Diameter: 5 mm
```

The motor drives a GT2 pulley connected to the timing belt.

---

## Guide Rail

A 2040 V-slot extrusion is used as the main linear guide.

The rail provides:

* Straight carriage motion
* Structural support
* V-wheel guidance
* Mounting locations for the motor and idler assemblies

The current design uses approximately:

```text
Pulley center distance: ~650 mm
```

---

# Carriage Design

The carriage rides on the aluminum extrusion using V-wheels.

The carriage supports:

* Rod pivot
* Belt attachment
* Bearings
* Sensor hardware
* Future electronics if required

The timing belt attaches directly to the carriage so that motor rotation translates the carriage along the guide rail.

---

# Rod Pivot

The balancing rod is connected to the carriage using a bearing-supported pivot.

The goal of the pivot is to minimize friction so that the rod can rotate freely.

The design includes:

* Pivot bracket
* Bearing support
* Shaft or shoulder bolt
* Rod attachment

A magnetic rotary encoder will eventually measure the rod angle at this pivot.

---

# Balancing Rod

The rod acts as the inverted pendulum.

Current prototype dimensions are approximately:

```text
Length: ~295 mm
Radius: ~2.5 mm
```

Possible materials include:

* Aluminum
* PETG
* Carbon fiber
* Steel

A lightweight, rigid rod is preferred because excessive mass increases the required motor acceleration.

---

# Sensors and Electronics

The system requires measurements of the rod angle and carriage motion.

Planned electronics include:

* Microcontroller
* Magnetic rotary encoder
* Diametrically magnetized magnet
* Motor driver
* Stepper motor
* Power supply
* Cart-position measurement system

---

## Rod Angle Sensor

The planned rod-angle sensor is an **AS5600 magnetic rotary encoder**.

The encoder uses a diametrically magnetized permanent magnet attached to the rotating rod shaft.

The sensor provides an absolute angular measurement that can be used by the feedback controller.

Important states include:

```text
Rod angle:            θ
Rod angular velocity: θ̇
Cart position:         x
Cart velocity:         ẋ
```

---

# Mathematical Model

The system is modeled as a coupled cart-and-inverted-pendulum system.

The primary generalized coordinates are:

$$
x(t)
$$

for the horizontal carriage position and

$$
\theta(t)
$$

for the rod angle.

The cart acceleration affects the angular motion of the rod, while the rod dynamics influence the force required from the carriage actuator.

Important state variables include:

$$
x,\quad \dot{x},\quad \theta,\quad \dot{\theta}
$$

These states form the basis of the feedback-control system.

---

# MATLAB / Simulink Model

A dynamic model of the system has been developed using:

* MATLAB
* Simulink
* Simscape Multibody

The simulation includes:

* Moving carriage
* Revolute rod pivot
* Gravity
* Rod rotational dynamics
* Horizontal control force
* Rod-angle sensing
* Cart-position sensing
* Closed-loop feedback control

The simulation is currently being used to develop the controller before transferring it to the physical system.

---

# Upright Reference

The desired upright rod angle is defined as:

$$
\theta_{\text{ref}}=\frac{\pi}{2}
$$

which corresponds to:

$$
90^\circ
$$

The basic angular error is:

$$
e_\theta
=
\theta_{\text{ref}}-\theta
$$

---

# Angular Error Wrapping

During early simulations, the revolute-joint angle continued accumulating when the rod completed full rotations.

For example:

```text
90°
450°
810°
1170°
```

all represent the same physical rod orientation.

Using the raw angular difference caused the controller error to grow very large after multiple rotations.

To solve this problem, the angle error was wrapped using:

$$
e_\theta=
\operatorname{atan2}
\left(
\sin(\theta_{\text{ref}}-\theta),
\cos(\theta_{\text{ref}}-\theta)
\right)
$$

This keeps the controller error within:

$$
-\pi \leq e_\theta \leq \pi
$$

and forces the controller to use the shortest angular distance to the desired orientation.

---

# PID Controller

A PID controller is currently being used to stabilize the rod.

The basic control structure is:

$$
u =
K_p e
+
K_i \int e\,dt
+
K_d\frac{de}{dt}
$$

where:

* \(K_p\) controls the strength of the correction
* \(K_i\) eliminates accumulated steady-state error
* \(K_d\) provides damping

---

## PID Tuning

The controller has been manually tuned using repeated Simulink tests.

Current experimental gains are approximately:

```text
Kp = 2.2
Ki = 0.4
Kd = 0.1
```

These values are still experimental and are not expected to directly transfer to the physical system.

The physical system will require additional tuning because of:

* Motor limitations
* Mechanical friction
* Sensor noise
* Belt elasticity
* Bearing friction
* Timing delays
* Motor-driver dynamics
* Physical disturbances

---

## PID Tuning Observations

Several behaviors were observed while tuning the controller.

### Low Proportional Gain

When \(K_p\) was too small:

* The carriage did not react strongly enough.
* The rod continued falling.
* The controller could not recover the upright position.

---

### High Proportional Gain

When \(K_p\) became too large:

* Oscillations increased.
* The simulation became unstable.
* The rod could rotate continuously.
* Control forces could become extremely large.

A proportional gain near:

```text
Kp ≈ 2.2
```

provided a useful starting point.

---

### Derivative Gain

Adding derivative control significantly improved the system response.

Derivative control acts as damping and reduces rapid angular motion.

Moderate values of \(K_d\) caused the oscillations to decay.

Excessively large derivative gain caused the simulation to become unstable again.

A useful experimental region was approximately:

```text
Kd ≈ 0.1 - 0.2
```

---

### Integral Gain

Integral control was later introduced to reduce remaining angular error.

Integral gain must be used carefully because excessive integral action can cause:

* Overshoot
* Integral windup
* Continued control effort
* Cart-position drift

The current value is approximately:

```text
Ki = 0.4
```

and may be reduced during future tuning.

---

# Current Simulation Result

The current PID controller can substantially reduce rod-angle oscillations.

The angular error now approaches the upright reference instead of continuously diverging.

In simulation:

$$
\theta(t)\rightarrow\frac{\pi}{2}
$$

and:

$$
e_\theta(t)\rightarrow 0
$$

The rod-angle controller is therefore able to approximately stabilize the inverted pendulum.

However, a new issue has become clear.

---

# Current Limitation: Cart Position Drift

Although the rod angle approaches the upright position, the carriage continues drifting along the rail.

In other words:

$$
\theta(t)\rightarrow\frac{\pi}{2}
$$

while:

$$
x(t)\rightarrow \pm\infty
$$

in the unrestricted simulation.

This occurs because the current controller primarily regulates the rod angle.

As long as the rod remains upright, the controller has no requirement to return the carriage toward the center of the track.

Therefore, the simulation currently demonstrates:

```text
Rod angle control:      Working
Cart position control:  Not yet implemented
```

The next major control objective is to stabilize both quantities simultaneously.

---

# Next Control Stage: Cart Position Feedback

A separate cart-position controller will be added.

The desired cart position will be:

$$
x_{\text{ref}}=0
$$

with position error:

$$
e_x=x_{\text{ref}}-x
$$

Instead of directly combining cart position with the rod-angle error, the cart-position controller will create a small adjustment to the desired rod angle.

The commanded angle will become:

$$
\theta_{\text{cmd}}
=
\frac{\pi}{2}
+
\Delta\theta
$$

where the angle correction may depend on cart position and velocity:

$$
\Delta\theta
=
K_x e_x
-
K_v\dot{x}
$$

The existing rod controller will then attempt to follow this modified angle reference.

---

## Planned Cascaded Control Structure

```text
Desired Cart Position
        ↓
Cart Position Error
        ↓
Position Controller
        ↓
Small Angle Correction
        ↓
Desired Rod Angle
        ↓
Rod Angle Controller
        ↓
Motor / Force Command
        ↓
Cart Motion
        ↓
Rod Stabilization
```

This should allow the controller to perform two objectives:

1. Keep the rod upright.
2. Keep the carriage near the center of the rail.

---

# Current Project Status

## Mechanical Design

* [x] Initial inverted-pendulum concept developed
* [x] Rack-and-pinion drivetrain explored
* [x] Timing-belt drivetrain selected
* [x] GT2 pulley system designed
* [x] 2040 V-slot guide rail selected
* [x] NEMA 17 motor selected
* [x] Motor mount designed
* [x] Idler mount designed
* [x] Carriage concept designed
* [x] Belt attachment concept designed
* [x] Rod geometry created
* [x] Rod pivot designed
* [x] Bearing support developed
* [ ] Finalize complete assembly
* [ ] Manufacture remaining components
* [ ] Assemble carriage
* [ ] Install timing belt
* [ ] Tension timing belt
* [ ] Install rod and pivot

---

## Simulation and Controls

* [x] Simscape Multibody model created
* [x] Carriage translation modeled
* [x] Rod rotation modeled
* [x] Gravity implemented
* [x] Horizontal control force implemented
* [x] Rod-angle measurement implemented
* [x] Cart-position measurement implemented
* [x] Upright reference established
* [x] PID controller implemented
* [x] Manual PID tuning started
* [x] Angular error wrapping implemented
* [x] Continuous rod rotation issue identified
* [x] Rod-angle oscillations substantially damped
* [x] Rod stabilized approximately around upright position
* [x] Cart-position drift identified
* [ ] Refine PID gains
* [ ] Add cart-position feedback
* [ ] Prevent carriage drift
* [ ] Add actuator saturation
* [ ] Add realistic motor limits
* [ ] Test disturbances
* [ ] Test controller robustness

---

## Electronics

* [ ] Acquire remaining electronics
* [ ] Install AS5600 rotary encoder
* [ ] Install magnet on pivot
* [ ] Connect motor driver
* [ ] Connect NEMA 17 motor
* [ ] Implement cart-position measurement
* [ ] Connect microcontroller
* [ ] Implement real-time sensor acquisition
* [ ] Implement hardware feedback controller

---

## Physical Testing

* [ ] Test carriage motion
* [ ] Measure mechanical friction
* [ ] Test rod-angle sensor
* [ ] Characterize motor acceleration
* [ ] Compare simulated and physical dynamics
* [ ] Transfer controller to hardware
* [ ] Tune physical PID controller
* [ ] Add cart-position controller
* [ ] Test balancing from small disturbances
* [ ] Perform full-system balancing test

---

# Project Development Timeline

The project has progressed through several major stages:

```text
1. Inverted-pendulum concept
          ↓
2. Dynamic model development
          ↓
3. Rack-and-pinion drivetrain concept
          ↓
4. Transition to GT2 timing belt
          ↓
5. Guide rail and carriage design
          ↓
6. Motor and idler mount design
          ↓
7. Rod pivot design
          ↓
8. Simscape Multibody model
          ↓
9. Angle feedback controller
          ↓
10. PID tuning
          ↓
11. Angular error wrapping
          ↓
12. Rod-angle stabilization
          ↓
13. Current Stage:
    Cart-position regulation
          ↓
14. Hardware implementation
          ↓
15. Physical balancing tests
```

---

# Engineering Skills Used

This project combines several engineering disciplines.

## Mechanical Engineering

* Dynamics
* Mechanism design
* CAD
* Mechanical assemblies
* Bearings
* Belt drives
* Linear motion systems
* Structural design
* Rapid prototyping

## Controls

* PID control
* Feedback systems
* Inverted pendulum dynamics
* Stability analysis
* Sensor feedback
* Controller tuning
* Multi-variable control

## Software

* MATLAB
* Simulink
* Simscape Multibody
* Data visualization
* Numerical simulation

## Electronics

* Rotary encoders
* Stepper motors
* Motor drivers
* Microcontrollers
* Sensor integration
* Power electronics

## Manufacturing

* 3D printing
* PETG prototyping
* Mechanical assembly
* Fasteners
* Bearing installation

---

# Future Improvements

Several improvements are planned as the project develops.

Possible future upgrades include:

* Cart-position feedback
* State-space control
* LQR control
* Kalman filtering
* Improved velocity estimation
* Encoder-based cart-position measurement
* Motor acceleration limiting
* Anti-windup control
* Controller output saturation
* Sensor-noise filtering
* Automatic PID tuning
* Comparison between PID and LQR control
* Experimental system identification

---

# Long-Term Goal

The final goal is to develop a complete physical self-balancing system that combines:

```text
Mechanical Structure
        +
Sensors
        +
Motor and Drivetrain
        +
Microcontroller
        +
Feedback Controller
        =
Self-Balancing Rod
```

The system should eventually be capable of:

* Maintaining the rod near the upright position
* Recovering from small disturbances
* Keeping the carriage near the center of the track
* Operating within realistic motor and track limits

---

# Current Stage

The project is currently in the:

## Simulation and Control Development Stage

Rod-angle stabilization has been demonstrated in the Simulink model.

The current major limitation is carriage drift.

The next major milestone is:

```text
Simultaneous Rod-Angle + Cart-Position Control
```

Once the controller can regulate both variables in simulation, development will move toward implementing the control system on the physical prototype.

```
