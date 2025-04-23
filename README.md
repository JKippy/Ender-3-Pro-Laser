# MarlinLaser

This project uses a Creality 4.2.7 mainboard running Marlin 2.1.2.4 to control a laser engraver/cutter.
This should be easy for anyone to port over to using any 3d printer mainboard.

There are 4 parts to this project.

# Overview
+ Hardware
   - Laser Machine
   - Mainboard
   - Endstops (optional)
+ Marlin Firmware
+ Cutting Software (Lightburn or your preferred software)
+ Questions, Comments, and Bug Reports

# Hardware
I am using a Laser Tree 40w(5w Optical) laser module fitted onto an Ender 3 Pro. [Laser module bracket I made](https://www.thingiverse.com/thing:7018631)

I'm using 3 physical switch type endstops on X, Y, and Z, so the machine can home to 0,0 each time it runs. They aren't necessary, but they're nice to have.

Make sure your laser machine's stepper power supply is within specs for your selected mainboard. I belive the Creality 4.2.7 supports 12 and 24 volts. The Laser Tree 40w module operates at 12v and the Ender 3 Pro is a 24v machine so I had to use a buck converter to bring it down to 12v.

* Attach your power, stepper, and endstops in the normal locations.  If you want dual X or dual Y, you have two extra drivers to use onboard already.
* The laser's power is attached to the hotend heat connector (bottom row, far right pair on the green connector)
   * How I did this was taking a 3 pin JST-XH cable that came with my laser module and de-pinned one side. I then took a 2 pin JST-XH connector and attached the VCC and GND wires and plugged that into a JST-XH header on my buck converter's output wires. My buck converter's input wires were then inserted into the hotend heat connector
* The laser signal/PWM pin goes to the IN pin of the BLTouch header.  The center pin of the white 5 pin header.
   * Going back to my 3 pin JST-XH cable- our VCC and GND are taken care of but we still have the PWM wire not connected to anything. I then took a 5 pin JST-XH connector and installed the PWM wire into the middle pin. Even though I am only using 1/5 pins, I still used a 5 pin connector so it would connect nicely into the BLTouch header.

<img src='Creality.4.2.7 Connection Diagram.png' alt='Creality 4.2.7 Mainboard Connection Diagram'>

# Marlin Firmware

For this documentation, I'm using the Marlin 2.1.2.4 firmware. I tried 2.1.2.5 first and it didn't work so I just began stepping down until one did. 

The configuration is pretty simple.  It just drops 3 (or 5) things into your Marlin directory, make 1 change, and build it.
Copy the files from this repo into the following locations:

+ ./Marlin/Configuration.h 
+ ./Marlin/Configuration_adv.h 
+ ./Marlin/_Bootscreen.h
+ ./Marlin/_Statusscreen.h
+ ./Marlin/src/pins/stm32f1/pins_CREALITY_V427_JWS_LASER.h

Edit the file ./Marlin/src/pins/stm32f1/pins_CREALITY_V4.h
Add the following 3 lines to the bottom of pins_CREALITY_V4.h 

```
// -jws 20220128 - This is to override any pin definitions earlier, without messing with a bunch of other files.
// It'll be a lot easier to port that to future Marlin versions.
#include "pins_CREALITY_V427_JWS_LASER.h"
```

Build, flash, you're done.

I built the Config*.h files from the Marlin example file, not the Creality configuration. I included diff files, if you prefer to look for changes that way.  

I made the Bootscreen and Statusscreen graphics, just because I wanted it to look nice. 

# Cutting Software

I'm using Lightburn on my laptop for the time being.  When you set up the machine, just use the Marlin controller type.  In the device settings, you can choose M03/M05 or Inline. 

I don't have an air assist or laser cooler yet, so I can't confirm if that part is working, and don't yet have the pins assigned.

Under the GCode tab in the Device settings, you'll need to add these 3 lines to the Start GCode.
```
; In Marlin, inline mode is enabled with 'M3 I' and disabled with 'M5 I'.
 M3 I
```

# Questions, Comments, and Bug Reports

If you wish to contact me, you can do it through here, or via my [JWSmythe.com](https://jwsmythe.com).

If you find a bug, report it through the Issues tab at the top of the page. That way, others encountering the bug you found will be able to fix it before I put the next release together.
