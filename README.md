# Self-Balancing Rod Project

A mechatronics project designed to balance a vertical rod by moving its base horizontally using a **motor-driven timing belt and pulley system**.

The project combines **mechanical design, CAD, sensors, embedded programming, and feedback control** to create a physical inverted-pendulum system.

## Project Overview

The system consists of a vertical rod mounted to a moving carriage. The rod is free to rotate about a pivot while the carriage moves horizontally along a guide rail.

A motor drives a timing pulley connected to a toothed belt, which moves the carriage along the rail. An IMU mounted on the rod measures its angular position and motion.

The goal is to use feedback control to keep the rod near its upright equilibrium position by continuously moving the carriage underneath it.

## Main Objectives

* Design a stable carriage and guide-rail system
* Convert motor rotation into horizontal carriage motion using a timing belt and pulley
* Measure rod angle using an IMU
* Develop the equations of motion for the coupled rod-and-carriage system
* Implement a feedback controller
* Balance the rod near the vertical position
* Compare theoretical behavior with the physical prototype

## Mechanical Design

The mechanical system includes:

* Moving carriage
* Pivot-mounted balancing rod
* Timing belt and pulley drive
* Linear guide rail
* Motor mount
* Belt tensioning system
* Structural frame and mounting hardware

The carriage is designed to move horizontally while supporting the rod pivot and associated electronics.

The timing pulley converts the rotational motion of the motor into linear belt motion. The carriage is attached to the timing belt, allowing the motor to move the carriage in either horizontal direction.

## Drive System

The project uses a toothed timing belt and pulley system rather than a rack-and-pinion mechanism.

The belt drive was chosen to provide:

* Smooth linear motion
* Low backlash
* Relatively simple mechanical construction
* Easy coupling between the motor and moving carriage
* Lightweight moving components

The carriage is mechanically clamped or attached to the timing belt so that pulley rotation produces controlled horizontal displacement.

## System Concept

The balancing rod behaves similarly to an **inverted pendulum**.

The upright position is unstable. If the rod begins to fall in one direction, the carriage must move in that direction to reposition the pivot underneath the rod and restore balance.

The controller therefore attempts to maintain:

```text
θ ≈ 0
```

where `θ` represents the rod's deviation from the vertical position.

## Sensors and Electronics

The system is planned to use an **MPU6050 IMU** to measure rod orientation and angular motion.

Main electronic components include:

* MPU6050 accelerometer/gyroscope
* Microcontroller
* DC motor
* Motor driver
* Motor encoder
* Power supply

The motor encoder can be used to estimate motor rotation and carriage displacement, while the IMU provides feedback about the rod angle.

## Control System

The controller receives feedback from the IMU and determines how the carriage should move.

A simplified control loop is:

```text
Rod Angle
    ↓
MPU6050
    ↓
Microcontroller
    ↓
Control Algorithm
    ↓
Motor Driver
    ↓
Motor + Timing Pulley
    ↓
Timing Belt
    ↓
Carriage Motion
    ↓
Rod Motion
```

Possible control approaches include:

* PID control
* State-space control
* LQR control

## Mathematical Model

The system is modeled as a coupled cart-and-rod system.

Important variables include:

```text
x(t)      = horizontal carriage position
θ(t)      = rod angle
x_dot     = carriage velocity
θ_dot     = rod angular velocity
```

The rod's rotational motion and carriage translation are coupled. Acceleration of the carriage produces motion of the rod, which can be used to correct deviations from the upright position.

The timing belt drive provides the mechanical relationship between motor rotation and carriage displacement.

For a pulley with pitch radius `r`:

```text
x = rθ_m
```

where:

```text
x    = carriage displacement
r    = pulley pitch radius
θ_m  = motor/pulley rotation
```

## CAD and Prototyping

Current mechanical design work includes:

* Carriage geometry
* Timing pulley selection
* Timing belt routing
* Belt clamp design
* Guide rail selection
* Motor mount design
* Rod pivot assembly

The system is being designed in CAD before fabrication and assembly.

## Current Project Status

The project is currently under development.

## Current Progress

The main mechanical structure of the self-balancing rod system has been designed.

Completed so far:
- Aluminum linear guide rail integrated into the design
- Timing belt and pulley drive system
- NEMA 17 stepper motor selected and incorporated into the assembly
- Drive and idler pulley geometry modeled
- Moving carriage structure designed
- Balancing rod modeled
- Bearing-supported pivot added to allow the rod to rotate freely
- Overall mechanical layout assembled in CAD

Currently in progress:
- Designing and refining the motor mount
- Finalizing mounting hardware and support geometry
- Preparing the design for integration with electronics and control hardware

Next steps:
- Mount the motor and finalize the belt tensioning system
- Add a rotary angle sensor/encoder for rod position measurement
- Integrate the microcontroller and motor driver
- Begin implementing the balancing control system

## Engineering Areas Used

This project combines concepts from:

* Dynamics
* Control systems
* Mechanical design
* CAD
* Embedded systems
* Sensors and instrumentation
* Feedback control
* Electronics
* Programming

## Project Goal

The overall goal of this project is to design and build a working physical inverted-pendulum system that integrates mechanical motion, sensing, electronics, and feedback control.

Rather than only simulating the system, the project focuses on developing the complete physical mechanism from the ground up.
