# Radio Communication

Radio communication between ground station and flight controler is based on NRF24L01 chip.
This chip is a transceiver, which means that it can send and receive data, but it can do only ONE ROLE at the time. It means that it can be either in send OR receive mode. Which means, that in order to be able to send and receive data to and from flight controller we need to switch roles (for example when flight controller sends data, ground station is in receiver mode, then they switch roles and ground station will send command to flight controller, then they again switch roles again and again and again...)

## Configuration
The only configuration that you will need to change is the TX and RX addresses. This address is just an array of bytes (the size of the array <= 6).

TX address on ground station should be equal to RX address on flight controller (because ground station transmits data and flight controller receives it).
RX address on ground station should be equal to TX address on flight controller (because ground station receives data and flight controller transmits it).

## Example
Go to ground_station.ino file and find these lines

``` cpp title="ground_station.ino" 
const byte RxAddress[5] = {0xEE,0xDD,0xCC,0xBB,0xAA};
const byte TxAddress[5] = {0xAA,0xDD,0xCC,0xBB,0xAA};
```

You can also set address as the string
``` cpp
const byte RxAddress[5] = "00001";
const byte TxAddress[5] = "00002";
```

And now go to the main.cpp file for flight controller code and set respectful addresses their as well:

``` cpp title="main.cpp" 
const uint8_t RxAddress[] = {0xAA,0xDD,0xCC,0xBB,0xAA};
const uint8_t TxAddress[] = {0xEE,0xDD,0xCC,0xBB,0xAA};
```

Or

``` cpp
const byte RxAddress[5] = "00002";
const byte TxAddress[5] = "00001";
```

## Communication Data

NRF24L01 chip can send or receive 32 bytes of data at once. Under the hood it accepts the byte array which you want to send. But for easier communication we've built structs for sending telemetry data and commands (under the hood, struct will be converted to bytes and then on receiver side, these bytes will be translated to struct).

We have struct called sensor_packet_t which will be sent to ground station