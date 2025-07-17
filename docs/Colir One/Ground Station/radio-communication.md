# Radio Communication

Radio communication between ground station and flight controler is based on NRF24L01 chip.
This chip is a transceiver, which means that it can send and receive data, but it can do only ONE ROLE at the time. It means that it can be either in send OR receive mode. Which means, that in order to be able to send and receive data to and from flight controller we need to switch roles (for example when flight controller sends data, ground station is in receiver mode, then they switch roles and ground station will send command to flight controller, then they again switch roles again and again and again...)

## Configuration
The only configuration that you will need to change is the TX and RX addresses. This address is just an array of bytes (the size of the array <= 6).

TX address on ground station should be equal to RX address on flight controller (because ground station transmits data and flight controller receives it).
RX address on ground station should be equal to TX address on flight controller (because ground station receives data and flight controller transmits it).

### Example
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

### sensor_packet_t

We have struct called sensor_packet_t which will be packed to bytes and sent to ground station.
It looks like this:

``` cpp
typedef struct __attribute__((packed)) {
    uint8_t type;
    uint8_t packet_type;      
    uint32_t timestamp;
    uint8_t data[26]; // 32 - 1 - 5 = 26
} sensor_packet_t;
```

#### type

??? info "type"
    "type" is the type of packet (0 for data, type != 0 for custom data). So, on ground station, we will take the first byte from the packet, and will check if it's "0" -> we will try to parse it to sensor_packet_t struct, if type != 0 -> you can add your custom handler.

#### packet_type

??? info "packet_type"
    "packet_type" is related to the type of sensor data type.

Packet types described as enum:

``` cpp
typedef enum{
    RF_ACCELERATION = 0,
    RF_GYROSCOPE,
    RF_ORIENTATION,
    RF_QUATERNION,
    RF_BAROMETER,
    RF_GPS,
    RF_VERTICAL_VELOCITY,
} rf_packet_type_t;
```

#### timestamp

??? info "timestamp"
    "timestamp" is the timestamp when the data was read from sensor.

#### data

??? info "data"
    "data" is actual data from sensor converted to bytes.

### Received Data Handling

So, when ground station will handle received data it will take the first byte, check if it's sensor data type, if yes -> check packet_type and convert "data" bytes to actual sensor struct. And then, you can display this data on the display or send data via serial to the application on a laptop.