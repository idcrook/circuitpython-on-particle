circuitpython-on-particle
=========================

CircuitPython on Particle Argon (and perhaps Boron, Xenon, etc. in the future.)

Install Adafruit nRF52 bootloader
---------------------------------

To use CircuitPython, the nrf52840 module on the Particle Argon needs to be flashed with Adafruit's bootloader. This requires a Segger J-Link (connected to Particle board header with matching SWD adapter)

By burning the Adafruit nRF52-Bootloader on the Particle, you are overwriting the firmware already on your Particle board.

Here are the steps, including installing CircuitPython itself: [Installing CircuitPython on the nrf52840-based Particle Argon ](installing-circuitpython-particle-argon.md)

Build CircuitPython from Source
-------------------------------

Adafruit supports building the `.uf2` image for Particle (Argon, Boron, Xenon) now in its 4.x releases, along with 50+ other boards.

Adafruit also now publishes the `.uf2` files for each at https://circuitpython.org/downloads, e.g. https://circuitpython.org/board/particle_argon/

But building from source is also very simple these days: [Build CircuitPython for Particle Argon ](building-circuitpython-particle-argon.md)
