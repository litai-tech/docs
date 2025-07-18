# Buttons

Ground station has 2 buttons, which you can customize as you want. But in our example we use it to control parachute and to send launch rocket command.

The code for buttons is simple: there are 2 functions which are used as callbacks. It means that when there is an button event (button pressed and button released) respected function will be called. In our case those functions are:

``` cpp
void buttonPressedCallback(uint8_t pinIn)
void buttonReleasedCallback(uint8_t pinIn)
```

Where "pinIn" parameter is a GPIO pin of the button. Those pins are defined on these lines of code:

``` cpp
#define BUTTON_1                            13
#define BUTTON_2                            14
```

So, to add event handling, you can just add code in the function. Here is an example:

``` cpp
if(pinIn == BUTTON_1){
    colirone_payload_cmd_t colirone_payload_cmd = {0};
    colirone_payload_cmd.lighter_launch_number = 1;
    enqueue_cmd(colirone_payload_cmd);
}
else if(pinIn == BUTTON_2){
    colirone_payload_cmd_t colirone_payload_cmd = {0};
    colirone_payload_cmd.open_shutes = 1;
    enqueue_cmd(colirone_payload_cmd);
}
```

But if you don't want to use 1 callback for both buttons, you can just change callbacks registration in setup() function:

``` cpp
openShutesButton.registerCallbacks(button1PressedCallback, button1ReleasedCallback);
closeShutesButton.registerCallbacks(button2PressedCallback, button2ReleasedCallback);
```

And add new functions somewhere at the bottom of the file with the code

``` cpp
void button1PressedCallback(uint8_t pinIn)
{
    //handle here pressed event for BUTTON_1
}

void button1ReleasedCallback(uint8_t pinIn)
{
    //handle here released even for BUTTON_1
}

//and the same for button2 functions
```