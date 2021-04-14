circuitpython-on-particle
=========================

CircuitPython on Particle Argon, Xenon, Boron, etc.

**2021-04-14**: Updating for Xenon and Argon

-	[Particle deprecates Particle Mesh, discontinuing Particle Xenon](https://blog.adafruit.com/2020/01/28/particle-depricates-particle-mesh-discontinuing-particle-xenon-circuitpython-particle/)
	-	https://blog.particle.io/mesh-deprecation/
	-	https://docs.particle.io/tutorials/learn-more/xenon-circuit-python/

Adafruit has a [bootloader for nRF52](https://github.com/adafruit/Adafruit_nRF52_Bootloader) and also has CircuitPython builds for:

-	Particle Xenon (discontinued), **Bluetooth** and OpenThread
-	Particle Argon - **WiFi** and **Bluetooth**
-	Particle Boron - **LTE** and **Bluetooth**

The footprint and layout for pins matches the Adafruit FeatherWing layout, so is compatible with many Wings.

Install Adafruit nRF52 bootloader
---------------------------------

To use CircuitPython, the nrf52840 module on the Particle Argon needs to be flashed with Adafruit's bootloader. For my setup, this uses Segger J-Link (connected to Particle board header with matching SWD adapter) and OpenOCD.

By burning the Adafruit nRF52 Bootloader on the Particle, you are overwriting the Particle mesh firmware already on your board.

Here are the steps, including installing CircuitPython itself: [Installing CircuitPython on nRF52840-based Particle ](installing-circuitpython-particle-jlink.md)

Build CircuitPython from Source
-------------------------------

Adafruit supports building a released `.uf2` image for Particle (Argon, Boron, Xenon) now in 4.X+ releases, along with 50+ other boards.

Adafruit publishes `.uf2` files at: https://circuitpython.org/downloads

-	https://circuitpython.org/board/particle_argon/
-	https://circuitpython.org/board/particle_xenon/
-	https://circuitpython.org/board/particle_boron/

But building CircuitPython from source is also simple: [Build CircuitPython for Particle Argon ](building-circuitpython-particle-argon.md)


Xenon (Bluetooth)
-----------------

Specs:

-	Nordic Semiconductor nRF52840 SoC

	-	ARM Cortex-M4F 32-bit processor @ 64MHz
	-	1MB flash, 256KB RAM
	-	IEEE 802.15.4-2006: 250 Kbps
	-	Bluetooth 5: 2 Mbps, 1 Mbps, 500 Kbps, 125 Kbps

-	On-board additional 4MB SPI flash

https://docs.particle.io/datasheets/discontinued/xenon-datasheet/

Argon (Wifi and Bluetooth)
--------------------------

Specs:

-	Espressif ESP32-D0WD 2.4G Wi-Fi coprocessor

	-	On-board 4MB flash for ESP32
	-	802.11 b/g/n support, 802.11n (2.4 GHz), up to 150 Mbps

-	Nordic Semiconductor nRF52840 SoC

	-	ARM Cortex-M4F 32-bit processor @ 64MHz
	-	1MB flash, 256KB RAM
	-	IEEE 802.15.4-2006: 250 Kbps
	-	Bluetooth 5: 2 Mbps, 1 Mbps, 500 Kbps, 125 Kbps

-	On-board additional 4MB SPI flash

https://docs.particle.io/datasheets/wi-fi/argon-datasheet/

Boron (Cellular and Bluetooth)
------------------------------

Specs:

-	u-blox SARA R410 LTE modem

	-	LTE CAT M1 module with global hardware support (MVNO support for US only)
	-	3GPP Release 13 LTE Cat M1

-	Nordic Semiconductor nRF52840 SoC

	-	ARM Cortex-M4F 32-bit processor @ 64MHz
	-	1MB flash, 256KB RAM
	-	IEEE 802.15.4-2006: 250 Kbps
	-	Bluetooth 5: 2 Mbps, 1 Mbps, 500 Kbps, 125 Kbps

-	On-board additional 4MB SPI flash

https://docs.particle.io/datasheets/boron/boron-datasheet/
