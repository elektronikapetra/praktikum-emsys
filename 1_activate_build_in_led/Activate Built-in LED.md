# Activate Built-in LED

1. Open Vivado
2. Create new project, select ‘RTL Project’. Click ‘Next’

 

![Untitled](Activate%20Built-in%20LED/Untitled.png)

![Untitled](Activate%20Built-in%20LED/Untitled%201.png)

1. Goto Part Selection, select: xc7z020clg400-1 as for the PYNQ FPGA. Click “Next” and “Finish”

![Untitled](Activate%20Built-in%20LED/Untitled%202.png)

![Untitled](Activate%20Built-in%20LED/Untitled%203.png)

1. Create block diagram, Click “Ok”

![Untitled](Activate%20Built-in%20LED/Untitled%204.png)

1. Click “+” button on diagram. Select “Zynq7 processing system”

![Untitled](Activate%20Built-in%20LED/Untitled%205.png)

![Untitled](Activate%20Built-in%20LED/Untitled%206.png)

1. Click “Run Block Automation”. Click “Ok”

![Untitled](Activate%20Built-in%20LED/Untitled%207.png)

![Untitled](Activate%20Built-in%20LED/Untitled%208.png)

1. Sources > right click > add sources > next > create file > file name: “johnson_counter” > finish > ok > yes

![Untitled](Activate%20Built-in%20LED/Untitled%209.png)

![Untitled](Activate%20Built-in%20LED/Untitled%2010.png)

![Untitled](Activate%20Built-in%20LED/Untitled%2011.png)

![Untitled](Activate%20Built-in%20LED/Untitled%2012.png)

![Untitled](Activate%20Built-in%20LED/Untitled%2013.png)

![Untitled](Activate%20Built-in%20LED/Untitled%2014.png)

![Untitled](Activate%20Built-in%20LED/Untitled%2015.png)

![Untitled](Activate%20Built-in%20LED/Untitled%2016.png)

1. Open “johnson_counter”, replace code with code below: and save
    
    Link : [https://myhdl.org/docs/examples/jc2.html](https://myhdl.org/docs/examples/jc2.html)
    

```
module jc2 (
    goLeft,
    goRight,
    stop,
    clk,
    q
);
// A bi-directional 4-bit Johnson counter with stop control.
// 
// I/O pins:
// --------
// clk      : input free-running slow clock 
// goLeft   : input signal to shift left (active-low switch)
// goRight    : input signal to shift right (active-low switch)
// stop     : input signal to stop counting (active-low switch)
// q        : 4-bit counter output (active-low LEDs; q[0] is right-most)

input goLeft;
input goRight;
input stop;
input clk;
output [3:0] q;
reg [3:0] q;

reg [0:0] dir;
reg run;

always @(posedge clk) begin: JC2_LOGIC
    if ((goRight == 0)) begin
        dir <= 1'b0;
        run <= 1'b1;
    end
    else if ((goLeft == 0)) begin
        dir <= 1'b1;
        run <= 1'b1;
    end
    if ((stop == 0)) begin
        run <= 1'b0;
    end
    if (run) begin
        if ((dir == 1'b1)) begin
            q[4-1:1] <= q[3-1:0];
            q[0] <= (!q[3]);
        end
        else begin
            q[3-1:0] <= q[4-1:1];
            q[3] <= (!q[0]);
        end
    end
end

endmodule
```

![Untitled](Activate%20Built-in%20LED/Untitled%2017.png)

1. Open diagram > drag and drop jc2 > click q[3:0] > CTRL + K, set port name to leds > OK

![Untitled](Activate%20Built-in%20LED/Untitled%2018.png)

![Untitled](Activate%20Built-in%20LED/Untitled%2019.png)

![Untitled](Activate%20Built-in%20LED/Untitled%2020.png)

1. Double Left click on “processing_system7_0” > MIO Configuration > I/O Periphereals > GPIO > check EMO GPIO > OK

![Untitled](Activate%20Built-in%20LED/Untitled%2021.png)

![Untitled](Activate%20Built-in%20LED/Untitled%2022.png)

1. Sources > Right Click > Add sources > Next > Create File > FIle Type VHDL > file name “clock_divider” > Ok > Finish > Ok > Save

![Untitled](Activate%20Built-in%20LED/Untitled%2023.png)

![Untitled](Activate%20Built-in%20LED/Untitled%2024.png)

![Untitled](Activate%20Built-in%20LED/Untitled%2025.png)

1. Open Clock divider program > replace with code below

Copy this clock divider program:

[https://allaboutfpga.com/vhdl-code-for-clock-divider/](https://allaboutfpga.com/vhdl-code-for-clock-divider/)

```
library IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.numeric_std.ALL;

entity Clock_Divider is
port ( clk,reset: in std_logic;
clock_out: out std_logic);
end Clock_Divider;

architecture bhv of Clock_Divider is

signal count: integer:=1;
signal tmp : std_logic := '0';

begin

process(clk,reset)
begin
if(reset='0') then
count<=1;
tmp<='0';
elsif(clk'event and clk='1') then
count <=count+1;
if (count = 25000) then
tmp <= NOT tmp;
count <= 1;
end if;
end if;
clock_out <= tmp;
end process;

end bhv;
```

![Untitled](Activate%20Built-in%20LED/Untitled%2026.png)

1. Diagram > Drag and drop “Clock_Divider” on diagram > Ok

Connect wire as shown below

- FCLK_CLK0 —> clk on “Clock_Divider”
- FCLK_RESET0_N —> reset on “Clock_Divider”
- clk_out on “Clock_Divider” —> clk on jc2

![Untitled](Activate%20Built-in%20LED/Untitled%2027.png)

1. Click “+” on GPIO_0 > Add “Slice”

![Untitled](Activate%20Built-in%20LED/Untitled%2028.png)

![Untitled](Activate%20Built-in%20LED/Untitled%2029.png)

1. Connect GPIO_O to Din xlslice_0  > Rename xlslice_0 to left > copy paste the slice 2 times > rename theme to right and stop 

Connect wire as show below:

- Connect Dout on “left” —> goLeft on “jc2_1”
- Connect Dout on “right” —> goRight on “jc2_1”
- Connect Douton “stop” —> stop on “jc2_1”

![Untitled](Activate%20Built-in%20LED/Untitled%2030.png)

1. Double click on slice “left” and change Din width = 64, Din From = 0, Din Down = 0 and Dout width = 1

![Untitled](Activate%20Built-in%20LED/Untitled%2031.png)

1. Double click on slice “right” and change Din width = 64, Din From = 1, Din Down = 1 and Dout width = 1

![Untitled](Activate%20Built-in%20LED/Untitled%2032.png)

Double click on slice “stop” and change Din width = 64, Din From = 2, Din Down = 2 and Dout width = 1

![Untitled](Activate%20Built-in%20LED/Untitled%2033.png)

1. Now remove AXI Port > Double Click on ZYNQ7 Processing System > choose PS-PL Configuration > AXI NOn Secure Enablement > GP Master AXI Interface > Uncheck the M AXI GP0 interface > Ok 

![Untitled](Activate%20Built-in%20LED/Untitled%2034.png)

1. Press ‘F6’ of right click and choose ‘validate design’, and will show as image below

![Untitled](Activate%20Built-in%20LED/Untitled%2035.png)

![Untitled](Activate%20Built-in%20LED/Untitled%2036.png)

1. Right click on your file name on sources > Create HDL Wrapper > Ok 

![Untitled](Activate%20Built-in%20LED/Untitled%2037.png)

![Untitled](Activate%20Built-in%20LED/Untitled%2038.png)

1. Click Run Synthesis’ > Ok

![Untitled](Activate%20Built-in%20LED/Untitled%2039.png)

1. When synthesis done, choose open synthesis design and click ok

![Untitled](Activate%20Built-in%20LED/Untitled%2040.png)

![Untitled](Activate%20Built-in%20LED/Untitled%2041.png)

1. From list below, we can know that led pins are on: R14, P14, N16, M14

```
## Switches
set_property -dict {PACKAGE_PIN M20 IOSTANDARD LVCMOS33} [get_ports {sws_2bits_tri_i[0]}]
set_property -dict {PACKAGE_PIN M19 IOSTANDARD LVCMOS33} [get_ports {sws_2bits_tri_i[1]}]

## Audio
set_property -dict {PACKAGE_PIN U9 IOSTANDARD LVCMOS33} [get_ports IIC_1_scl_io]
set_property PULLUP true [get_ports IIC_1_scl_io];
set_property -dict {PACKAGE_PIN T9 IOSTANDARD LVCMOS33} [get_ports IIC_1_sda_io]
set_property PULLUP true [get_ports IIC_1_sda_io];
set_property -dict { PACKAGE_PIN U5   IOSTANDARD LVCMOS33 } [get_ports audio_clk_10MHz];
set_property -dict { PACKAGE_PIN R18   IOSTANDARD LVCMOS33 } [get_ports bclk];
set_property -dict { PACKAGE_PIN T17   IOSTANDARD LVCMOS33 } [get_ports lrclk];
set_property -dict { PACKAGE_PIN G18   IOSTANDARD LVCMOS33 } [get_ports sdata_o];
set_property -dict { PACKAGE_PIN F17   IOSTANDARD LVCMOS33 } [get_ports sdata_i];
set_property -dict { PACKAGE_PIN M17   IOSTANDARD LVCMOS33 } [get_ports {codec_addr[0]}]
set_property -dict { PACKAGE_PIN M18   IOSTANDARD LVCMOS33 } [get_ports {codec_addr[1]}]

## Buttons
set_property -dict {PACKAGE_PIN D19 IOSTANDARD LVCMOS33} [get_ports {btns_4bits_tri_i[0]}]
set_property -dict {PACKAGE_PIN D20 IOSTANDARD LVCMOS33} [get_ports {btns_4bits_tri_i[1]}]
set_property -dict {PACKAGE_PIN L20 IOSTANDARD LVCMOS33} [get_ports {btns_4bits_tri_i[2]}]
set_property -dict {PACKAGE_PIN L19 IOSTANDARD LVCMOS33} [get_ports {btns_4bits_tri_i[3]}]

## LEDs
set_property -dict {PACKAGE_PIN R14 IOSTANDARD LVCMOS33} [get_ports {leds_4bits_tri_o[0]}]
set_property -dict {PACKAGE_PIN P14 IOSTANDARD LVCMOS33} [get_ports {leds_4bits_tri_o[1]}]
set_property -dict {PACKAGE_PIN N16 IOSTANDARD LVCMOS33} [get_ports {leds_4bits_tri_o[2]}]
set_property -dict {PACKAGE_PIN M14 IOSTANDARD LVCMOS33} [get_ports {leds_4bits_tri_o[3]}]

## RGBLEDs
set_property -dict { PACKAGE_PIN L15   IOSTANDARD LVCMOS33 } [get_ports { rgbleds_6bits_tri_o[0] }];
set_property -dict { PACKAGE_PIN G17   IOSTANDARD LVCMOS33 } [get_ports { rgbleds_6bits_tri_o[1] }];
set_property -dict { PACKAGE_PIN N15   IOSTANDARD LVCMOS33 } [get_ports { rgbleds_6bits_tri_o[2] }];
set_property -dict { PACKAGE_PIN G14   IOSTANDARD LVCMOS33 } [get_ports { rgbleds_6bits_tri_o[3] }];
set_property -dict { PACKAGE_PIN L14   IOSTANDARD LVCMOS33 } [get_ports { rgbleds_6bits_tri_o[4] }];
set_property -dict { PACKAGE_PIN M15   IOSTANDARD LVCMOS33 } [get_ports { rgbleds_6bits_tri_o[5] }];

## Pmoda
## RPi GPIO 7-0 are shared with pmoda_rpi_gpio_tri_io[7:0]
set_property -dict {PACKAGE_PIN Y19 IOSTANDARD LVCMOS33} [get_ports {pmoda_rpi_gpio_tri_io[1]}]
set_property -dict {PACKAGE_PIN Y18 IOSTANDARD LVCMOS33} [get_ports {pmoda_rpi_gpio_tri_io[0]}]
set_property -dict {PACKAGE_PIN Y17 IOSTANDARD LVCMOS33} [get_ports {pmoda_rpi_gpio_tri_io[3]}]
set_property -dict {PACKAGE_PIN Y16 IOSTANDARD LVCMOS33} [get_ports {pmoda_rpi_gpio_tri_io[2]}]
set_property -dict {PACKAGE_PIN U19 IOSTANDARD LVCMOS33} [get_ports {pmoda_rpi_gpio_tri_io[5]}]
set_property -dict {PACKAGE_PIN U18 IOSTANDARD LVCMOS33} [get_ports {pmoda_rpi_gpio_tri_io[4]}]
set_property -dict {PACKAGE_PIN W19 IOSTANDARD LVCMOS33} [get_ports {pmoda_rpi_gpio_tri_io[7]}]
set_property -dict {PACKAGE_PIN W18 IOSTANDARD LVCMOS33} [get_ports {pmoda_rpi_gpio_tri_io[6]}]
set_property PULLUP true [get_ports {pmoda_rpi_gpio_tri_io[2]}]
set_property PULLUP true [get_ports {pmoda_rpi_gpio_tri_io[3]}]
set_property PULLUP true [get_ports {pmoda_rpi_gpio_tri_io[6]}]
set_property PULLUP true [get_ports {pmoda_rpi_gpio_tri_io[7]}]

## Pmodb
set_property -dict {PACKAGE_PIN Y14 IOSTANDARD LVCMOS33} [get_ports {pmodb_gpio_tri_io[1]}]
set_property -dict {PACKAGE_PIN W14 IOSTANDARD LVCMOS33} [get_ports {pmodb_gpio_tri_io[0]}]
set_property -dict {PACKAGE_PIN T10 IOSTANDARD LVCMOS33} [get_ports {pmodb_gpio_tri_io[3]}]
set_property -dict {PACKAGE_PIN T11 IOSTANDARD LVCMOS33} [get_ports {pmodb_gpio_tri_io[2]}]
set_property -dict {PACKAGE_PIN W16 IOSTANDARD LVCMOS33} [get_ports {pmodb_gpio_tri_io[5]}]
set_property -dict {PACKAGE_PIN V16 IOSTANDARD LVCMOS33} [get_ports {pmodb_gpio_tri_io[4]}]
set_property -dict {PACKAGE_PIN W13 IOSTANDARD LVCMOS33} [get_ports {pmodb_gpio_tri_io[7]}]
set_property -dict {PACKAGE_PIN V12 IOSTANDARD LVCMOS33} [get_ports {pmodb_gpio_tri_io[6]}]
set_property PULLUP true [get_ports {pmodb_gpio_tri_io[2]}]
set_property PULLUP true [get_ports {pmodb_gpio_tri_io[3]}]
set_property PULLUP true [get_ports {pmodb_gpio_tri_io[6]}]
set_property PULLUP true [get_ports {pmodb_gpio_tri_io[7]}]

## Arduino GPIO
set_property -dict {PACKAGE_PIN T14 IOSTANDARD LVCMOS33} [get_ports {arduino_gpio_tri_io[0]}]
set_property -dict {PACKAGE_PIN U12 IOSTANDARD LVCMOS33} [get_ports {arduino_gpio_tri_io[1]}]
set_property -dict {PACKAGE_PIN U13 IOSTANDARD LVCMOS33} [get_ports {arduino_gpio_tri_io[2]}]
set_property -dict {PACKAGE_PIN V13 IOSTANDARD LVCMOS33} [get_ports {arduino_gpio_tri_io[3]}]
set_property -dict {PACKAGE_PIN V15 IOSTANDARD LVCMOS33} [get_ports {arduino_gpio_tri_io[4]}]
set_property -dict {PACKAGE_PIN T15 IOSTANDARD LVCMOS33} [get_ports {arduino_gpio_tri_io[5]}]
set_property -dict {PACKAGE_PIN R16 IOSTANDARD LVCMOS33} [get_ports {arduino_gpio_tri_io[6]}]
set_property -dict {PACKAGE_PIN U17 IOSTANDARD LVCMOS33} [get_ports {arduino_gpio_tri_io[7]}]
set_property -dict {PACKAGE_PIN V17 IOSTANDARD LVCMOS33} [get_ports {arduino_gpio_tri_io[8]}]
set_property -dict {PACKAGE_PIN V18 IOSTANDARD LVCMOS33} [get_ports {arduino_gpio_tri_io[9]}]
set_property -dict {PACKAGE_PIN T16 IOSTANDARD LVCMOS33} [get_ports {arduino_gpio_tri_io[10]}]
set_property -dict {PACKAGE_PIN R17 IOSTANDARD LVCMOS33} [get_ports {arduino_gpio_tri_io[11]}]
set_property -dict {PACKAGE_PIN P18 IOSTANDARD LVCMOS33} [get_ports {arduino_gpio_tri_io[12]}]
set_property -dict {PACKAGE_PIN N17 IOSTANDARD LVCMOS33} [get_ports {arduino_gpio_tri_io[13]}]
set_property -dict {PACKAGE_PIN Y11 IOSTANDARD LVCMOS33} [get_ports {arduino_gpio_tri_io[14]}]
set_property -dict {PACKAGE_PIN Y12 IOSTANDARD LVCMOS33} [get_ports {arduino_gpio_tri_io[15]}]
set_property -dict {PACKAGE_PIN W11 IOSTANDARD LVCMOS33} [get_ports {arduino_gpio_tri_io[16]}]
set_property -dict {PACKAGE_PIN V11 IOSTANDARD LVCMOS33} [get_ports {arduino_gpio_tri_io[17]}]
set_property -dict {PACKAGE_PIN T5  IOSTANDARD LVCMOS33} [get_ports {arduino_gpio_tri_io[18]}]
set_property -dict {PACKAGE_PIN U10 IOSTANDARD LVCMOS33} [get_ports {arduino_gpio_tri_io[19]}]

## Arduino direct I2C
set_property -dict {PACKAGE_PIN P15 IOSTANDARD LVCMOS33} [get_ports arduino_direct_iic_scl_io]
set_property -dict {PACKAGE_PIN P16 IOSTANDARD LVCMOS33} [get_ports arduino_direct_iic_sda_io]
set_property PULLUP true [get_ports arduino_direct_iic_scl_io]
set_property PULLUP true [get_ports arduino_direct_iic_sda_io]

## Arduino direct SPI
set_property -dict {PACKAGE_PIN W15 IOSTANDARD LVCMOS33} [get_ports arduino_direct_spi_io1_io]
set_property -dict {PACKAGE_PIN T12 IOSTANDARD LVCMOS33} [get_ports arduino_direct_spi_io0_io]
set_property -dict {PACKAGE_PIN H15 IOSTANDARD LVCMOS33} [get_ports arduino_direct_spi_sck_io]
set_property -dict {PACKAGE_PIN F16 IOSTANDARD LVCMOS33} [get_ports arduino_direct_spi_ss_io]

## Arduino analog channels
set_property -dict {PACKAGE_PIN D18 IOSTANDARD LVCMOS33} [get_ports Vaux1_v_n]
set_property -dict {PACKAGE_PIN E17 IOSTANDARD LVCMOS33} [get_ports Vaux1_v_p]
set_property -dict {PACKAGE_PIN E19 IOSTANDARD LVCMOS33} [get_ports Vaux9_v_n]
set_property -dict {PACKAGE_PIN E18 IOSTANDARD LVCMOS33} [get_ports Vaux9_v_p]
set_property -dict {PACKAGE_PIN J14 IOSTANDARD LVCMOS33} [get_ports Vaux6_v_n]
set_property -dict {PACKAGE_PIN K14 IOSTANDARD LVCMOS33} [get_ports Vaux6_v_p]
set_property -dict {PACKAGE_PIN J16 IOSTANDARD LVCMOS33} [get_ports Vaux15_v_n]
set_property -dict {PACKAGE_PIN K16 IOSTANDARD LVCMOS33} [get_ports Vaux15_v_p]
set_property -dict {PACKAGE_PIN H20 IOSTANDARD LVCMOS33} [get_ports Vaux5_v_n]
set_property -dict {PACKAGE_PIN J20 IOSTANDARD LVCMOS33} [get_ports Vaux5_v_p]
set_property -dict {PACKAGE_PIN G20 IOSTANDARD LVCMOS33} [get_ports Vaux13_v_n]
set_property -dict {PACKAGE_PIN G19 IOSTANDARD LVCMOS33} [get_ports Vaux13_v_p]

## HDMI signals
create_clock -period 8.334 -waveform {0.000 4.167} [get_ports hdmi_in_clk_p]

## HDMI RX
set_property -dict {PACKAGE_PIN P19 IOSTANDARD TMDS_33} [get_ports hdmi_in_clk_n]
set_property -dict {PACKAGE_PIN N18 IOSTANDARD TMDS_33} [get_ports hdmi_in_clk_p]
set_property -dict {PACKAGE_PIN W20 IOSTANDARD TMDS_33} [get_ports {hdmi_in_data_n[0]}]
set_property -dict {PACKAGE_PIN V20 IOSTANDARD TMDS_33} [get_ports {hdmi_in_data_p[0]}]
set_property -dict {PACKAGE_PIN U20 IOSTANDARD TMDS_33} [get_ports {hdmi_in_data_n[1]}]
set_property -dict {PACKAGE_PIN T20 IOSTANDARD TMDS_33} [get_ports {hdmi_in_data_p[1]}]
set_property -dict {PACKAGE_PIN P20 IOSTANDARD TMDS_33} [get_ports {hdmi_in_data_n[2]}]
set_property -dict {PACKAGE_PIN N20 IOSTANDARD TMDS_33} [get_ports {hdmi_in_data_p[2]}]
set_property -dict {PACKAGE_PIN T19 IOSTANDARD LVCMOS33} [get_ports {hdmi_in_hpd[0]}]
set_property -dict {PACKAGE_PIN U14 IOSTANDARD LVCMOS33} [get_ports hdmi_in_ddc_scl_io]
set_property -dict {PACKAGE_PIN U15 IOSTANDARD LVCMOS33} [get_ports hdmi_in_ddc_sda_io]

## HDMI TX
set_property -dict {PACKAGE_PIN L17 IOSTANDARD TMDS_33} [get_ports hdmi_out_clk_n]
set_property -dict {PACKAGE_PIN L16 IOSTANDARD TMDS_33} [get_ports hdmi_out_clk_p]
set_property -dict {PACKAGE_PIN K18 IOSTANDARD TMDS_33} [get_ports {hdmi_out_data_n[0]}]
set_property -dict {PACKAGE_PIN K17 IOSTANDARD TMDS_33} [get_ports {hdmi_out_data_p[0]}]
set_property -dict {PACKAGE_PIN J19 IOSTANDARD TMDS_33} [get_ports {hdmi_out_data_n[1]}]
set_property -dict {PACKAGE_PIN K19 IOSTANDARD TMDS_33} [get_ports {hdmi_out_data_p[1]}]
set_property -dict {PACKAGE_PIN H18 IOSTANDARD TMDS_33} [get_ports {hdmi_out_data_n[2]}]
set_property -dict {PACKAGE_PIN J18 IOSTANDARD TMDS_33} [get_ports {hdmi_out_data_p[2]}]
set_property -dict {PACKAGE_PIN R19 IOSTANDARD LVCMOS33} [get_ports {hdmi_out_hpd[0]}]

## Raspberry PI
##  RPI_IDE Pin#   |   RP Connector  | Schematic Name | Dual Functionality
##        1        |      3v3        |     NA
##        3        |      GPIO2      |     JA4_P      |    I2C1_SDA
##        5        |      GPIO3      |     JA4_N      |    I2C1_SCL
##        7        |      GPIO4      |     JA1_P      |    GCLK0
##        9        |      GROUND     |     NA
##        11       |      GPIO17     |     RP_IO17_R
##        13       |      GPIO27     |     RP_IO27_R
##        15       |      GPIO22     |     RP_IO22_R
##        17       |      3v3        |     NA
##        19       |      GPIO10     |     RP_IO10_R   |    SPIO0_MOSI
##        21       |      GPIO9      |     RP_IO09_R   |    SPIO0_MISO
##        23       |      GPIO11     |     RP_IO11_R   |    SPIO0_SCLK
##        25       |      GROUND     |     NA
##        27       |      GPIO0      |     JA2_P       |    I2C0_SDA ID EEPROM
##        29       |      GPIO5      |     JA1_N       |    GCLK1
##        31       |      GPIO6      |     JA3_P       |    GCLK2
##        33       |      GPIO13     |     RP_IO13_R   |    PWM1
##        35       |      GPIO19     |     RP_IO19_R   |    SPIO1_MISO
##        37       |      GPIO26     |     RP_IO26_R
##        39       |      GROUND     |     NA

##        2        |      5V         |     NA
##        4        |      5V         |     NA
##        6        |      GROUND     |     NA
##        8        |      GPIO14     |     RP_IO14_R   |    UART0_TXD
##        10       |      GPIO15     |     RP_IO15_R   |    UART0_RXD
##        12       |      GPIO18     |     RP_IO18_R   |    PCM_CLK
##        14       |      GROUND     |     NA
##        16       |      GPIO23     |     RP_IO23_R
##        18       |      GPIO24     |     RP_IO24_R
##        20       |      GROUND     |     NA
##        22       |      GPIO25     |     RP_IO25_R
##        24       |      GPIO8      |     RP_IO08_R   |    SPIO0_CE0_N
##        26       |      GPIO7      |     JA3_N       |    SPIO0_CE1_N
##        28       |      GPIO1      |     JA2_N       |    I2C0_SDC ID EEPROM
##        30       |      GROUND     |     NA
##        32       |      GPIO12     |     RP_IO12_R   |    PWM0
##        34       |      GROUND     |     NA
##        36       |      GPIO16     |     RP_IO16_R   |    SPIO1_CE2_N
##        38       |      GPIO20     |     RP_IO20_R   |    SPIO1_MOSI
##        40       |      GPIO21     |     RP_IO21_R   |    SPIO1_SCLK
## RPi GPIO 27-8 are mapped to rpi_gpio_tri_io[19:0]
set_property -dict { PACKAGE_PIN F19   IOSTANDARD LVCMOS33 } [get_ports { rpi_gpio_tri_io[0]  }];
set_property -dict { PACKAGE_PIN V10   IOSTANDARD LVCMOS33 } [get_ports { rpi_gpio_tri_io[1]  }];
set_property -dict { PACKAGE_PIN V8    IOSTANDARD LVCMOS33 } [get_ports { rpi_gpio_tri_io[2] }];
set_property -dict { PACKAGE_PIN W10   IOSTANDARD LVCMOS33 } [get_ports { rpi_gpio_tri_io[3] }];
set_property -dict { PACKAGE_PIN B20   IOSTANDARD LVCMOS33 } [get_ports { rpi_gpio_tri_io[4] }];
set_property -dict { PACKAGE_PIN W8    IOSTANDARD LVCMOS33 } [get_ports { rpi_gpio_tri_io[5] }];
set_property -dict { PACKAGE_PIN V6    IOSTANDARD LVCMOS33 } [get_ports { rpi_gpio_tri_io[6] }];
set_property -dict { PACKAGE_PIN Y6    IOSTANDARD LVCMOS33 } [get_ports { rpi_gpio_tri_io[7] }];
set_property -dict { PACKAGE_PIN B19   IOSTANDARD LVCMOS33 } [get_ports { rpi_gpio_tri_io[8] }];
set_property -dict { PACKAGE_PIN U7    IOSTANDARD LVCMOS33 } [get_ports { rpi_gpio_tri_io[9] }];
set_property -dict { PACKAGE_PIN C20   IOSTANDARD LVCMOS33 } [get_ports { rpi_gpio_tri_io[10] }];
set_property -dict { PACKAGE_PIN Y8    IOSTANDARD LVCMOS33 } [get_ports { rpi_gpio_tri_io[11] }];
set_property -dict { PACKAGE_PIN A20   IOSTANDARD LVCMOS33 } [get_ports { rpi_gpio_tri_io[12] }];
set_property -dict { PACKAGE_PIN Y9    IOSTANDARD LVCMOS33 } [get_ports { rpi_gpio_tri_io[13] }];
set_property -dict { PACKAGE_PIN U8    IOSTANDARD LVCMOS33 } [get_ports { rpi_gpio_tri_io[14] }];
set_property -dict { PACKAGE_PIN W6    IOSTANDARD LVCMOS33 } [get_ports { rpi_gpio_tri_io[15] }];
set_property -dict { PACKAGE_PIN Y7    IOSTANDARD LVCMOS33 } [get_ports { rpi_gpio_tri_io[16] }];
set_property -dict { PACKAGE_PIN F20   IOSTANDARD LVCMOS33 } [get_ports { rpi_gpio_tri_io[17] }];
set_property -dict { PACKAGE_PIN W9    IOSTANDARD LVCMOS33 } [get_ports { rpi_gpio_tri_io[18] }];
set_property -dict { PACKAGE_PIN V7    IOSTANDARD LVCMOS33 } [get_ports { rpi_gpio_tri_io[19] }];
```

 24. Click “Window” > I/O Ports

![Untitled](Activate%20Built-in%20LED/Untitled%2042.png)

1. Set LED Ports as shown below

![Untitled](Activate%20Built-in%20LED/Untitled%2043.png)

![Untitled](Activate%20Built-in%20LED/Untitled%2044.png)

1. Save constrains > set name to pin_constrains.xdc

![Untitled](Activate%20Built-in%20LED/Untitled%2045.png)

1. Set I/O Std to LVCM0S33\

![Untitled](Activate%20Built-in%20LED/Untitled%2046.png)

1. Save constrain (CTRL + S)
2. Click generate bitstream

![Untitled](Activate%20Built-in%20LED/Untitled%2047.png)

1. Open “..2_CustomOverlay_GPIO\2_CustomOverlay_GPIO.runs\impl_1” copy bit file

![Untitled](Activate%20Built-in%20LED/Untitled%2048.png)

1. Open Jupyter Notebook and create folder
2. Upload the .bit file to the created folder
3. Rename the bit file same as the Folder Name for example GPIO LED
4. Back to Vivado and navigate to Diagram section. Then clik File > Export > Export Block Design

![Untitled](Activate%20Built-in%20LED/Untitled%2049.png)

1. Navigate to “..2_CustomOverlay_GPIO” and select the .tcl file

![Untitled](Activate%20Built-in%20LED/Untitled%2050.png)

1. Upload .tcl file to jupyter folder and rename same as the folder name
2. Upload .hwh file to jupyter folder where you can find in “..C:\Users\TELEMATIKA\3_ReadingSwitch\3_ReadingSwitch.gen\sources_1\bd\read_switch\hw_handoff”
3. Create python file

![Untitled](Activate%20Built-in%20LED/Untitled%2051.png)

1. Replace the python file with this code

![Untitled](Activate%20Built-in%20LED/Untitled%2052.png)
