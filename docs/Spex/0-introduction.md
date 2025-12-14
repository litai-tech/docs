# Introduction

Litai Spex is a dual-brain development board built on the RP2350B MCU and Radxa CM3 module.

This product helps you build applications that require an MCU for sensor readings, servo/motor control, and RF communication, while displaying and interacting with data through a touchscreen.

The key advantage of using a Linux machine for the GUI is the ability to build your interface with web technologies (HTML, CSS, and JavaScript/TypeScript). This means you can leverage existing libraries to speed up development—adding charts, complex tables, or even 3D graphics.

## How it works

Litai Spex has two main processing units: the RP2350B MCU and the Radxa CM3.

The RP2350B MCU handles communication with sensors, servos, motors, RF modules, and other peripherals. We chose it for its performance and the extensive libraries and examples available online.

The Radxa CM3 hosts an Electron-based GUI app and communicates with the RP2350B over a serial interface. You can build your UI using standard web technologies and use our API to send commands to and receive data from the RP2350B.

This combination makes it easy to build products that need both external device communication and a user-friendly GUI. The compact form factor and USB PD power support also make it a portable solution for field use—displaying drone telemetry data, for example.