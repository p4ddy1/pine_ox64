# Flashing using picoprobe

## Prerequisites
* Raspberry Pi Pico
* Modified [picoprobe](https://github.com/sanjay900/picoprobe/releases/tag/v1.0)
* [BLDevCube](https://dev.bouffalolab.com/download)


## Install picoprobe

* Put the Pi Pico in bootloader mode. Hold the BOOTSEL button while plugging the USB Cable in.
* Move the `picoprobe.uf2` file to the appearing USB storage device

## Connect the Pi to the Ox64

Pico GP4/UART1 TX -> 0x64 GPIO15/UART0_RX
Pico GP5/UART1 RX -> 0x64 GPIO14/UART0_TX
Pico GND -> 0x64 GND

## Ox64 Bootloader Mode
Simply hold the BOOT button before providing power to the board. Release after it is powered up.

## Flash Images
* Open BLDevCube
* Select BL808
* Switch to the MCU Tab
* Select the Pi Pico USB Device as UART Interface. Uart Rate: 2000000
* General Options
    * M0
      * Group: group0
      * Image Addr: 0x58000000
      * helloworld_bl808_m0.bin
    * D0
      * Group: group0
      * Image Addr: 0x58000000
      * helloworld_bl808_d0.bin
* Click Create&Download
* Power Cycle the Ox64 after the flash ended successfully
* Click Log to view the log output of the board