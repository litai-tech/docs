# Display

Ground Station uses OLED display with SSD1306 driver connected to I2C bus. The resolution is 128x64. This is small, but allows us to keep the cost low, but still more or less enough to display the most critical data. To render all info, we use out UI App, which you can run on your laptop. 

We are using U8g2 library for rendering data on the display.

As main info, we've chose next values: Altitude and Vertical Velocity (Line 1 and Line 2). Also display indicates each button function. 

Main rendering implemented in oledMainScreenDisplay() function:

``` cpp
void oledMainScreenDisplay(String verticalVelocity, String altitude){
  u8g2.clearBuffer();
  //Line 1
  u8g2.drawGlyph(0*FONT_WIDTH, FONT_HEIGHT, 0x2195); // 8 symbols before
  u8g2.setCursor(2*FONT_WIDTH, FONT_HEIGHT);
  u8g2.print("Velocity");
  u8g2.setCursor(10*FONT_WIDTH, FONT_HEIGHT); // 10 symbols before
  u8g2.print(":");
  u8g2.setCursor(13*FONT_WIDTH, FONT_HEIGHT); //13 or 14
  u8g2.print(verticalVelocity + "m/s");

  //Line 2
  u8g2.setCursor(0*FONT_WIDTH, 2*FONT_HEIGHT + LINE_SPACING);
  u8g2.print("Altitude:");
  u8g2.setCursor(13*FONT_WIDTH, 2*FONT_HEIGHT + LINE_SPACING); //13 or 14
  u8g2.print(altitude + "m");

  //Line 3
  const char* line3_str = "Shutes";
  u8g2.setCursor(OLED_CENTER*FONT_WIDTH - (strlen(line3_str) / 2)*FONT_WIDTH, 3*FONT_HEIGHT + LINE_SPACING*2);
  u8g2.print(line3_str);
  //Line 4
  u8g2.drawGlyph(0*FONT_WIDTH, 4*FONT_HEIGHT + LINE_SPACING*3, 0x2193);
  u8g2.setCursor(1*FONT_WIDTH, 4*FONT_HEIGHT + LINE_SPACING*3);
  u8g2.print("Open");
  const char* close_str = "Close";
  u8g2.setCursor((OLED_MAX_SYMBOLS_IN_1_LINE)*FONT_WIDTH - 1*FONT_WIDTH - (strlen(close_str)*FONT_WIDTH), 4*FONT_HEIGHT + LINE_SPACING*3);
  u8g2.print(close_str);
  u8g2.drawGlyph((OLED_MAX_SYMBOLS_IN_1_LINE)*FONT_WIDTH - 1*FONT_WIDTH, 4*FONT_HEIGHT + LINE_SPACING*3, 0x2193);
  u8g2.sendBuffer();
}
```

But you can use display to show any information you want. Just change function parameters to values that you want to display and call oledMainScreenDisplay() in the loop function.