# Getting Started

Colir One is the flight controller which has built in:

* STM32F405 MCU
* Powerful IMU - BNO055
* Pressure sensor for altitude and vertical velocity estimation
* Pins to connect your favourite GPS
* 8 outputs for servo (5V, up to 2A current)
* 6 outputs for e-igniters (5V, up to 3A)
* NRF24L01 - for data telemetry
* 8MB flash memory for logs
* SD card slot to copy logs from flash memory
* Debug interface to debug your code

To use flight controller you will also need Ground Station, which will receive telemetry data from flight controller and will display some data on built-in screen and output all the data via USB Serial interface.

``` cpp
int a = 115;
```