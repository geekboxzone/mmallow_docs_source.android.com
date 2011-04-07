# Flashing #

To flash a device, you will need to use `fastboot`. Place the device in fastboot mode either manually by holding the appropriate key combination at boot, or from the shell with

    $ adb reboot bootloader

Once the device is in fastboot mode, run 

    $ fastboot flashall -w

The `-w` option wipes the `/data` partition on the device; this is useful for your first time flashing a particular device, but is otherwise unnecessary.

# Emulating #

To run the emulator, type

    $ emulator


