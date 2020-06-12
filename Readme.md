# Boosted Battery extended range (B2XR) RLOD FIX

<img src="Pictures/RLOD.jpeg" width="65%">

DISCLAMER: DO IT AT YOUR OWN RISK, these batteries can become extremely dangerous in case of fault. They can self ignite, explode and kill. Before starting, you must prepare your working area, make sure you can easily move the battery to a safe zone in the case things are really going wrong.


As of 11th June 2020, I haven't thoroughly tested the battery after the FIX (it is too early) -> I don't know if there are any potential side effects yet...


## BOM
* FT232H interface or equivalent recognized by flashrom, for example: [UM232H-B-NC](https://www.digikey.com/product-detail/en/ftdi-future-technology-devices-international-ltd/UM232H-B-NC/768-1160-ND/3770837)
* Soldering station for SMD components.
* Small diameter wire (I used KYNAR WRAPPING WIRE AWG 30).
* Linux computer with flashrom v1.2 installed -> precompiled GNU Linux x64 bin [here](https://raw.githubusercontent.com/jonataubert/RLOD_B2XR/master/Flashrom/flashrom).
* Optional: multimeter
* Optional: USB extender for the FT232H.


## Disassembling the battery
Follow this video: https://www.youtube.com/watch?v=XqM4JGT5Mbk


## Optional: Check the voltage levels of the cells

In my case the cells were well balanced:

   | Cell #  | voltage  |
   |---------|-------------|
   | 1      | 3.477 |
   | 2      | 3.481 |
   | 3      | 3.445 |
   | 4      | 3.477 |
   | 5      | 3.482 |
   | 6      | 3.481 |
   | 7      | 3.480 |
   | 8      | 3.480 |
   | 9      | 3.481 |
   | 10     | 3.480 |
   | 11     | 3.480 |
   | 12     | 3.480 |
   | 13     | 3.480 |


Note 1: I don't know if the method works if your cells way too unbalanced.  
Note 2: I will add a description of the pins to be probed soon :)

## Add probes to your FT232H

To be more effective in the process, I suggest you to equip the pins D0 to D3 and GND with this type of probe, it will allow to easily connect it to the flash memory (see next step).
<img src="https://raw.githubusercontent.com/jonataubert/RLOD_B2XR/master/Pictures/FT232H_probes.jpg" width="30%">




## Make sure the  FT232H is recognized by flashrom

1. Connect the FT232H to your computer.
2. In the Linux terminal launch: **flashrom -p ft2232_spi:type=232H**  
If your FT232H is properly recognized by flashrom, you should see:  
> flashrom v1.2 on Linux 4.10.0-38-generic (x86_64)
>
> flashrom is free software, get the source code at https://flashrom.org
>
> Using clock_gettime for delay loops (clk_id: 1, resolution: 1ns).
> 
> No EEPROM/flash device found.
>
> Note: flashrom can never write if the flash chip isn't found automatically.  


In the case your the FT232H is not recognized, run the following commands and go to step 2.  
**sudo rmmod ftdi_sio**  
**sudo rmmod usbserial**


3) Once properly recognized by flashrom, **keep the FT232H connected to the USB port**.  
This is very **important** because flashrom sets the electrical mode of the GPIOs.  
Thus, it is **safer** to do it before attempting a connection to the SPI flash as no current will be accidentally injected!


## Disable the microcontroller

* For this you need to connect MCLR to GND.
<img src="https://raw.githubusercontent.com/jonataubert/RLOD_B2XR/master/Pictures/MCLR_GND.png" width="20%">


* Solder two wires here so that you can bind them!
<img src="https://raw.githubusercontent.com/jonataubert/RLOD_B2XR/master/Pictures/MCLR_CONNECT.png" width="25%">

## Remove the conformal coating around the flash memory (U5)

* Use plastic tools, example: <img src="https://raw.githubusercontent.com/jonataubert/RLOD_B2XR/master/Pictures/tool_coating.jpg" width="25%">



## Solder the wires to the SPI FLASH memory 

<img src="https://raw.githubusercontent.com/jonataubert/RLOD_B2XR/master/Pictures/FLASH_CONNECT.jpg" width="30%">



## Connect the FT232H interface to the SPI FLASH memory 

WARNING: make sure the FT232H gpios were properly configured by the command **flashrom -p ft2232_spi:type=232H**  
* Connect the FT232H to the SPI wires, start with the GND, you can use the one already bound to MCLR!
<img src="https://raw.githubusercontent.com/jonataubert/RLOD_B2XR/master/Pictures/MCLR_GND_FT232.jpg" width="65%">  
<img src="https://raw.githubusercontent.com/jonataubert/RLOD_B2XR/master/Pictures/FT232H_FLASH.png" width="65%">





## Unleash the beast

For this step, you will need your Linux terminal and your finger to keep pressing the push button of the battery during the flashing operations. (As the MCU is disabled the logic power rail is not held by the FW anymore).  

Have a look at the FAQ before doing this.

* **Keep pressing the push button of the battery.**

* **flashrom -p ft2232_spi:type=232H -r ./dumpflash.bin**

> flashrom v1.2 on Linux 4.10.0-38-generic (x86_64)  
> flashrom is free software, get the source code at https://flashrom.org  
>   
> Using clock_gettime for delay loops (clk_id: 1, resolution: 1ns).  
> Found ISSI flash chip "IS25LP128" (16384 kB, SPI) on ft2232_spi.  
> Reading flash... done.

* **Release the push button**
* **Keep pressing the push button**  

* **flashrom -p ft2232_spi:type=232H -v ./dumpflash.bin**

> flashrom v1.2 on Linux 4.10.0-38-generic (x86_64)  
> flashrom is free software, get the source code at https://flashrom.org  
> Using clock_gettime for delay loops (clk_id: 1, resolution: 1ns).  
> Found ISSI flash chip "IS25LP128" (16384 kB, SPI) on ft2232_spi.  
> Verifying flash... VERIFIED.  

Note: Make sure the verification is "VERIFIED", otherwise try again to dump and verify.  
* **Release the push button**

* **Keep pressing the push button**  

* **flashrom -p ft2232_spi:type=232H -E**

> flashrom v1.2 on Linux 4.10.0-38-generic (x86_64)  
> flashrom is free software, get the source code at https://flashrom.org  
>   
> Using clock_gettime for delay loops (clk_id: 1, resolution: 1ns).  
> Found ISSI flash chip "IS25LP128" (16384 kB, SPI) on ft2232_spi.  
> Erasing and writing flash chip...


* **Wait for 5 minutes (use this opportunity to reflect about life stuff).**

* **Press ctrl-c to exit this never ending operation**

* **flashrom -p ft2232_spi:type=232H**
 
* **Release the push button and relax :)**

## Disconnect the FT232H from the FLASH chip and enable the MCU

* Disconnect the FT232H from the SPI CHIP (last wire to be removed is GND)
* Disconnect MCLR from GND



## Press the push button to switch the battery on, check for green light!
<img src="https://raw.githubusercontent.com/jonataubert/RLOD_B2XR/master/Pictures/B2XR_fixed.jpg" width="55%">


If you have any questions you can find me on the boosted board discord or at: pro(at)jonataubert.com


## FAQ 

Q: What should I do with the dumpflash.bin file?
A: This is the backup of the data that were stored in the flash before the erasing process. Keep it!

Q: What is your B2XR firmware?  
A: I don't know yet, I promise I will add it really soon!

Q: Ok, so we erased the content of the flash but would removing the SPI flash do the same?  
A: Nope, if you do so you will end up with a different RLOD error code.

Q: What happens if I write back the backup bin data to the flash?  
A: The RLOD comes back, isn't it nice ;P

Q: Does this method work if the cells are unbalanced by more than 500 [mV]?  
A: I don't know yet, please inform me if it works for you!

Q: Does it work with any firmware?  
A: I don't know yet.

Q: I accidentally released the push button while erasing the FLASH and got such messages:  
> Found ISSI flash chip "IS25LP128" (16384 kB, SPI) on ft2232_spi.  
> Erasing and writing flash chip... FAILED at 0x00069000! Expected=0xff, Found=0x00, failed byte count from 0x00069000-0x00069fff: 0x1000
> ERASE FAILED!  
> Looking for another erase function.  
> FAILED at 0x00069000! Expected=0xff, Found=0x00, failed byte count from 0x00069000-0x00069fff: 0x1000  
> ERASE FAILED!  
> No usable erase functions left.  
> FAILED!  
> Your flash chip is in an unknown state.  

A: run **flashrom -p ft2232_spi:type=232H** to reset the GPIOs, then you can restart the erasing process, if you still get this message, go ahead it may have properly erased the chip anyway (at least it works with me).


