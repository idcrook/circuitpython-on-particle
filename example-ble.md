Trying to get Bluetooth LE up
=============================

[`adafruit_ble`](https://github.com/adafruit/Adafruit_CircuitPython_BLE) is in the [CP Libraries](https://circuitpython.org/libraries) collection

Download the recent library and extract from zip.

```shell
cd ~/projects/hw/circuitpython-on-particle
curl -o downloads/adafruit-circuitpython-bundle-6.x-mpy-20210414.zip -L https://github.com/adafruit/Adafruit_CircuitPython_Bundle/releases/download/20210414/adafruit-circuitpython-bundle-6.x-mpy-20210414.zip
cd downloads/
unzip adafruit-circuitpython-bundle-6.x-mpy-20210414.zip
```

Simple Beacon Code
------------------

Add two libraries to board

-	adafruit_ble
-	adafruit_ble_eddystone

Example eddystone beacon
------------------------

 Needed: A mobile device running an Eddystone beacon discovery app, such as Physical Web:

-	[Physical Web for iOS](https://apps.apple.com/us/app/physical-web/id927653608)
-	[Physical Web for Android](https://play.google.com/store/apps/details?id=com.physicalweb&hl=en_US)

From https://learn.adafruit.com/circuitpython-ble-advertising-beacons/simple-beacon-code-in-circuitpython

```python
# SPDX-FileCopyrightText: 2021 ladyada for Adafruit Industries
# SPDX-License-Identifier: MIT

"""This example broadcasts our Mac Address as our Eddystone ID and a link to the Adafruit Discord
   server."""

import time

import adafruit_ble
from adafruit_ble_eddystone import uid, url

radio = adafruit_ble.BLERadio()

# Reuse the BLE address as our Eddystone instance id.
eddystone_uid = uid.EddystoneUID(radio.address_bytes)
eddystone_url = url.EddystoneURL("https://adafru.it/discord")

while True:
    # Alternate between advertising our ID and our URL.
    radio.start_advertising(eddystone_uid)
    time.sleep(0.5)
    radio.stop_advertising()

    radio.start_advertising(eddystone_url)
    time.sleep(0.5)
    radio.stop_advertising()

    time.sleep(4)
```
