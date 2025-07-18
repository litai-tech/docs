# Radio Communication

Radio communication between ground station and flight controler is based on nRF24L01 chip.
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

## Switching TX/RX modes

As it was mentioned before, nRF24L01 chip cannot receive and transmit data at once, it needs to be eaither in RX or in TX mode. In order to solve this, ground station is always in RX mode, but flight controller can send specific command to ground station, which will tell ground station to switch to TX mode, which will tell ground station, that flight controller is switched to RX mode and started listening to commands from ground station. This intervals can be configured on flight controller side. And if ground station has command to send, it sends it to flight controller and switches to RX mode back and flight controller switches to TX mode, to continue sending the telemetry data.

When you use our code for ground station, to switch to TX role and send command, you can use transmit_cmd function:

``` cpp
void transmit_cmd(colirone_payload_cmd_t colirone_payload_cmd)
```

It will stop listening (switch to TX mode), send the command and will start listening (switch to RX mode).

## Communication Data

nRF24L01 chip can send or receive 32 bytes of data at once. Under the hood it accepts the byte array which you want to send. But for easier communication we've built structs for sending telemetry data and commands (under the hood, struct will be converted to bytes and then on receiver side, these bytes will be translated to struct).

## Receiving Data

When nRF24L01 chip is in RX mode, it will "listen" for incoming data on RxAddress and when there is some data radio.available() will return "true". Then you need to read data from chip to your local buffer:

``` cpp
if(adio.available())
    radio.read(buffer, sizeof(buffer));
```

And then you can convert this buffer to your data. In our code we use the first byte to indicate what kind of data was sent (is it data from sensors or "switch to TX mode" command).

``` cpp
uint8_t packet_type = buffer[0];
switch (packet_type) {
    case 1: {
        // handle switch to TX mode command and send command to flight controller
        break;
    }
    case 0: {
        //handle received sensor data
        sensor_packet_t sensor_packet;
        memcpy(&sensor_packet, buffer, sizeof(sensor_packet_t));
        break;
    }
    default:
        Serial.println("Unknown packet type received.");
        break;
}
```

### sensor_packet_t

In order to send telemetry data, we have struct called sensor_packet_t which will be packed to bytes and sent to ground station.
It looks like this:

``` cpp
typedef struct __attribute__((packed)) {
    uint8_t type;
    uint8_t packet_type;      
    uint32_t timestamp;
    uint8_t data[26]; // 32 - 1 - 1 - 4 = 26
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

## Sending Data

As we assume, that user will send only commands (open close shutes, launch rocket, start logging, etc.) from ground station to flight controller, we've build this communication based on commands. 

### colirone_payload_cmd_t

"colirone_payload_cmd_t" struct has fields, which are actually "flags" what flight controller should to do. And in flight controller you should add handling to these commands (for example your parachute can be ignited by charge or released by servo).

??? info "colirone_payload_cmd_t"
    ``` cpp
    typedef struct __attribute__((packed)) {
        uint8_t lighter_launch_number;
        uint8_t close_shutes;
        uint8_t open_shutes;
        uint8_t start_logs;
        uint8_t write_logs;
        uint8_t reset_altitude;
        uint8_t remove_logs;
    } colirone_payload_cmd_t;
    ```

For example, you want to send "open_shutes" command. (this event can be triggered by button or received command from serial port). You need only to add this command to the queue and it will be sent during next switch to TX role.

``` cpp
colirone_payload_cmd_t colirone_payload_cmd = {0};
colirone_payload_cmd.open_shutes = 1;
enqueue_cmd(colirone_payload_cmd);
```