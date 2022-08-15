# Praktikum EMSYS (Embedded System) - Teknik Elektro Universitas Kristen Petra
# Pynq Z2 - Fundamental Literature

![Untitled](Pynq%20Z2%20-%20Fundamental%20Literature/Untitled.png)

## PYNQ-Z2 Image Download
https://github.com/Xilinx/PYNQ/releases

## PYNQ-Z2 Features

![Untitled](Pynq%20Z2%20-%20Fundamental%20Literature/Untitled%201.png)

## Powering Board

![Untitled](Pynq%20Z2%20-%20Fundamental%20Literature/Untitled%202.png)

The PYNQ-Z2 can be powered from the Micro-USB port (J8), an external power supply (**RECOMENDED**), or a battery. The power source is selected by setting jumper J9 (near SW1) to USB or REG (External power regulator/Battery). Use an external power regulator (coax, centre-positive 2.1mm internal-diameter plug) that can be connected to the power jack (DC1). The board supports 7VDC to 15VDC (12V recommended).

## Boot Mode

![Untitled](Pynq%20Z2%20-%20Fundamental%20Literature/Untitled%203.png)

The PYNQ-Z2 supports MicroSD, Quad SPI Flash, and JTAG boot modes. The boot mode is selected using the Mode jumper (JP1). TO select the boot mode, move the jumper to the appropriate position as indicated by the label on the board.

## LEDs, Button, Switches

The PYNQ-Z2 board includes 2 tri-color LEDs, 2 dipswitches, 4 push buttons, and 4 individual LEDs connected to the PL.

![Untitled](Pynq%20Z2%20-%20Fundamental%20Literature/Untitled%204.png)

### Push-buttons

The four push buttons generate a logic high on the corresponding PL pin when pressed.

![Untitled](Pynq%20Z2%20-%20Fundamental%20Literature/Untitled%205.png)

### Switches

When the switches are closed (in the “up” position) they generate a logic high on the corresponding PL pins.

![Untitled](Pynq%20Z2%20-%20Fundamental%20Literature/Untitled%206.png)

LEDs

The four individual LEDs are anode-connected tot he Zynq PL via 330-ohms resistors. Applying logic to the appropriate pins will turn on the LEDs.

![Untitled](Pynq%20Z2%20-%20Fundamental%20Literature/Untitled%207.png)

The board also includes LEDs indicating board power, PL programming “done”, and status for USB and Ethernet

## Ethernet Port

The PYNQ-Z2 has a Realtek RTL8211E-VL PHY supporting 10/100/1000 Ethernet. The PHY is connected to the Zynq RGMII controller. The auxiliary interrupt (INTB) and reset (PHYRSTB) signals connect to MIO pins MIO10 and MIO9, respectively. One of the Zynq PS Ethernet controllers can be connected to the appropriate MIO pins to control the Ethernet port.

![Untitled](Pynq%20Z2%20-%20Fundamental%20Literature/Untitled%208.png)

## Pmod

![Untitled](Pynq%20Z2%20-%20Fundamental%20Literature/Untitled%209.png)

Pynq has two Pmod interfaces. Each 12-pin Pmod port provides two 3.3V VCC signals (pins 6 and 12), two Ground signals (pins 5 and 11), and eight logic signals. The VCC and Ground pins can deliver up to 1A of current.

![Untitled](Pynq%20Z2%20-%20Fundamental%20Literature/Untitled%2010.png)

Pmod A pins are shared with the Raspberry Pi header

![Untitled](Pynq%20Z2%20-%20Fundamental%20Literature/Untitled%2011.png)

![Untitled](Pynq%20Z2%20-%20Fundamental%20Literature/Untitled%2012.png)

## Jupyter Notebook

After the board powered up, access the jupyter notebook from your browser:

- [http://192.168.2.99](http://192.168.2.99/)

***notes: the board and your laptop must be in the same network**

![Untitled](Pynq%20Z2%20-%20Fundamental%20Literature/Untitled%2013.png)

# Pynq Overlay

/[https://notebook.community/Xilinx/PYNQ/docs/source/pynq_overlays/loading_an_overlay](https://notebook.community/Xilinx/PYNQ/docs/source/pynq_overlays/loading_an_overlay)

The Xilinx® Zynq® All Programmable device is an SOC based on a dual-core ARM® Cortex®A9 processor (referred to as the *Processing System* or **PS**), integrated with FPGA fabric (referred to as *Programmable Logic* or **PL**). The *PS* subsystem includes a number of dedicated peripherals (memory controllers, USB, Uart, IIC, SPI etc) and can be extended with additional hardware IP in a *PL* Overlay.

![Untitled](Pynq%20Z2%20-%20Fundamental%20Literature/Untitled%2014.png)

Overlays, or hardware libraries, are programmable/configurable FPGA designs that extend the user application from the Processing System of the Zynq into the Programmable Logic. Overlays can be used to accelerate a software application or to customize the hardware platform for a particular application.

PYNQ provides a Python interface to allow overlays in the *PL* to be controlled from Python running in the *PS*. FPGA design is a specialized task that requires hardware engineering knowledge and expertise. PYNQ overlays are created by hardware designers and wrapped with this PYNQ Python API. Software developers can then use the Python interface to program and control specialized hardware overlays without needing to design an overlay themselves. This is analogous to software libraries created by expert developers which are then used by many other software developers working at the application level.

## Loading an Overlay

By default, an overlay (bitstream) called *base* is downloaded into the PL at boot time. The *base* overlay can be considered a reference design for a board. New overlays can be installed or copied to the board and can be loaded into the PL as the system is running.

An overlay usually includes:

- A bitstream to configure the FPGA fabric
- A Vivado design HWH file to determine the available IP
- Python API that exposes the IPs as attributes

The PYNQ `Overlay` class can be used to load an overlay. An overlay is instantiated by specifying the name of the bitstream file. Instantiating the Overlay also downloads the bitstream by default and parses the HWH file.

```python
from pynq import Overlay
overlay = Overlay("base.bit")
```

## Base Overlay

The purpose of the base overlay design is to allow PYNQ to use peripherals on a board out-of-the-box. The design includes hardware IP to control peripherals on the target board and connects these IP blocks to the Zynq PS. If a base overlay is available for a board, peripherals can be used from the Python environment immediately after the system boots.

Board peripherals typically include GPIO devices (LEDs, Switches, Buttons), Video, Audio, and other custom interfaces.

As the base overlay includes IP for the peripherals on a board, it can also be used as a reference design for creating new customized overlays.

In the case of general-purpose interfaces, for example, Pmod or Arduino headers, the base overlay may include a PYNQ MicroBlaze. A PYNQ MicroBlaze allows control of devices with different interfaces and protocols on the same port without requiring a change to the programmable logic design.

![PYNQ-Z2 diagram](Pynq%20Z2%20-%20Fundamental%20Literature/Untitled%2015.png)

PYNQ-Z2 diagram

The base overlay on PYNQ-Z2 includes the following hardware:

- HDMI (Input and Output)
- Audio codec
- User LEDs, Switches, Pushbuttons2x
- Pmod PYNQ MicroBlaze
- Arduino PYNQ MicroBlaze
- RPi (Raspberry Pi) PYNQ MicroBlaze
- 4x Trace Analyzer (PMODA, PMODB, ARDUINO, RASPBERRYPI)

## Overlay Design Methodology

PYNQ *Overlays* are analogous to software libraries. A programmer can download overlays into the Xilinx Programmable Logic at runtime to provide the functionality required by the software application.

An *overlay* is a class of Programmable Logic design. Programmable Logic designs are usually highly optimized for a specific task. Overlays however are designed to be configurable, and reusable for a broad set of applications. A PYNQ overlay will have a Python interface, allowing a software programmer to use it like any other Python package.

There are a number of components required in the process of creating an overlay:

- Board or platform settings
- Interfaces between the host processor and programmable logic
- MicroBlaze Soft Processors
- Python/C Integration
- Python AsyncIO
- Python Overlay API
- Python Packaging

An overlay consists of two main parts; the PL design (bitstream) and the project HWH file. Overlay design is a specialized task for hardware engineers. This section assumes the reader has some experience with digital design, building Zynq systems, and the Vivado design tools.

## PL Design

The Xilinx® Vivado software is used to create a Zynq design. A *bitstream* or *binary* file (.bit file) will be generated that can be used to program the Zynq PL.

## Overlay HWH File

The HWH (hardware handoff) file is automatically generated from the Vivado IP Integrator block design and it is used by PYNQ to automatically identify the Zynq system configuration, IP including versions, interrupts, resets, and other control signals. Based on this information, some parts of the system configuration can be automatically modified from PYNQ, drivers can be automatically assigned, features can be enabled or disabled, and signals can be connected to corresponding Python methods.

The HWH file is automatically generated by Vivado when generating the bitstream. The HWH file must be provided with the bitstream file as part of an overlay. The PYNQ PL class will automatically parse the HWH file.

The directory of HWH file is in the:

```markdown
sources_1/bd/<bd_name>/hw_handoff/<bd_name>.hwh
```

The HWH filename should match the .bit filename. For example, my_overlay.bit
 and my_overlay.hwh.

## Zynq PS

A Vivado project for a Zynq design consists of two parts; the PL design, and the PS configuration settings.

It is recommended to place the Zynq PS in the top level of your IP Integrator. The Zynq PS is also supported within a hierarchy, but this is discouraged.

The PYNQ image which is used to boot the board configures the Zynq PS at boot time. This will fix most of the PS configuration, including setup of DRAM, and enabling of the Zynq PS peripherals, including SD card, Ethernet, USB and UART which are used by PYNQ.

The PS configuration also includes settings for system clocks, including the clocks used in the PL. The PL clocks can be programmed at runtime to match the requirements of the overlay. This is managed automatically by the PYNQ Overlay class.

During the process of downloading a new overlay, the clock configuration will be parsed from the overlay’s HWH file. The new clock settings for the overlay will be applied automatically before the overlay is downloaded.

## PWM

Digital signals have two positions: on or off, interpreted in shorthand as 1 or 0. Analog signals, on the other hand, can be on, off, half-way, two-thirds the way to on, and an infinite number of positions between 0 and 1 either approaching 1 or descending down to zero. The two are handled very differently in electronics, but very often must work together (that’s when we call it “mixed signal electronics.”) Sometimes we have to take an analog (real world) input signal (e.g., temperature) into a microcontroller (which only understands digital). Often engineers will translate that analog input into digital input for the microcontroller (MCU) by using an analog-to-digital converter. But what about outputs?

PWM is a way to control analog devices with a digital output. Another way to put it is that you can output a modulating signal from a digital device such as an MCU to drive an analog device. It’s one of the primary means by which MCUs drive analog devices like variable-speed motors, dimmable lights, actuators, and speakers. PWM is not true analog output, however. PWM “fakes” an analog-like result by applying power in pulses, or short bursts of regulated voltage.

![Untitled](Pynq%20Z2%20-%20Fundamental%20Literature/fig_1_pwm.jpg)

An example would be to apply full voltage to a motor or lamp for fractions of a second or pulse the voltage to the motor at intervals that made the motor or lamp do what you wanted it to do. In reality, the voltage is being applied and then removed many times in an interval, but what you experience is an analog-like response. If you have ever jogged a box fan by applying power intermittently, you will experience a PWM response. The fan and its motor do not stop instantly due to inertia, and so by the time you re-apply power it has only slowed a bit.

Therefore, you do not experience an abrupt stop in power if a motor is driven by PWM. The length of time that a pulse is in a given state (high/low) is the “width” of a pulse wave.

![Untitled](Pynq%20Z2%20-%20Fundamental%20Literature/fig_2_pwm.jpg)

A device that is driven by PWM ends up behaving like the average of the pulses. The average voltage level can be a steady voltage or a moving target (dynamic/changing over time). To simplify the example, let’s assume that your PWM-driven fan has a high-level voltage of 24 volts. If the pulse is driven high 50% of the time, we call this a 50% duty cycle. The term duty cycle is used elsewhere in electronics, but in every case duty cycle is a comparison of “on” versus “off.”

Going back to our fan motor example, if we know that the high voltage is 24, the low is 0v, and the duty cycle is 50%, then we can determine the average voltage by multiplying the duty cycle by the pulse’s high level. If you want the motor to go faster, you can drive the PWM output to a higher duty cycle. The higher the frequency of high pulses, the higher the average voltage and the faster the fan motor will spin. IF you were making your own PWM output by plugging the fan in and out of a socket at equal intervals of 1 second in the socket, 1 second out, then you are acting like a digital output that’s driving the fan at a steady average of 12V.

The analogy comes in when you increase the frequency of plugging in and out of the socket so that you only have it in the socket ½ a second and out of the socket an equal ½ second. At this point, your duty cycle is still 50%, but you have increased the number of cycles per second to two. In electronics, we would identify frequency as cycles per second, or Hertz (Hz.) You have increased the speed of the fan. That ½ second is the width of the pulse you are making.

You might have gathered by now that PWM, duty cycle, and frequency are interrelated. We use duty cycle and frequency to describe the PWM, and we often talk about frequency in reference to speed. For example, a variable frequency drive motor produces a response like analog device in the real world. The separate pulses that the VFD motor gets are not discernable to us; as far as we can see, the pulses are so fast (usually somewhere in the milliseconds) that by real world standards it just seems like a motor ramping up.

If you take the duty cycle and multiply it by the high voltage level (which is a digital “on” or “1” state as far as the MCU is concerned), you will get the average voltage level that the motor is seeing at that moment.

Duty Cycle x High Voltage Level = Average Voltage

Now put the word “instantaneous” in there and you get the idea that things are dynamically changing…which looks more analog (see Figure 2):

Instantaneous Duty Cycle x High Voltage Level = Instantaneous Average Voltage

The duty cycle can change to affect the average voltage that the motor experiences. The frequency of the cycles can increase. The pulse can even be increased in length. These can all happen together, too, but in general, it’s easier to think of as either duty cycle increasing or frequency increases to increase the speed of the motor. (Pulse width is directly related to duty cycle, so if you decide to increase the width of a pulse, you are just altering the duty cycle.)

The only thing that hasn’t changed in all of this is the high voltage level, because “on” is always the same for the digital output; merely flicking the output on and off at varying speeds and for varying lengths of time is how you get pulse width modulation to fake an analog output. MCUs are digital. An example of something that can create a true analog output would be a transducer (something that directly translates physical phenomenon to an analog signal). But transducers are another analog discussion.
