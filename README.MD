#	JAMMIX Firmware Updater Core and Testers

Based on the work by JimmyStones and others (see README1.MD and Acknowledgements).

This core allows flashing of a firmware binary to the STM32 on JAMMIX, for future updates.

It uses the standard OSD menu to load the firmware file, and shows a few steps to carry out the update.

This core also provides vairous testing for controller input testing including SNAC, audio testing, and video testing.  



## Core, Firmware and Directory Structure 

Regular users that want to use update their firmware and use the testers should downownload the core and the frimware from: https://github.com/JAMMIX1o/JAMMIX_FW_Updater/tree/main/releases 

There are 2 files required for updating JAMMIX’s firmware:

JAMMIX_FW_Updater_######.rbf

STM32_FW_JAMMIX_######.bin

- Download the “JAMMIX_FW_Updater_######.rbf” and place it at “/media/fat/” if you use ssh/sftp/ftp to access your MiSTer or in the root of the SD Card if the SD Card is mounted on a PC.

- Create a directory called “JAMMIX_FW” in “/media/fat/” if you use ssh/sftp/ftp to access your MiSTer, or in the root of the SD Card if the SD Card is mounted on a PC. This directory must be named with all capitals and an underscore as shown. 

- Download the “STM32_FW_JAMMIX_######.bin” and place it in “JAMMIX_FW” directory that was created in the step above.

- Launch the JAMMIX_FW_Updater core. Instructions for flashing are in the core.

- Set dip switches 7, 8 and 9 to “ON”

- Using the OSD menu to “Mount fw file *.BIN” the STM32_FW_JAMMIX_######.bin

- Briefly short/jumper pins 1 and 3 on the PROG header, or briefly push the STM32 Reset button if you have an optional FW/HD Audio Bridge Board.

- Using the OSD menu option to “Flash Firmware!”

It’s highly recommended that users have a USB keyboard or USB controller when turning DIP 9 to ON as DIP 9 prevents controls/input  from the JAMMA edge and CPS2 Kick edge when on. 

If the process pauses for a long time (30 seconds or more), please use the Reset Core option on the OSD, and try again.

If you find you have issues with a USB keyboard or controllers not working, please set DIP switch 9 to OFF, disconnect and reconnect power to JAMMIX, launch the JAMMIX_FW_Updater core, then try the Flash process again.



## Configuration

Before using the variuos testers of this core with the JAMMA edge on JAMMIX you must have complted these configurations:

JAMMIX requires a configuration to the MiSTer.ini config file and the u-boot.txt config file. 

MiSTer.ini Configuration:

The MiSTer.ini can be found at “/media/fat/MiSTer.ini” if you use ssh/sftp/ftp to access your MiSTer or in the root of the SD Card if the SD Card is mounted on a PC. 
Append the following to the bottom of the MiSTer.ini. 
```;JAMMIX
; Disable merging input devices. Use if only player 1 works.
; Leave no_merge_pid empty to apply this to all devices with the same VID.
no_merge_vid=0x8888
;no_merge_pid=0x8888
```
- The line “;no_merge_pid=0x8888 must be included.
- If you use alternative configurations “mister_alt_1.ini, mister_alt_2.ini and  mister_alt_1.ini” this should also be appended to those files as well. 

15 kHz Monitors/Arcade Monitors/PVM & BVM can also set to in the MiSTer.ini 
- Set “composite_sync=1” in the MiSTer.ini file, this sets MiSTer for RGBS video output. 

```composite_sync=1```

31KHz Monitors/Computer Monitor 
- Set “composite_sync=0” in the MiSTer.ini file, this sets MiSTer for RGBHV/VGA video output.

```composite_sync=0```

Note: JAMMIX can output RGB from the JAMMA edge and D-Sub port simultaneously. Component video is available via the D-Sub port only." 

u-boot.txt Configuration:

The u-boot.txt can be found at “/media/fat/linux/u-boot.txt”  if you use ssh/sftp/ftp to access your MiSTer or in the “linux” directory from the root of the SD Card if the SD Card is mounted on a PC. 
Remove any existing text in the u-boot.txt file. If this file does not exist then it should be created. Add the following to the u-boot.txt. 

```v=loglevel=4 usbhid.jspoll=1 xpad.cpoll=1 usbhid.quirks=0x8888:0x8888:0x040```

## JAMMA/CHAMMA/MVS HARNESS WARNING

Due to the CHAMMA compatibility feature of JAMMIX, pins e and 27 can not be used as the sole Ground (GND / 0V) wire connections to the PSU. At least one other Ground wire must be used for the PSU Ground connection.
Pins e and 27 can however be used for ground connections for buttons / switches. The JAMMIX firmware protects against pins e and 27 being the sole PSU grounds, and will prevent JAMMIX from powering up. 

Unfortunately, some cabinets and sellers of JAMMA harnesses neglect to add the 'key' to the JAMMA plug, which is supposed to prevent backwards-connection of the plug. Please be very careful to plug the harness onto JAMMIX correctly. The +12V, +5V, -5V power wires of the plug should be nearest the notch on the JAMMIX edge connector.

| Solder Side       |                   |                      |   |    | Parts Side           |                   |                   |
|-------------------|-------------------|----------------------|---|----|----------------------|-------------------|-------------------|
| MVS Multi Slot    | CHAMMA            | JAMMA                |   |    | JAMMA                | CHAMMA            | MVS Multi Slot    |
| GND               | GND               | GND                  | A | 1  | GND                  | GND               | GND               |
| GND               | GND               | GND                  | B | 2  | GND                  | GND               | GND               |
|                   |                   | +5v'                 | C | 3  | +5v'                 |                   |                   |
|                   |                   | +5v'                 | D | 4  | +5v'                 |                   |                   |
|                   |                   | -5v'                 | E | 5  | -5v'                 |                   |                   |
|                   |                   | +12v'                | F | 6  | +12v'                |                   |                   |
|                   |                   | Key, No Pin          | H | 7  | Key, No Pin          |                   |                   |
|                   |                   | Coin Counter 2       | J | 8  | Coin Counter 1       |                   |                   |
|                   |                   | Coin Lockout         | K | 9  | Coin Lockout         |                   |                   |
|                   |                   | Speaker (-)          | L | 10 | Speaker (+)          |                   |                   |
| Test Switch       |                   | NC                   | M | 11 | NC                   |                   | Audio + (Mono)    |
|                   |                   | Video Analog Green   | N | 12 | Video Analog Red     |                   |                   |
|                   |                   | Video Composite Sync | P | 13 | Video Analog Blue    |                   |                   |
|                   |                   | Service Switch       | R | 14 | Video Ground         |                   |                   |
| Coin D            |                   | Tilt/Slam            | S | 15 | Test                 |                   | NC                |
|                   |                   | Coin B               | T | 16 | Coin A               |                   |                   |
|                   |                   | Player 2 Start       | U | 17 | Player 1 Start       |                   |                   |
|                   |                   | Player 2 Up          | V | 18 | Player 1 Up          |                   |                   |
|                   |                   | Player 2 Down        | W | 19 | Player 1 Down        |                   |                   |
|                   |                   | Player 2 Left        | X | 20 | Player 1 Left        |                   |                   |
|                   |                   | Player 2 Right       | Y | 21 | Player 1 Right       |                   |                   |
|                   |                   | Player 2 Button 1    | Z | 22 | Player 1 Button 1    |                   |                   |
|                   |                   | Player 2 Button 2    | a | 23 | Player 1 Button 2    |                   |                   |
|                   |                   | Player 2 Button 3    | b | 24 | Player 1 Button 3    |                   |                   |
| Player 2 Button 4 | Player 2 Button 4 | NC                   | c | 25 | NC                   | Player 1 Button 4 | Player 2 Button 4 |
| Game Select Down  | Player 2 Button 5 | NC                   | d | 26 | NC                   | Player 1 Button 5 | Game Select Up    |
| GND               | Player 2 Button 6 | GND                  | e | 27 | GND                  | Player 1 Button 6 | GND               |
| GND               | GND               | GND                  | f | 28 | GND                  | GND               | GND               |



##	MiSTer Input Tester

Befor using this Input Tester buttons must be defined from Menu core and defined in this core.

There are 4 test for input
 - Digital - shows joypad buttons for input devices 1,2,3,4. These are activated by the first, second, thrid, and fourth contollers MiSTer detects. To activate a controller press any button. (Default screen)
 - SNAC - shows hex code for button presses. testing with a MD/Gen controller is recomended as it can test all SNAC lines (Default Screen).
 - Analog - shows analog axes for first two input devices (Hold "Coin" to activate the analog test)
 - Advanced - shows all standard HPS sourced inputs (joystick, analog, paddle, spinner) simultaneously as well as a ps2 keyboard console (Hold "Start" to activate the Advanced test)
 
 Mode switching instructions are shown at the bottom of the screen.
 
 Video Test Patterns can be accessed by using the OSD under the Test Pattern menu option. Included test pattren are:
 - White screen
 - Red Screen
 - Green Screen
 - Blue Screen
 - RGB Ramp
 - SMPTE Color Bars
 
 A test tone is also provided durring these Video Test Pattrens for audio test.

## Acknowledgements

Orginal Input Test core Created by JimmyStones (http://github.com/jimmystones), with thanks to:
- alanswx (https://github.com/alanswx) for ideas, cross-platform simulation support, and the tutorial this core was based on
- Jotego (https://github.com/jotego) for his very tidy JTFRAME modules
- Porkchop Express (https://misteraddons.com/) for testing a bunch of devices
- Team JAMMIX for the addtions of Players 3 & 4, SNAC Testing, and Test Pattrens and Audio Tone.

## Core Hardware

An 8-bit system with a 40x30 character display, each character can be set to one of 256 colours. All relevant MiSTer inputs are mapped into memory regions.

### Components
 - Z80 CPU (tv80 by Guy Hutchison, based on VHDL T80 core by Daniel Wallner)
 - jtframe_vtimer for VGA timing (from JTFRAME (https://github.com/jotego/jtframe) by Jotego)
 - jtframe_cen24 for clock enables (from JTFRAME (https://github.com/jotego/jtframe) by Jotego)
 - 16Kb program ROM (port 1 cpu, port 2 download)
 - 16Kb work RAM (port 1 cpu)
 - 2Kb character ROM (port 1 cpu, port 2 download)
 - 2Kb character RAM (port 1 cpu, port 2 graphics)
 - 2Kb colour RAM (port 1 cpu, port 2 graphics)
 - 7 Memory-mapped IO regions (all read-only).  _Yes I know about MREQ but SFRs turned out to be annoying in sdcc so :)_
   - Hardware state (H/V Sync, H/V Blank etc) (8 bytes)
   - joystick_5->0 from HPS (192 bytes)
   - joystick_analog_5->0 from HPS (96 bytes)
   - paddle_5->0 from HPS (48 bytes)
   - spinner_5->0 from HPS (96 bytes)
   - ps2_key from HPS (11 bytes)
   - ps2_mouse from HPS (48 bytes, combination of ps2_mouse and ps2_mouseext with some padding to align bytes) - currently unused

### Memory Map
Start|End|Length|Name
---|---|---|---
0x0000|0x3FFF|0x4000|Program ROM
0x4000|0x47FF|0x0800|Char ROM
0x6000|0x6000|0x0001|System inputs (video signals)
0x7000|0x70BF|0x00C0|Joystick inputs
0x7100|0x715F|0x0060|Analog inputs
0x7200|0x722F|0x0030|Paddle inputs
0x7300|0x735F|0x0060|Spinner inputs
0x7400|0x740B|0x000C|PS2 keyboard input
0x7500|0x752F|0x0030|PS2 mouse input
0x8000|0x87FF|0x0800|Char RAM
0x8800|0x8FFF|0x0800|Colour RAM
0xC000|0xFFFF|0x4000|Work RAM

## Software

/src contains a C program compiled to Z80 assembly using sdcc.  

All required ROMs (compiled program and default MiSTer font) are built into this core.  

Selecting Load BIOS in the core allows hot loading of binary OS ROM files for easier development.
Selecting Load Font in the core allows hot loading of MiSTer .pf font files (https://github.com/MiSTer-devel/Fonts_MiSTer).

### Building the OS

#### Required packages
- `sdcc` 
- `srecord`
#### Build steps
- Run `make` in `/src/Makefile` to create binary ROM
- Run `od -An -t x1 -v src/os.bin > rtl/rom.hex` to create hex version of ROM for Quartus build

## License
This program is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version.

