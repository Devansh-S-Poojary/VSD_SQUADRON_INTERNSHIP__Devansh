set_io  led_red	39
set_io  led_blue 40
set_io  led_green 41
set_io  hw_clk 20
set_io  testwire 17

This is the original pcf file.
The RGB led has internal connections(39, 40, 41).
If we want an External RGB led, and it has 3+ and 1- pin, we can connect them as shown below.

As it has an internal clock, it is not necessary to connect and external clock.
Same with the testwire

**UPDATED CODE**
set_io led_red    4
set_io led_green  6
set_io led_blue   9
set_io  hw_clk 20
set_io  testwire 17

As the pins 4, 6, and 9 are PWM pins, assign pins 4, 6, and 9 to the red, green, and blue positive pins on an external RGB LED respectively.​

Physically connect the RGB LED's anodes to GPIO pins 4, 6, and 9 through appropriate current-limiting resistors (typically 220Ω to 470Ω). Connect the common cathode to the ground (GND) of the FPGA board.
