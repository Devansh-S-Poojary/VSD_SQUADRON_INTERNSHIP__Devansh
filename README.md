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
  #module top (
  output wire led_red  , 
  output wire led_blue , 
  output wire led_green , 
  input wire hw_clk, 
  output wire testwire
);
 ```
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

</details>
