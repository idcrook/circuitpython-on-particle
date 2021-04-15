Installing CircuitPython on the nrf52840-based Particle Xenon or Argon
======================================================================

2021-04-14: Update to include Xenon based on particle's own tutorial:

-	https://docs.particle.io/tutorials/learn-more/xenon-circuit-python/

Originally based on https://learn.adafruit.com/circuitpython-on-the-nrf52/overview

-	https://circuitpython.org/downloads
	-	-> https://circuitpython.org/board/particle_argon/ downloaded version `4.0.1` `.uf2`

In addition, you'll need to install the following software:

-	J-Link Software if using a Segger J-Link for this tutorial.
-	nRF Command-Line Tools
-	arm gcc compiler. Note: Make sure that the GCC Compiler is in your PATH
-	Lastly, you will need to download a CircuitPython release

Install JLink software
----------------------

download the .deb 64-bit Linux from: https://www.segger.com/downloads/jlink#J-LinkSoftwareAndDocumentationPack

https://www.segger.com/downloads/jlink/#J-LinkSoftwareAndDocumentationPack

```
mkdir -p ~/projects/hw/jlink
mkdir -p ~/projects/hw/jlink/downloads
cd ~/projects/hw/jlink
mv ~/Downloads/JLink_Linux_V700_x86_64.deb downloads/
sudo dpkg -i ./downloads/JLink_Linux_V700_x86_64.deb
```

Install nRF Command-Line Tools
------------------------------

https://www.nordicsemi.com/Software-and-tools/Development-Tools/nRF-Command-Line-Tools/Download#infotabs

To flash using Jlink, `nrfjprog` is used

The binary files provided by the manufacturer of the chip can be downloaded via the following page : https://www.nordicsemi.com/Software-and-tools/Development-Tools/nRF-Command-Line-Tools/Download#infotabs

[ Downloads ] tab - Select Linux64 in dropdown

choose `10.12.1 Linux64` as Selected version; use link to download `nRF-Command-Line-Tools_10_12_1_Linux-amd64.tar.gz`

Doc: https://infocenter.nordicsemi.com/topic/struct_nrf52/struct/nrf52.html

### extract and install

This is how I installed on my system. YMMV.

```
cd ~/projects/hw/jlink
mv ~/Downloads/nRF-Command-Line-Tools_10_12_1_Linux-amd64.tar.gz downloads/
mkdir -p tmp/
tar -xzvf downloads/nRF-Command-Line-Tools_10_12_1_Linux-amd64.tar.gz -C ./tmp/
dpkg --info     tmp/nRF-Command-Line-Tools_10_12_1_Linux-amd64.deb
dpkg --contents tmp/nRF-Command-Line-Tools_10_12_1_Linux-amd64.deb

(cd tmp && sudo apt install ./nRF-Command-Line-Tools_10_12_1_Linux-amd64.deb )

mergehex --version
nrfjprog --version
```

version info

```
mergehex version: 10.12.1
nrfjprog version: 10.12.1
JLinkARM.dll version: 7.00
```

Install ARM embedded toolchain
------------------------------

https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm

-	https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads

	-	`gcc-arm-none-eabi-10-2020-q4-major-x86_64-linux.tar.bz2` Linux x86_64 Tarball

can use the distribution version instead

```shell
sudo apt install gcc-arm-none-eabi libnewlib-arm-none-eabi build-essential
```

Download CircuitPython release
------------------------------

https://circuitpython.org/board/particle_xenon/

```
cd ~/projects/hw/jlink
mv ~/Downloads/adafruit-circuitpython-particle_xenon-en_US-6.2.0.uf2 downloads

```

Replacing the Default Bootloader
================================


Manually Burning the Bootloader via `nrfjprog` (using Jlink)
------------------------------------------------------------

https://docs.particle.io/tutorials/learn-more/xenon-circuit-python/#option-2-burning-a-new-bootloader-with-the-segger-j-link

Going with **Option 2**: Burning a new Bootloader with the Segger J-Link

##### Disable Mass Storage Support

If this is your first time using the Segger J-Link for burning a bootloader, you'll want to disable *Mass Storage support*.

-	Open a terminal window and run the JLinkExe command, which was created when you installed the J-Link tools.
-	The first thing this command will do is update the firmware on your device. Once done, you'll be dropped into a J-Link prompt.
-	Type `MSDDisable` and hit enter. Then type `exit` and hit enter.

**Note**: If you ever need to re-enable Mass Storage support, you can do so with the `MSDEnable` command.

#### Manually build Adafruit Bootloader

Burn via nrfjprog (using Jlink)

https://learn.adafruit.com/circuitpython-on-the-nrf52/build-flash-particle

Download (`git clone`) Adafruit nRF52840 Bootloader

```
cd ~/projects/hw/jlink/
git clone https://github.com/adafruit/Adafruit_nRF52_Bootloader
cd Adafruit_nRF52_Bootloader
git submodule update --init --recursive
```

To flash, requires JLink hooked up to Particle (SWD header).

Also, board needs to be powered, so connect Host computer USB to Particle board's micro-USB port.

Make sure you're still in the Adafruit_nRF52_Bootloader directory and run the following command to erase the underlying Nordic chip on the Xenon.

```shell
nrfjprog -f nrf52 --eraseall
```

from the instructions

```shell
cd ~/projects/hw/jlink/Adafruit_nRF52_Bootloader

pip3 install --user adafruit-nrfutil
pip3 install --user intelhex

#make BOARD=particle_xenon
#make BOARD=particle_xenon erase

make BOARD=particle_xenon clean
make BOARD=particle_xenon sd
# Flashing: lib/softdevice/s140_nrf52_6.1.1/s140_nrf52_6.1.1_softdevice.hex
# nrfjprog --program lib/softdevice/s140_nrf52_6.1.1/s140_nrf52_6.1.1_softdevice.hex -f nrf52 --sectorerase --reset

make BOARD=particle_xenon flash
# Flashing: particle_xenon_bootloader-0.5.0-dirty_nosd.hex
# nrfjprog --program _build/build-particle_xenon/particle_xenon_bootloader-0.5.0-dirty_nosd.hex --sectoranduicrerase -f nrf52 --reset
```

After this step the board should reset, and then the `XENONBOOT` drive should be mounted, letting you know it can accept a `.uf2` image now.

<details>

<summary>**Unsuccessful** Try using `Adafruit_nRF52_Bootloader` release without building from clone</summary>

The release `.hex` seems to include the softdevice in same image as bootloader, but it wasn't working right.

It was causing the drive to only be accessible by superuser, but the build-from-source way was not.


Can download latest bootloader directly from Adafruit [Adafruit_nRF52_Bootloader](https://github.com/adafruit/Adafruit_nRF52_Bootloader/releases) repo

```
cd ~/projects/hw/jlink
wget https://github.com/adafruit/Adafruit_nRF52_Bootloader/releases/download/0.5.0/particle_xenon_bootloader-0.5.0.zip
curl -o downloads/particle_xenon_bootloader-0.5.0.zip -L https://github.com/adafruit/Adafruit_nRF52_Bootloader/releases/download/0.5.0/particle_xenon_bootloader-0.5.0.zip

mkdir xenon_bootloader
unzip downloads/particle_xenon_bootloader-0.5.0.zip -d  xenon_bootloader
ls xenon_bootloader/
```

##### manual steps attempt

```shell
nrfjprog -f nrf52 --eraseall
```

At this point, the RGB LED on your Xenon will stop blinking, which is expected as the Particle Bootloader and Device OS has been removed.

Flash a new softdevice to the Xenon.

```shell
 nrfjprog --program MISSING.hex  -f nrf52 --chiperase --reset
```

The blue D7 LED will pulsate and the RGB LED will blink red and then turn green.

Burn a new bootloader with the flash command. This will take a few moments, and once done, the RGB LED will blink red and then turn Green. The blue D7 LED will remain on, and a new drive named `XENONBOOT` will show up on your computer.

```shell
nrfjprog --program ~/projects/hw/jlink/xenon_bootloader/particle_xenon_bootloader-0.5.0_s140_6.1.1.hex  -f nrf52 --chiperase --reset
```

</details>

### Install CircuitPython

Drag the CircuitPython `.uf2` onto `***ONBOOT` drive

```
cd ~/projects/hw/jlink/downloads
```

Drag circuitpython image onto the drive, in this case `adafruit-circuitpython-particle_xenon-en_US-6.2.0.uf2`

The board will reset when CircuitPython flash is complete, and will now show up as `CIRCUITPY` drive. `boot_out.txt` contents:

```console
$ cat '/media/dpc/CIRCUITPY/boot_out.txt'
Adafruit CircuitPython 6.2.0 on 2021-04-05; Particle Xenon with nRF52840
```

#### To get back to `***ONBOOT`

If you need to upgrade/change the CircuitPython image (not the nRF52 bootloader), the Particle board running the Adafruit nRF52 bootloader will need to be put into "USB bootloader mode". To get to USB bootloader mode, hold down the `MODE` button while clicking the `RESET` button. The board's RGB LED should turn green and the `***ONBOOT` drive will present itself/be mounted.

connect to serial console
-------------------------

```
ls /dev/ttyACM*
# then using screen
screen /dev/ttyACM0 115200
```

> Adafruit CircuitPython 6.2.0 on 2021-04-05; Particle Xenon with nRF52840

To end `screen` session: <kbd>C-a :quit</kbd>

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

-	<kbd>C-c</kbd> will enter REPL
-	<kbd>C-d</kbd> will enter REPL

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
