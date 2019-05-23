Build CircuitPython for Particle Argon
======================================

pre-reqs
--------

https://learn.adafruit.com/building-circuitpython/linux

```
sudo apt update
# Try running `make`. If it's not installed, do:
# sudo apt install build-essential
sudo apt install git
sudo apt install gettext
pip3 install --user huffman
sudo add-apt-repository ppa:team-gcc-arm-embedded/ppa
# If you don't have add-apt-repository, do:
# sudo apt install add-apt-repository
sudo apt update
sudo apt install gcc-arm-embedded
```

Fetch the code to build
-----------------------

```
cd ~/projects
git clone https://github.com/adafruit/circuitpython.git
cd circuitpython
git submodule sync
git submodule update --init --recursive
```

Build `mpy-cross`
-----------------

```
make -C mpy-cross
```

Build CircuitPython
-------------------

board are in the `ports` dir, e.g.

`ls ports/nrf/boards/particle_argon`

```
sudo apt install python3-pip python3-setuptools python3-wheel
pip3 install --user --upgrade adafruit-nrfutil

#
cd  ports/nrf
# no longer needed - in tree # ./bluetooth/download_ble_stack.sh
make BOARD=particle_argon
```

when done

```
ls build-particle_argon/*.uf2
```

Version string when installed
-----------------------------

This gets put into `boot_out.txt` and also will be printed

```
Adafruit CircuitPython 4.0.1-5-g63b253c33 on 2019-05-22; Particle Argon with nRF52840
```

The version string is telling us this build is five git commits beyond the `4.0.1` tag (release)
