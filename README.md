# VSD SQUADRON FPGA Mini Internship 
---
<details>
  <summary> 
    
# Task-1 
  </summary>
          
# Objective:
     
Participants are expected to understand and document the provided Verilog code, create the necessary PCF file, and integrate the design with the VSDSquadron FPGA Mini board using the provided datasheet.
  <details>
  <summary> 
    
# Step 1 Understanding the Verilog Code
  </summary>
 This is the Verilog code link- https://github.com/thesourcerer8/VSDSquadron_FM/blob/main/led_blue/top.v 
Remove the first # while using


 ```bash
  #module top (
  output wire led_red  , 
  output wire led_blue , 
  output wire led_green , 
  input wire hw_clk, 
  output wire testwire
);

  wire        int_osc            ;
  reg  [27:0] frequency_counter_i;

  assign testwire = frequency_counter_i[5];
 
  always @(posedge int_osc) begin
    frequency_counter_i <= frequency_counter_i + 1'b1;
  end

  SB_HFOSC #(.CLKHF_DIV ("0b10")) u_SB_HFOSC ( .CLKHFPU(1'b1), .CLKHFEN(1'b1), .CLKHF(int_osc));

  SB_RGBA_DRV RGB_DRIVER (
    .RGBLEDEN(1'b1                                            ),
    .RGB0PWM (1'b0), // red
    .RGB1PWM (1'b0), // green
    .RGB2PWM (1'b1), // blue
    .CURREN  (1'b1                                            ),
    .RGB0    (led_red                                       ), 
    .RGB1    (led_green                                       ),
    .RGB2    (led_blue                                        )
  );
  defparam RGB_DRIVER.RGB0_CURRENT = "0b000001";
  defparam RGB_DRIVER.RGB1_CURRENT = "0b000001";
  defparam RGB_DRIVER.RGB2_CURRENT = "0b000001";

endmodule
```
---
Now let us understand what is it

```bash
1. This Verilog module, named "top", is designed for a FPGA board
   We have the Outputs 
2. "led_red, led_blue, led_green": Control an RGB LED.
3. "testwire": Outputs a signal derived from an internal counter.
4. the "int_osc" is the Internal Oscillator output.
   And as well as Inputs
5. "hw_clk": An external hardware oscillator
6. "frequency_counter_i"is a counter that increments on every internal clock cycle.
7. There is a High Frequency Internal Oscillator, That is "SB_HFOSC"
8. The function "0b10" Divides the base clock by 4, that is, divides 48MHz by 4, to give 12MHz
9. There is a RGB led driver "SB_RGBA_DRV"
10. There are few PWM input too
"RGB0PWM = 0" → Red LED OFF
"RGB1PWM = 0" → Green LED OFF
"RGB2PWM = 1" → Blue LED ON
"defparam" sets the lowest brightness (Default Parameters)
There is also a code line stating the RGB numbers-
RGB0 → led_red
RGB1 → led_green
RGB2 → led_blue
 ```
# Now what is the Purpose of the Module?
~Generates an internal clock using an FPGA’s high-frequency oscillator (SB_HFOSC).
~Implements a 28-bit counter to create a lower-frequency signal.
~Drives an RGB LED using the SB_RGBA_DRV hardware block.
~Outputs a test signal (testwire) from the counter’s 5th bit, creating a low-frequency square wave.

# Description of internal logic and oscillator 
```bash
"SB_HFOSC" is an Internal Oscillator

SB_HFOSC #(.CLKHF_DIV ("0b10")) u_SB_HFOSC ( 
 .CLKHFPU(1'b1), 
 .CLKHFEN(1'b1), 
 .CLKHF(int_osc)
 ) ;

SB_HFOSC is a built-in FPGA high-frequency oscillator.
CLKHF_DIV = "0b10" sets the oscillator frequency to 12 MHz (48 MHz ÷ 4).
The output int_osc is the clock signal used in the module.
There is a Frequency counter too
A 28-bit counter (frequency_counter_i) increments on every positive clock edge.
Since int_osc is 12 MHz, the counter increases every 83.3 ns.
testwire toggles at ~187.5 kHz, acting as a test signal.
 ```
# What is the Functionality of the RGB LED Driver and Relationship to Outputs
First let us check waht is the RGB LED driver here
```bash

  SB_RGBA_DRV RGB_DRIVER (
    .RGBLEDEN(1'b1),
    .RGB0PWM (1'b0), 
    .RGB1PWM (1'b0),
    .RGB2PWM (1'b1),
    .CURREN  (1'b1),
    .RGB0    (led_red), 
    .RGB1    (led_green),
    .RGB2    (led_blue)
  );
  defparam RGB_DRIVER.RGB0_CURRENT = "0b000001";
  defparam RGB_DRIVER.RGB1_CURRENT = "0b000001";
  defparam RGB_DRIVER.RGB2_CURRENT = "0b000001";

endmodule


Here you can see that there is a driver, "SB_RGBA_DRV"
This is a special hardware block that directly drives an RGB LED.
RGBLEDEN = 1'b1 enables the driver.
PWM Inputs
RGB0PWM = 0 - Red LED OFF
RGB1PWM = 0 - Green LED OFF
RGB2PWM = 1 - Blue LED ON
Outputs
RGB0 → led_red
RGB1 → led_green
RGB2 → led_blue
These connect to the actual LED pins.
Check the 
Sets the current (brightness) for each LED color.
0b000001 = low brightness.
The Blue LED (led_blue) is always ON, while Red and Green are OFF.
The LED pins (led_red, led_green, led_blue) directly connect to the RGB LED hardware.
TIP-Modifying RGBxPWM dynamically could change the LED color.
```
Wanted the table for the functionality??
Here you go
![image](https://github.com/user-attachments/assets/2c5673c4-0e2a-424f-8845-3fb1efb36ba2)

</details>

<details>
  <summary>
    
# Step 2: Creating the PCF File
  </summary>
  
  Access the PCF file from the provided link: https://github.com/thesourcerer8/VSDSquadron_FM/blob/main/led_blue/VSDSquadronFM.pcf
  This the code
  ```bash
set_io  led_red	39
set_io  led_blue 40
set_io  led_green 41
set_io  hw_clk 20
set_io  testwire 17
```
# What does it say?
set_io → Assigns a Verilog signal to a specific physical FPGA pin.
<led_blue> (example) → The name of the signal in the Verilog code.
<40> (example) → The pin number
```bash
Name of Signal   Pin number     Functionality
led_red	           39	        Red LED output
led_blue	   40	        Blue LED output
led_green	   41	        Green LED output
hw_clk	           20	        External hardware clock input
testwire	   17	        Test signal output
```
The pin mappings are connected to the chip.

```
Pin	Signal	  Function in Verilog	          Function in Hardware
39	led_red	         Controls red LED	          Red LED output
40	led_blue	 Controls blue LED	          Blue LED output (Always ON)
41	led_green	 Controls green LED	          Green LED output (OFF)
20	hw_clk	         Unused in Verilog	          Reserved for external clock
17	testwire	 Outputs a slow test signal	  Debugging pin
```
# What does it Imply?
**The blue LED is always on (1'b1).**

**The red and green LEDs are off (1'b0).**

**The design uses an internal oscillator, so hw_clk is unused.**

**Test Wire outputs a debugging signal that can be checked with an oscilloscope.**

# Creating a PCF file
Access the pcf file from the attachments from this repo

</details>
