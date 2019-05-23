from logs
---------

```
make BOARD=particle_argon clean

########################################################################

make BOARD=particle_argon
CC main.c
CC boards.c
CC flash_nrf5x.c
CC dfu_ble_svc.c
CC dfu_init.c
CC nrfx_power.c
CC nrf_nvmc.c
CC bootloader.c
CC bootloader_settings.c
CC bootloader_util.c
CC dfu_transport_serial.c
CC dfu_transport_ble.c
CC dfu_single_bank.c
CC pstorage_raw.c
CC ble_dfu.c
CC ble_dis.c
CC app_timer.c
CC app_scheduler.c
CC app_error.c
CC app_util_platform.c
CC crc16.c
CC hci_mem_pool.c
CC hci_slip.c
CC hci_transport.c
CC nrf_assert.c
CC usb_desc.c
CC usb.c
CC msc_uf2.c
CC ghostfat.c
CC system_nrf52840.c
CC dcd_nrf5x.c
CC hal_nrf5x.c
CC tusb_fifo.c
CC usbd.c
CC usbd_control.c
CC cdc_device.c
CC msc_device.c
CC custom_device.c
CC tusb.c
AS gcc_startup_nrf52840.S
LD particle_argon_bootloader-0.2.11-3-gfb847b9-nosd.out

   text	   data	    bss	    dec	    hex	filename
  28624	    348	  22190	  51162	   c7da	_build-particle_argon/particle_argon_bootloader-0.2.11-3-gfb847b9-nosd.out

########################################################################
make BOARD=particle_argon erase
Erasing chip
nrfjprog --eraseall -f nrf52
Erasing user available code and UICR flash areas.
Applying system reset.

########################################################################
make BOARD=particle_argon sd
Flashing: lib/softdevice/s140_nrf52_6.1.1/s140_nrf52_6.1.1_softdevice.hex
nrfjprog --program lib/softdevice/s140_nrf52_6.1.1/s140_nrf52_6.1.1_softdevice.hex -f nrf52 --chiperase  --reset
Parsing hex file.
Erasing user available code and UICR flash areas.
Applying system reset.
Checking that the area to write is not protected.
Programming device.
Applying system reset.
Run.

########################################################################

make BOARD=particle_argon flash
CR particle_argon_bootloader-0.2.11-3-gfb847b9-nosd.hex
Flashing: _build-particle_argon/particle_argon_bootloader-0.2.11-3-gfb847b9-nosd.hex
nrfjprog --program _build-particle_argon/particle_argon_bootloader-0.2.11-3-gfb847b9-nosd.hex --sectoranduicrerase -f nrf52 --reset
Parsing hex file.
Erasing page at address 0xF4000.
Erasing page at address 0xF5000.
Erasing page at address 0xF6000.
Erasing page at address 0xF7000.
Erasing page at address 0xF8000.
Erasing page at address 0xF9000.
Erasing page at address 0xFA000.
Erasing page at address 0xFB000.
Erasing UICR flash area.
Applying system reset.
Checking that the area to write is not protected.
Programming device.
Applying system reset.
Run.
```
