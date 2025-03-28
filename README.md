# VSD SQUADRON FPGA Mini Internship 
---
<details>
  <summary> 
    
# Getting Started 
  </summary>

Refer to the Datasheet (check the files of this repo) to install the required things.

   Let's start the virtual box(I have changed the Desktop background!)
![image](https://github.com/user-attachments/assets/22ad717b-27bb-473d-b614-9c4bdd4872a4)

Lets open the Terminal
![image](https://github.com/user-attachments/assets/e86f69ee-5b65-4d5c-b2b8-c8cfcc6da551)

Lets check the Blink_Led file (Which is a sample file pre-stored)
![image](https://github.com/user-attachments/assets/064969bd-537d-449a-b7a9-ab5d0e1ac8a7)

There were only 3 files when I installed. But when I run the code, there were more files which are generated. 

These are the real files (I deleted the generated files 😃)
![image](https://github.com/user-attachments/assets/e4b37ab0-3b3d-45eb-ac14-df3bed1a9a71)

- .v file stands for "Verilog" file

- .pcf file stands for "Physical Constraints File"

- And a Makefile is a script used with the "make" build system to automate repetitive tasks in compiling, synthesizing, and uploading designs to an FPGA. It defines rules for building projects efficiently, ensuring dependencies are handled correctly.
- use the command "nano" to open these files

## Now let's check what is inside all of these files.

![image](https://github.com/user-attachments/assets/fab32f5e-1871-476a-9a48-f71abd3a5968)

As mentioned, we use "nano" before each of these files to see what is inside them
![image](https://github.com/user-attachments/assets/0acc4dcf-abfa-4139-ae67-2b2ebda498a2)

# Let's see in VSDSquadron_FM.pcf file

![image](https://github.com/user-attachments/assets/992258ea-82ca-4a26-a9bc-cd2759b76cb8)

You can see this inside it

![image](https://github.com/user-attachments/assets/b83890ac-4a14-4971-a07d-4472548e80c6)

# Now we will se inside the rgb_blink.v

![image](https://github.com/user-attachments/assets/4e67e481-7dd6-4cb9-8cdd-915c14d8bcd7)

![image](https://github.com/user-attachments/assets/45fe7680-7d0f-4dba-b413-1b9a4f9916eb)

# Makefile

![image](https://github.com/user-attachments/assets/32ea5536-c963-4279-88a3-dff5911945ed)


![image](https://github.com/user-attachments/assets/5b08986b-d3da-47ee-9757-f1d75b2d036f)

# rgb_blink.json

![image](https://github.com/user-attachments/assets/5a65662a-2d46-4442-b73f-4d8c84d498ef)

# rgb_blink.asc

![image](https://github.com/user-attachments/assets/8cdcda38-104e-4aed-8221-909ee2082053)

# check rgb_blink.timings

![image](https://github.com/user-attachments/assets/e7b3cb35-f627-476d-9849-e3fdcf1b26ad)

# So we know the basics, right? Come, lets check the Tasks and their steps 🥳

  </details>
    
<details>
  <summary> 
    
# Task-1 
  </summary>
  
## Objective:
               
Participants are expected to understand and document the provided Verilog code, create the necessary PCF file, and integrate the design with the VSDSquadron FPGA Mini board using the provided datasheet.

## Step 1 Understanding the Verilog Code
 

 This is the Verilog code link- 
 
 https://github.com/thesourcerer8/VSDSquadron_FM/blob/main/led_blue/top.v 


 ```bash
  module top (
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

- Generates an internal clock using an FPGA’s high-frequency oscillator (SB_HFOSC).

- Implements a 28-bit counter to create a lower-frequency signal.
  
- Drives an RGB LED using the SB_RGBA_DRV hardware block.
  
- Outputs a test signal (testwire) from the counter’s 5th bit, creating a low-frequency square wave.

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


## Step 2: Creating the PCF File
  
  
  Access the PCF file from the provided link: 
  
  https://github.com/thesourcerer8/VSDSquadron_FM/blob/main/led_blue/VSDSquadronFM.pcf 
  
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
Access the pcf file from the attachments, They are working in my board successfully.

## Step-3 Implement in VSDsquadron  

  First lets check the Blue project
   Refer to the Datasheet to upload it. these are the results
   ---
   ![image](https://github.com/user-attachments/assets/ac9cc3ea-309f-4a08-95ec-8950bc8f115b)
The Blue LED is ON 

https://github.com/Devansh-S-Poojary/VSD_SQUADRON_INTERNSHIP__Devansh/blob/main/FPGA_-_Task-_-1.mp4 

# Now lets check my own project
These were the stunning results
---
I had a hard time taking the photos of the superfast blinker

# first 0.5 second
![image](https://github.com/user-attachments/assets/9ee41d0a-8c31-4d53-a3eb-8b59579aa71d)

# The red led is on

# Immediately, 
![image](https://github.com/user-attachments/assets/f275b34b-bbc4-4358-a114-b8cc4f4fa038)

# The blue is on

 **And this continues**

   ## Step-4 Final Documentation

 
 # Summary of the Verilog Code Functionality
 
The given Verilog code is designed for an FPGA board to drive an RGB LED and generate a test signal. It uses an internal high frequency oscillator to increment a counter, which in turn affects the test output. The RGB LED driver is instantiated but hardcoded to enable only the blue LED.

# Key Components of the Code:

# 1. Internal Oscillator (SB_HFOSC)

- Generates the clock signal (int_osc), which is used as the main timing source.

- The frequency divider is set to "0b10", which likely corresponds to a specific clock frequency.

# 2. Counter (frequency_counter_i)

- A 28-bit counter increments on every clock cycle of int_osc.

- The 6th bit (frequency_counter_i[5]) is used as an output (testwire), likely generating a low-frequency test signal.

# 3. RGB LED Driver (SB_RGBA_DRV)

- Controls the onboard RGB LED with RGB0, RGB1, and RGB2 connected to led_red, led_green, and led_blue, respectively.

- Only the blue LED (RGB2PWM) is enabled (1'b1), meaning the LED will only light up blue.

- The brightness for each LED channel is set to the lowest level (0b000001).

## Pin Mappings

```
Pin	Signal	  Function in Verilog	          Function in Hardware
39	led_red	         Controls red LED	          Red LED output
40	led_blue	 Controls blue LED	          Blue LED output (Always ON)
41	led_green	 Controls green LED	          Green LED output (OFF)
20	hw_clk	         Unused in Verilog	          Reserved for external clock
17	testwire	 Outputs a slow test signal	  Debugging pin
```
# Integration Steps and Observations while Working with the FPGA Mini Board

## 1. Synthesis & Implementation:

- The Verilog code needs to be synthesized using a tool like Yosys and implemented with NextPNR or a similar FPGA place-and-route tool.

- The PCF file ensures the correct mapping of logic to physical FPGA pins.

## 2. Programming the FPGA:

- After generating the bitstream, it must be loaded onto the FPGA using OpenFPGALoader or vendor-specific tools.

## 3. Hardware Testing:

- Upon power-up, the onboard oscillator starts running.

- The counter increments, affecting testwire, which can be probed with an oscilloscope or logic analyzer.

- The RGB LED should glow blue because of the SB_RGBA_DRV configuration.

- If there are any Mistakes, or the output is not as expected, this is a way to correct it.

# Challenges Faced

Overall, I have faced many challenges like

- Virtual Box is not responding
  
- USB Device not detected
  
- Code mappings are mismatched
  
- Timing mismatch and so on.
  

For **MOST OF THESE PROBLEMS**, the Solution was restarting the computer and Virtual box. But these were the extra solutions for them, respectively.

- Restart the Virtual box/Computer OR Check if it has been properly installed as per the datasheet.
  
- Go to Devices in Virtual machine>USB>And then select the board name (FTDI Single) an so on OR Restart the machine OR check the USB cable.
  
- Recheck the code again and find the mistakes
  
- Restart the machine OR check the code.
  

</details>

<details>
  <summary> 
    
# Task-2
  </summary>


## Step-1 Study the Existing Code:

Access the code from [the Repository](https://github.com/thesourcerer8/VSDSquadron_FM/tree/main/uart_loopback)

## What is inside the code (uart_trx.v)?

```
// 8N1 UART Module, transmit only

module uart_tx_8n1 (
    clk,        // input clock
    txbyte,     // outgoing byte
    senddata,   // trigger tx
    txdone,     // outgoing byte sent
    tx,         // tx wire
    );

    /* Inputs */
    input clk;
    input[7:0] txbyte;
    input senddata;

    /* Outputs */
    output txdone;
    output tx;

    /* Parameters */
    parameter STATE_IDLE=8'd0;
    parameter STATE_STARTTX=8'd1;
    parameter STATE_TXING=8'd2;
    parameter STATE_TXDONE=8'd3;

    /* State variables */
    reg[7:0] state=8'b0;
    reg[7:0] buf_tx=8'b0;
    reg[7:0] bits_sent=8'b0;
    reg txbit=1'b1;
    reg txdone=1'b0;

    /* Wiring */
    assign tx=txbit;

    /* always */
    always @ (posedge clk) begin
        // start sending?
        if (senddata == 1 && state == STATE_IDLE) begin
            state <= STATE_STARTTX;
            buf_tx <= txbyte;
            txdone <= 1'b0;
        end else if (state == STATE_IDLE) begin
            // idle at high
            txbit <= 1'b1;
            txdone <= 1'b0;
        end

        // send start bit (low)
        if (state == STATE_STARTTX) begin
            txbit <= 1'b0;
            state <= STATE_TXING;
        end
        // clock data out
        if (state == STATE_TXING && bits_sent < 8'd8) begin
            txbit <= buf_tx[0];
            buf_tx <= buf_tx>>1;
            bits_sent = bits_sent + 1;
        end else if (state == STATE_TXING) begin
            // send stop bit (high)
            txbit <= 1'b1;
            bits_sent <= 8'b0;
            state <= STATE_TXDONE;
        end

        // tx done
        if (state == STATE_TXDONE) begin
            txdone <= 1'b1;
            state <= STATE_IDLE;
        end

    end

endmodule
```

## What is the meaning of the code?

```
- This is a UART (Universal Asynchronous Receiver-Transmitter) transmitter module written in Verilog.
It sends 8-bit data serially over a single wire (tx).
The communication format used is 8N1, meaning:
~ 8 data bits
~ No parity bit
~ 1 stop bit
```

# Working 

## 1. Idle State ("STATE_IDLE)

- "tx" stays HIGH (default idle state for UART).

- If "senddata" is HIGH, it loads "txbyte" (8-bit data) into "buf_tx" and moves to the next state.

## 2. Start Bit ("STATE_STARTTX")

- The first thing a UART transmission does is send a start bit (LOW).

- The module sets "tx" to "0" to indicate the start of transmission.

## 3. Sending 8 Data Bits ("STATE_TXING")

- The module transmits the least significant bit (LSB) first.

- Each bit is shifted out serially, and "tx" takes the value of "buf_tx[0]".

- This process repeats until all 8 bits are sent.

## 4. Stop Bit ("STATE_TXDONE")

- After the 8 data bits, it sends a stop bit (HIGH) to signal the end of transmission.

- "txdone" is set to "1" to indicate the byte has been sent.

## 5. Returning to Idle

- After the stop bit, "tx" stays HIGH, and the module returns to "STATE_IDLE".

## The whole function in just 5 points

- This is a transmit-only UART module.
- The data is sent bit by bit over the "tx" line.
- It follows the 8N1 UART protocol (Start → 8 bits → Stop).
- The "clk" (clock) controls the timing of each state transition.
- The "txdone" signal tells when the transmission is complete.

  # Step-2 Design Documentation

- ## Create a block diagram illustrating the UART loopback architecture.

First we will see what is inside the .pcf file

```
set_io  led_green 40
set_io  led_red	39
set_io  led_blue 41
set_io  uarttx 14
set_io  uartrx 15
set_io  hw_clk 20
```

  The .pcf file states that,
  - Green LED pin is 40
  - Red LED pin is 39
  - Blue LED pin is 41
  - uarttx pin is 14
  - uartrx pin is 15
  - hw_clk pin is 20

   ## ROUGH SKETCH

  ### 1. Preparation 

  I am using the MS Paint tool to create it

  First we use various shapes and fill them with colour
![image](https://github.com/user-attachments/assets/b31814af-4971-463c-9ebc-24d30bb090b4)

Then we start adjusting it by erasing etc

This is our outcome

![image](https://github.com/user-attachments/assets/24f6891c-40a2-4405-a5ba-6168aaa69a2b)

Thus this is the Final [Block Diagram](https://github.com/user-attachments/assets/a015b5cd-85a8-41bc-be4f-ce593909c2c7) created by me.
   ## Develop a detailed circuit diagram showing connections between the FPGA and any peripheral devices used.

   The circuit is almost similar to the block diagram.

   I used wokwi to do it.

   I searched the web and found an [FPGA Project](https://wokwi.com/projects/411237214736236545)  

   Now we can use it to create our circuit.

- I tried using simply a custom chip as my FPGA, with the connections.
- The RGB led is for the LED's.
- I used simply a LED for rx signal.
- I used pushbutton for tx.
- And a buzzer for the clock

- OFCOURSE, the circuit is not working, but for visualisation, this is good.

  ![image](https://github.com/user-attachments/assets/63068931-aa20-4c1a-9601-81f35d5b0086)

  Here is the [circuit diagram](https://wokwi.com/projects/426582088903001089)

 # 3. Implementation

These were the results

![WhatsApp Image 2025-03-27 at 18 37 19_1256cebb](https://github.com/user-attachments/assets/e5fdf01a-35fd-465b-88f5-968224972eab)

# 4. Testing and Verification

When I Searched the WEB, I Found Docklight.
    
Open Docklight - and check that our Real system is connected to the right com port.
Then double click on the blue box in send sequences and enter a name, select a format and then write anything, click "Apply" and then verify that this has entered in send sequences. Then, click the arrow beside the name and verify the result is as follows:

![image](https://github.com/user-attachments/assets/298fc743-6d51-49a3-94af-cc4c573a1d31)

![image](https://github.com/user-attachments/assets/47a7f864-2fa9-458e-9380-3b264b0f8904)

https://github.com/user-attachments/assets/443cf339-d2ac-45a5-885c-c1fdc74a46ed


 </details>

<details>
  <summary> 
    
# Task-3
  </summary>
  
## What does the verilog ode say?

```
// 8N1 UART Module, transmit only

module uart_tx_8n1 (
    clk,        // input clock
    txbyte,     // outgoing byte
    senddata,   // trigger tx
    txdone,     // outgoing byte sent
    tx,         // tx wire
    );

    /* Inputs */
    input clk;
    input[7:0] txbyte;
    input senddata;

    /* Outputs */
    output txdone;
    output tx;

    /* Parameters */
    parameter STATE_IDLE=8'd0;
    parameter STATE_STARTTX=8'd1;
    parameter STATE_TXING=8'd2;
    parameter STATE_TXDONE=8'd3;

    /* State variables */
    reg[7:0] state=8'b0;
    reg[7:0] buf_tx=8'b0;
    reg[7:0] bits_sent=8'b0;
    reg txbit=1'b1;
    reg txdone=1'b0;

    /* Wiring */
    assign tx=txbit;

    /* always */
    always @ (posedge clk) begin
        // start sending?
        if (senddata == 1 && state == STATE_IDLE) begin
            state <= STATE_STARTTX;
            buf_tx <= txbyte;
            txdone <= 1'b0;
        end else if (state == STATE_IDLE) begin
            // idle at high
            txbit <= 1'b1;
            txdone <= 1'b0;
        end

        // send start bit (low)
        if (state == STATE_STARTTX) begin
            txbit <= 1'b0;
            state <= STATE_TXING;
        end
        // clock data out
        if (state == STATE_TXING && bits_sent < 8'd8) begin
            txbit <= buf_tx[0];
            buf_tx <= buf_tx>>1;
            bits_sent = bits_sent + 1;
        end else if (state == STATE_TXING) begin
            // send stop bit (high)
            txbit <= 1'b1;
            bits_sent <= 8'b0;
            state <= STATE_TXDONE;
        end

        // tx done
        if (state == STATE_TXDONE) begin
            txdone <= 1'b1;
            state <= STATE_IDLE;
        end

    end

endmodule
```

The meaning-

#### The term 8N1 means:
- 8 data bits
- N (no) parity bit
- 1 stop bit

This module transmits (TX) serial data but does not receive (RX).

## 1. Inputs & Outputs

### Inputs:

- "clk": System clock.
- "txbyte [7:0]": 8-bit data to send.
- "senddata": Trigger signal to start transmission.

### Outputs:

- "txdone": Signals when transmission is complete.
- "tx": Serial TX output line.

## 2. State Machine for Transmission

The module defines four states:
- "STATE_IDLE (0)" → Waiting for "senddata"
- "STATE_STARTTX (1)" → Sends start bit (0)
- "STATE_TXING (2)" → Sends 8 data bits
- "STATE_TXDONE (3)" → Sends stop bit (1) and sets "txdone"

## 3. Working of the "always @ (posedge clk)" Block
### Step 1: Waiting for "senddata"

- If "senddata == 1" and the module is in "STATE_IDLE", it moves to "STATE_STARTTX".
- Stores "txbyte" in "buf_tx" for shifting later.

### Step 2: Sending Start Bit

-Moves to "STATE_TXING" and sends start bit (0).

## Step 3: Transmitting Data Bits

- Sends one bit per clock cycle.
- Shifts the "buf_tx" register to send the LSB first.

## Step 4: Sending Stop Bit

- Once all 8 bits are sent, sends stop bit (1).
- Moves to "STATE_TXDONE".

## Step 5: Indicating Completion

- Sets "txdone = 1" and returns to "STATE_IDLE".

## Block and Circuit Diagram


## Implementation

- Download and Install PuTTY
- Connect the FPGA to the computer.
- Choose the correct port and baud settings.
- Check that a series of "D" is being generated.

#### Jumping to the  virtual box

- Open the VM
- Go to terminal
- From [this Website](https://github.com/thesourcerer8/VSDSquadron_FM/tree/main/uart_tx), Download the makefile, pcf file and the 2 Verilog files, and then flash it into the FPGA.
- These are the combined results
  
Here is the Video of [Task-3 in PuTTY and Virtual Box](https://github.com/Devansh-S-Poojary/VSD_SQUADRON_INTERNSHIP__Devansh/blob/main/FPGA%23TASK%233%23DEVANSH.mp4)

## Testing and Verification

Check that a series of "D"s are generated and the RGB LED is blinking.

[Task-3 in PuTTY and Virtual Box](https://github.com/Devansh-S-Poojary/VSD_SQUADRON_INTERNSHIP__Devansh/blob/main/FPGA%23TASK%233%23DEVANSH.mp4)

While opening this file, if you get any error, try downloading and then watch it.

  </details>

 
