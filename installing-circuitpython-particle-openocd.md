Installing CircuitPython on the nrf52840-based Particle Argon
=============================================================

2021-04-14: Update to include Xenon based on particle's own tutorial:

-	https://docs.particle.io/tutorials/learn-more/xenon-circuit-python/

Originally based on https://learn.adafruit.com/circuitpython-on-the-nrf52/overview

-	https://circuitpython.org/downloads
	-	-> https://circuitpython.org/board/particle_argon/ downloaded version `4.0.1` `.uf2`

In addition, you'll need to install the following software:

-	J-Link Software if using a Segger J-Link for this tutorial.
-	nRF Command-Line Tools
-	arm gcc compiler. Note: Make sure that the GCC Compiler is in your PATH
-	OpenOCD unless you've already installed Particle Workbench, in which case OpenOCD will have been installed for you.
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

		```
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

#### Can download latest bootloader

directly from Adafruit [Adafruit_nRF52_Bootloader](https://github.com/adafruit/Adafruit_nRF52_Bootloader/releases) repo

```
cd ~/projects/hw/jlink
wget https://github.com/adafruit/Adafruit_nRF52_Bootloader/releases/download/0.5.0/particle_xenon_bootloader-0.5.0.zip
curl -o downloads/particle_xenon_bootloader-0.5.0.zip -L https://github.com/adafruit/Adafruit_nRF52_Bootloader/releases/download/0.5.0/particle_xenon_bootloader-0.5.0.zip

mkdir xenon_bootloader
unzip downloads/particle_xenon_bootloader-0.5.0.zip -d  xenon_bootloader
ls xenon_bootloader/
```


Install OpenOCD
---------------

Note: This distribution one will not work, since it doesn't have the `target/nrf52-particle.cfg`

```shell
sudo apt install openocd
```

Since I use *Visual Studio Code* can install "Particle Workbench" from https://marketplace.visualstudio.com/items?itemName=particle.particle-vscode-pack

<kbd>C-S-p</kbd> in VS Code, then:

```
ext install particle.particle-vscode-pack
```

Will install openocd in `~/.particle/toolchains/openocd/0.11.2-adhoc6ea4372.0` once you install a particle toolchain.



### Flash using OpenOCD

**IMPORTANT**: Didn't have a supported DAP probe available, nor was `jlink` supported in the particle toolchain openocd version.

See other file for jlink-based method which builds a bootloader from source relying on  `Adafruit_nRF52_Bootloader` repo

Next, we'll use OpenOCD to burn this bootloader hex file to your Xenon.

```
First, put your Xenon into DFU Mode by holding down the MODE and RESET buttons. Then, let go of RESET, but continue to hold down the MODE button until the RGB LED on the device is blinking yellow.

Connect the Particle Debugger to the debug port on your Particle device using the provided ribbon cable, and plug both into your computer. The debug port consists of ten exposed pins protected by some plastic, and you can find it next to the LiPo battery port near the top of the device.
```

```shell
openocd -f interface/cmsis-dap.cfg -f target/nrf52-particle.cfg \
    -c "adapter_khz 1000" \
    -c "init" \
    -c "flash list" \
    -c "exit"
```

```
embedded:startup.tcl:26: Error: Can't find target/nrf52-particle.cfg
```

```shell
cd ~/.particle/toolchains/openocd/0.11.2-adhoc6ea4372.0
bin/openocd -f interface/jlink.cfg -f target/nrf52-particle.cfg \
    -c "adapter_khz 1000" \
    -c "init" \
    -c "flash list" \
    -c "exit"
```



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
