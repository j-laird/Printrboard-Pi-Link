# Printrboard-Pi-Link
 The Printrboard-Pi-Link is a Printed Circuit Board Assembly that connects a Printrbot Printrboard with a newer-model Raspberry Pi (B+, Raspberry Pi 2 B, etc).

![Printrboard Pi Link](http://garthvh.com/assets/img/printrboardpilink/printrboard_pi_link_01.jpg "Printrboard Pi Link")

## Feature Overview
The Printrboard-Pi-Link has the following unique features:
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

![Printrboard Pi Link Board Top](http://garthvh.com/assets/img/printrboardpilink/board_top_rev_b.png "Printrboard Pi Link Board Top")

![Printrboard Pi Link Board Top](http://garthvh.com/assets/img/printrboardpilink/board_bottom_rev_b.png "Printrboard Pi Link Board Bottom")

### BUILD INFORMATION

If you would like to make your own Printrboard-Pi-Link and provide some beta feedback, please be aware that the Printrboard-Pi-Link board uses some tiny surface mount components and is an advanced soldering project that will also require flashing the firmware on your Printrboard.

To order your own PCB's this git repository can be downloaded as a zip and uploaded directly to [OSHPark](https://www.oshpark.com/) where you can order three boards for less than $30.

![Printrboard Pi Link](http://garthvh.com/assets/img/printrboardpilink/pilink_oshpark.png "Printrboard Pi Link Order")

All parts but L1 (inductor) can be sourced from Digikey. A pre populated [shopping cart](http://www.digikey.com/short/71w84q) with enough parts to build this (minus ribbon cable and 16awg wire) is available.   NOTE THIS CART IS FOR REV B.  Rev A used a different fuse holder.

[Bill of Materials](https://docs.google.com/spreadsheets/d/1JWQViEhnYA_FKTI_PxGergLy6LZkPQN6mZyLyM8gfQU/edit?usp=sharing)

### CONFIGURATION

When creating the Printrboard-Pi-Link, I recognized that some printrbot models "hide" the Printrboard underneath the printer, while others leave the board exposed.  When this board is installed on the Raspberry Pi, only two cables need be connected from this board to the Printrboard: a ribbon cable to EXP1, and a 4/6-wire power cable to the power connection.  

It is expected that a short ribbon cable can be supplied for users who want the Raspberry Pi located back-to-back with the Printrboard, and a longer ribbon cable can be supplied for those "hidden" Printrboards.  Similarly, the supplied 4 or 6-pin power extension has only a connector on one end -- and can be cut to length by the customer (thus the screw terminals).

### FIRMWARE

In order to enable the hardware UART on the Printrboard (rather than the USB CDC serial port), use of this board requires a special build of firmware.  Standard firmware versions will not communicate directly, although it would be possible to communicate using the traditional micro USB cable to one of the Raspberry Pi's USB ports.

There are instructions available for flashing the Printrboard firmware available from [Printrbot support](http://help.printrbot.com/Guide/How+to+Reflash+Firmware).

* [Rev D Firmware](https://github.com/j-laird/Marlin/releases/tag/PiBachelor--V3)
* [Rev F4/F5 Firmware](https://github.com/j-laird/Marlin/releases/tag/PiLassen-V3)

### RASPBERRY PI / OCTOPRINT SETUP

The hardware UART on the Raspberry Pi must be enabled.  Run raspi-config and select Advanced Options.  Under Serial, disable login over serial port.

Modify /boot/config.txt to add init_uart_clock=4000000

You may need to add the UART serial port to octoprint under Settings > Serial Connection.  In the Additional serial ports section add /dev/ttyAMA0 so that you can select the UART port in OctoPrint.  250000 is the baudrate that has been verified to be working.

![Octoprint serial connection](http://garthvh.com/assets/img/printrboardpilink/octoprint_connection.png "Octoprint serial connection settings")

Install the wiringPi library using the following [instructions](https://projects.drogon.net/raspberry-pi/wiringpi/download-and-install/).

Modify ~/.octoprint/config.yaml to add the following lines under system: actions to add the commands to the OctoPrint System menu.

    - action: shutdown printbot
      command: gpio -g write 2 1
      name: Kill Printrboard 12V supply
    - action: poweron printrbot
      command: gpio -g write 2 0
      name: Enable Printrboard 12V supply

![Octoprint System Menu](http://garthvh.com/assets/img/printrboardpilink/octoprint_system_menu.png "Octoprint System Menu")

Modify /etc/init.d/octoprint to add the following lines in the do_start() function, right above RETVAL="$?"

    gpio -g export 2 out
    gpio -g mode 2 out
    gpio -g write 2 0


# DISCLAIMERS

Although using a Raspberry Pi with [Octoprint](http://octoprint.org/) is a fabulous way to run a print job semi-unattended, and this board enables additional features in that regard, it is **categorically not endorsed/recommended** as a safe practice.  The additional safety and remote shutdown features should not encourage you to leave the printer at home/work printing.  For those of you who void warranties, tear up manuals, and eat your steak rare -- hopefully this provides additional safety margin.

But seriously - don't print unattended.  
