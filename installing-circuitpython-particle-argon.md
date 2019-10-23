Installing CircuitPython on the nrf52840-based Particle Argon
=============================================================

https://learn.adafruit.com/circuitpython-on-the-nrf52/overview

-	https://circuitpython.org/downloads
	-	-> https://circuitpython.org/board/particle_argon/ downloaded version `4.0.1` `.uf2`

Install JLink software
----------------------

download the .deb 64-bit Linux from: https://www.segger.com/downloads/jlink#J-LinkSoftwareAndDocumentationPack

```
mkdir -p ~/projects/jlink
mkdir -p ~/projects/jlink/downloads
cd ~/projects/jlink
mv ~/Downloads/JLink_Linux_V648b_x86_64.deb downloads/
sudo dpkg -i ./downloads/JLink_Linux_V648b_x86_64.deb
```

Install nrfjprog
----------------

To flash using Jlink, `nrfjprog` is used

The binary files provided by the manufacturer of the chip can be downloaded via the following page : https://www.nordicsemi.com/Software-and-Tools/Development-Tools/nRF-Command-Line-Tools

[ Downloads ] tab

Select Linux64 in dropdown

choose `Linux64 10.2.1` as Selected version

`nRFCommandLineTools1021Linuxamd64tar.gz`

Doc: https://infocenter.nordicsemi.com/index.jsp?topic=%2Fug_nrf5x_cltools%2FUG%2Fcltools%2Fnrf5x_command_line_tools_lpage.html&cp=6_1

### extract and install

This is how I install things like this on my system. YMMV.

```
cd ~/projects/jlink
mv ~/Downloads/nRFCommandLineTools1021Linuxamd64tar.gz downloads/
mkdir -p tmp/
tar -xzvf downloads/nRFCommandLineTools1021Linuxamd64tar.gz -C ./tmp/
tar -xzvf tmp/./nRF-Command-Line-Tools_10_2_1_Linux-amd64.tar.gz -C ./tmp/

# mkdir -p ~/bin/linux/containers/
rm -rf ~/bin/linux/containers/mergehex/
rm -rf ~/bin/linux/containers/nrfjprog/

mv tmp/mergehex/ ~/bin/linux/containers/
mv tmp/nrfjprog/ ~/bin/linux/containers/
rmdir tmp
chmod +x ~/bin/linux/containers/mergehex/mergehex
chmod +x ~/bin/linux/containers/nrfjprog/nrfjprog
# add  ~/bin/linux/containers/nrfjprog/ to $PATH
# modpath ~/bin/linux/containers/nrfjprog/
nrfjprog --version
```

```
nrfjprog version: 10.2.1
JLinkARM.dll version: 6.48b
```

<!-- See [Flash bootloader using Arduino](flash-adafruit-bootloader-with-arduino.md) -->

Manually Burning Adafruit Bootloader via nrfjprog (using Jlink)
===============================================================

https://learn.adafruit.com/circuitpython-on-the-nrf52/build-flash-particle

Going with **Option 2**: Manually Burning the Bootloader via `nrfjprog` (using Jlink)

Download (`git clone`) Adafruit nRF52840 Bootloader

```
cd ~/projects/jlink/
# git clone https://github.com/adafruit/Adafruit_nRF52_Bootloader
cd Adafruit_nRF52_Bootloader
git submodule update --init --recursive
```

To flash, requires JLink hooked up to Particle (SWD header).

Also, board needs to be powered, so connect Host computer USB to Particle board's micro-USB port.

```
cd ~/projects/jlink/Adafruit_nRF52_Bootloader
make BOARD=particle_argon clean
make BOARD=particle_argon
make BOARD=particle_argon erase
make BOARD=particle_argon sd
make BOARD=particle_argon flash
```

After this step the board should reset, and then the `ARGONBOOT` drive should be mounted, letting you know it can accept a `.uf2` image now.

### Install CircuitPython

Drag the CircuitPython `.uf2` onto `ARGONBOOT`, e.g. `adafruit-circuitpython-particle_argon-en_US-4.0.1.uf2`

The board will reset when CircuitPython flash is complete, and will now show up as `CIRCUITPY` drive. `boot_out.txt` contents:

```
Adafruit CircuitPython 4.0.1 on 2019-05-22; Particle Boron with nRF52840
```

(not sure exactly why it says Boron here)

#### To get back to `ARGONBOOT`

If you need to upgrade/change the CircuitPython image (not the nRF52 bootloader), the Particle board running the Adafruit nRF52 bootloader will need to be put into "USB bootloader mode". To get to USB bootloader mode, hold down the `MODE` button while clicking the `RESET` button. The board's RGB LED should turn green and the `ARGONBOOT` drive will present itself/be mounted.

Here's how I flashed a built-from-source `.uf2` file:

```
cd ~/projects/circuitpython/ports/nrf/
ls -d build-*
gio open ./build-particle_argon
# drag firmware.uf2 onto ARGONBOOT
```

The version info from this build method will look something like this:

`Adafruit CircuitPython 4.0.1-5-g63b253c33 on 2019-05-22; Particle Argon with nRF52840`

connect to serial console
-------------------------

```
ls /dev/ttyACM*
# then using screen
screen /dev/ttyACM0 115200
```

> Adafruit CircuitPython 4.0.1-5-g63b253c33 on 2019-05-22; Particle Argon with nRF52840
>
> Adafruit CircuitPython 5.0.0-alpha.0-128-g347fbb652-dirty on 2019-08-02; Particle Argon with nRF52840

CircuitPython programs
======================

The mount point on my system is something like this: `/media/<USERNAME>/CIRCUITPY`. I use the Emacs editor to directly edit the `CIRCUITPY/code.py` file on the `CIRCUITPY` drive.

### blink

```python3
import time
import board
from digitalio import DigitalInOut, Direction, Pull

led = DigitalInOut(board.BLUE_LED)
led.direction = Direction.OUTPUT

while True:
    led.value = False
    time.sleep(0.1)
    led.value = True
    time.sleep(0.1)
```

### mode button (closest to SWD connector)

```python
# CircuitPython on Particle Demo - GPIO
import time
import board
from digitalio import DigitalInOut, Direction, Pull

led_pin = DigitalInOut(board.BLUE_LED)
led_pin.direction = Direction.OUTPUT
led_pin.value = False

button = DigitalInOut(board.MODE)
button.direction = Direction.INPUT

while True:
    # Check for a button press
    if not button.value:
        print('Button Pressed!')
    led_pin.value = button.value
    time.sleep(0.01)
```
