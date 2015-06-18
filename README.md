# Printrboard-Pi-Link
PCBA linking Printrbot Printrboard and Raspberry Pi

**Overview**

The Printrboard-Pi-Link is intended to connect a Printrbot Printrboard with a newer-model Raspberry Pi (B+, Raspberry Pi 2 B, etc).  It has the following unique features:
* Accepts 12v input on either 4-pin(ATX 12V) or 6-pin(PCI-E) Molex Minifit-Jr connectors
* Supplies switched (see below) 12V power to Printrboard via screw-type Phoenix connector
* Produces high-current (3A max) 5V power for Raspberry Pi (no wall-wart needed)
* Uses UART connection on Raspberry Pi GPIO (replaces micro-USB connection to Printrboard)
* Exposes 3 GPIO intended for:
  * Printrbot "ready to print" LED
  * Printrbot "push to print" button
  * Wi-Fi mode switch between AP and infrastructure mode
* Allows the Raspberry Pi to shutoff 12V power to Printrboard (in case bad things happen)
* Exposes an external safety 12v shutoff for user-supplied circuits (smoke detector, etc.)
* Adds automotive-style ATO fuse in 12V path

**BUILD INFORMATION**

All parts but L1 can be sourced from Digikey. A shopping cart with enough parts to build this (minus ribbon cable and 16awg wire) can be found at http://www.digikey.com/short/71w84q.   NOTE THIS CART IS FOR REV B.  Rev A used a different fuse holder.

**CONFIGURATION**

When creating the Printrboard-Pi-Link, I recognized that some printrbot models "hide" the Printrboard underneath the printer, while others leave the board exposed.  When this board is installed on the Raspberry Pi, only two cables need be connected from this board to the Printrboard: a ribbon cable to EXP1, and a 4/6-wire power cable to the power connection.  

It is expected that a short ribbon cable can be supplied for users who want the Raspberry Pi located back-to-back with the Printrboard, and a longer ribbon cable can be supplied for those "hidden" Printrboards.  Similarly, the supplied 4 or 6-pin power extension has only a connector on one end -- and can be cut to length by the customer (thus the screw terminals).

**FIRMWARE**

In order to enable the hardware UART on the Printrboard (rather than the USB CDC serial port), use of this board requires a special build of firmware.  Standard firmware versions will not communicate directly, although it would be possible to communicate using the traditional micro USB cable to one of the Raspberry Pi's USB ports.

**RASPBERRY PI / OCTOPRINT SETUP**

The hardware UART on the RPI must be anabled.  Run raspi-config and select Advanced Options.  Under Serial, disable login over serial port.

Modify /boot/config.txt to add init_uart_clock=4000000

TBD: add GPIO / wiringpi setup here.

**DISCLAIMERS**

Although using Raspberry Pi / Octoprint is a fabulous way to run a print job semi-unattended, and this board enables additional features in that regard, it is **categorically not endorsed/recommended** as a safe practice.  The additional safety and remote shutdown features should not encourage you to leave the printer at home/work printing.  For those of you who void warranties, tear up manuals, and eat your steak rare -- hopefully this provides additional safety margin.

But seriously - don't print unattended.  
