# LED

Ground Station uses 1 WS2812B component to display "connected" state. This component allows you to control the brightness and color (16M colors).

We are using Adafruit_NeoPixel library to control this LED.

You will only need to use 2 functions to control it: setBrightness() and setPixelColor();

To set brightness, just call

``` cpp
strip.setBrightness(10); // Set BRIGHTNESS to about 4% (max = 255)
```

We've chose 10, because on max brightness (255 value) it will be too bright and will distract user.

And to set the color use next function:

``` cpp
strip.setPixelColor(0, 255, 0, 0);
```

Where first parameter is always 0 (this parameter is responsible for LED index, as we have only 1 LED, so it will be always 0).
Next 3 parameters are "red", "green" and "blue" values. So, if you want to display "red" color, just set like this:

``` cpp
strip.setPixelColor(0, 255, 0, 0);
```

To set "green" color:

``` cpp
strip.setPixelColor(0, 0, 255, 0);
```

And "blue":

``` cpp
strip.setPixelColor(0, 0, 0, 255);
```

And you can use some online tools to pick the color you want, combining those R G B values.