# NanoDLP Shield Firmware

This is a firmare for custom RaspberryPi shield to drive LCD 3d printer based on NanoDLP software.

[NanoDLP](https://www.nanodlp.com/) is a web-based control for LCD or DLP 3D printer. It allows user to upload
a model to print, slice it, and then drive 3D printer hardware (LCD, motors, shutters) in order to print the model.

Unfortunately NanoDLP software is closed source and therefore has limited extension capabilities. Out of the box
it can drive Z-axis motor, display slices on LCD or DLP projector, can drive 2x16 LCD screen. But that is it. If
you want more you have to write external scripts. Well, this is a good option, but it can't be used for low-latency
user input (e.g. adding more hadrware buttons).

A good option is to delegate dealing with the hardware to an Arduino. User can significantly extend hadrware capabilities.
The drawback of this approach is having multiple external components (Stepper motor drivers, UV LED drivers, I2C and UART
level converters) and wires which may lead to hardware instability. Moreover multiple component require a space to mount
them in the printer.

Aim of this project is to create a small all-in one extendable RaspberryPI shield that would include:
- Stepper Motor driver
- two Z-axis end stops (incl optional pull-up or pull-down resistors)
- Powerful MOSFET to drive UV LED
- GPIO for user needs (buttons, driving external hardware components)
- 1-wire DS18B20 thermometer support
- PWM output
- Fan output with PWM support
- I2C and UART interfaces with integrated level converters (3.3V or 5V selectable)
- low power LED output
- Buzzer
- Provide various voltage levels for external components (3.3V, 5V, 12V)

This shield would take responsibility of driving all external hardware, replacing Arduino with number of external components.
Communication between the shield and NanoDLP software is implemented via pseudo-COM port, so that NanoDLP software treats is
as external Arduino.

# Hardware

Schematics and PCB can be found [here](https://easyeda.com/editor#id=1c84f9033af4487bb82d24a9e845125c|2ef221c521474696ba044a7bebf7602c).

BOM:
MFR Part:            MFR:               QTY:
BSS138	            ON Semiconductor	  4
106990005	          Seeed Studio	      1
MB 12	              Visaton	            1
ERJ-UP8F4701V	      Panasonic	          19
CRCW12060000Z0EAC  	Vishay	            6
2N7002	            ON Semiconductor	  1
UVZ1H101MPD1TD	    Nichicon	          1
90147-1108	        Molex	              2
M20-9990345	        Harwin	            8
M20-9720345	        Harwin	            1
M20-9720245	        Harwin	            2
OQ025A000000G	      Amphenol	          2
M20-9730245	        Harwin	            2
M20-9990445	        Harwin	            2
URS1H101MPD1TD	    Nichicon	          1
CRCW1206120RFKEAC	  Vishay	            4
VJ1206Y104JXAMR	    Vishay	            8
UMK316BJ105KD-T	    Taiyo Yuden	        1
FDLL4148-D87Z	      ON Semiconductor	  1
76342-320HLF	      FCI / Amphenol	    1
DMN3150L-7	        Diodes Incorporated	1
IRL2203NPBF	        Infineon	          1

Choice: Add 2x JST-XH 3-Pin and 1x JST-XH 4-Pin for Motor + E-stop connectors or substitute with bare pin headers.

# Software

Software is based on WiringPi library which provides a Arduino-like interface to drive RasPi's GPIOs. Additionally it uses
[SpeedyStepper](https://github.com/Stan-Reifel/SpeedyStepper) library with minor modifications to drive stepper motor.

Installing prerequisites
```bash
sudo apt-get install cmake g++ wiringpi
```

# Building
-Install git on the Raspberry Pi.
-Git clone the repo
-Use Nano to edit:
    -> Config.h
        -> steps/mm
        -> defined/commented hardware buttons
    -> SpeedyStepper.h
        -> Steps/rev
        -> speeds in steps/s
        -> accelerations in steps/s/s
    -> SpeedyStepper.h
        -> Steps/rev
        -> speeds in steps/s
        -> accelerations in steps/s/s

 create cmake dependencies with:
 ```bash
    cmake ~/nanodlpshield
 ```   
 build with:
 ```bash
    cmake --build ~/nanodlpshield
 ```  
 install with:
 ```bash
    cmake --install ~/nanodlpshield
 ```    
 Run the installed app with:
 ```bash
    ./NanoDlpShield
 ```
 and note the resulting name return, which should be:
     ```bash
     Name: /dev/pts/1
     ```
    (the number will increment if you restart the app without rebooting the pi)

 Open NanoDLP Web GUI, set the shield mode to USB/I2C and set the address to the value returned from 'Name:'.

 Go to Tools -> RAMPS Terminal and test some basic commands.
