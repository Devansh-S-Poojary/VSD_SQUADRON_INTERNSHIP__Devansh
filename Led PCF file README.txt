set_io  led_red	39
set_io  led_blue 40
set_io  led_green 41
set_io  hw_clk 20
set_io  testwire 17

This is the original pcf file
The RGB led has internal connections(39, 40, 41)
If we want an External RGB led, and it has 3+ and 1- pin, we can connect them in this way

set_io led_red    4
set_io led_green  6
set_io led_blue   9
set_io  hw_clk 20
set_io  testwire 17

As it has an internal clock, it is not necessary to connect and external clock.
Same with the testwire



