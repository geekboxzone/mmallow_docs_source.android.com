# Bluetooth #

Android's Bluetooth stack uses BlueZ for GAP, SDP, and RFCOMM profiles, and
is a SIG-qualified Bluetooth stack. 

Bluez is GPL licensed, so the Android framework interacts with userspace bluez code through D-BUS IPC to avoid proprietary code.

Headset and Handsfree (v1.5) profiles are implemented in the Android framework and are both tightly coupled with the Phone App. These profiles are also SIG qualified.

The diagram below offers a library-oriented view of the Bluetooth stack. Click [Bluetooth Process Diagram](bluetooth-process.html) for a process-oriented view.

<img src="/images/androidBluetooth.gif">

Solid elements represent Android blocks and dashed elements represent partner-specific blocks.

## Porting ##

BlueZ is Bluetooth 2.1 compatible and should work with any 2.1 chipset and is backward compatibile with older Bluetooth versions. There are two integration points:

- UART driver

- Bluetooth Power On/Off

### UART Driver ###

The BlueZ kernel sub-system attaches to your hardware-specific UART driver using the `hciattach` daemon.

For example, for MSM7201A, this is `drivers/serial/msm_serial.c`. You may also need to edit command line options to `hciattach` via `init.rc`.

### Bluetooth Power On/Off ###

The method for powering on and off your bluetooth chip varies from Android V 1.0 to post-1.0.

- 1.0: Android framework writes a 0 or 1 to `/sys/modules/board_[PLATFORM]/parameters/bluetooth_power_on`.

- Post-1.0: Android framework uses the linux `rfkill` API. See `arch/arm/mach-msm/board-trout-rfkill.c` for an example.

### Compiling ###

To compile Android with Bluetooth support enabled, add the following line to `BoardConfig.mk`.

    BOARD_HAVE_BLUETOOTH := true

### Troubleshooting ###

#### Debugging ####

To debug your bluetooth implementation, start by reading the logs (`adb logcat`) and look for ERRROR and WARNING messages regarding Bluetooth. Android uses BlueZ, which comes with some useful debugging tools. The snippet below provides examples in a suggested order:

    hciconfig -a  			# print BT chipset address and features. Useful to check if you can communicate with your BT chipset.
    hcidump -XVt  			# print live HCI UART traffic.
    hcitool scan  			# scan for local devices. Useful to check if RX/TX works.
    l2ping ADDRESS  		# ping another BT device. Useful to check if RX/TX works.
    sdptool records ADDRESS # request the SDP records of another BT device.

#### Daemon Logs ####

Deamon logs for `hcid` (`STDOUT`) and `hciattach` (`STDERR`) are sent to `/dev/null` by default. Edit `init.rc` and `init.PLATFORM.rc` to run these daemons under `logwrapper`, which redirects output to `logcat`.

#### hciconfig and hcitool ####

If you compile your own system.img for Android, and `hciconfig -a` works but `hcitool` scan doesn't, try installing the firmware for the Bluetooth chipset. This firmware isn't yet available in the open source codebase, but you can `adb pull` and then `adb push`it from a stock T-Mobile G1 (located in `/etc/firmware/brf6300.bin`).

## Tools ##

BlueZ provides a rich set of command line tools for debugging and interacting with the Bluetooth sub-system, including:

- `hciconfig`

- `hcitool`

- `hcidump`

- `sdptool`

- `dbus-send`

- `dbus-monitor`

## Feature Support ##

This section provides a change history of Bluetooth features added in each Android release and provides some rough guidance as to future features. 

### Android 1.0 release ###

Platform features:

- Based on Bluez 3.36 and Linux Kernel 2.6.25

- Bluetooth 2.0+EDR host stack

- Headset Profile 1.0 in Audio Gateway role

- Handsfree Profile 1.5 in Audio Gateway role

    - Three-way calling
    - Phonebook over AT commands
    
Qualifications:

- QDID B014524: Host stack (SDP, L2CAP, GAP, RFCOMM, SPP)

- QDID B014624: EPL for HTC Dream (HSP, HFP)

Example products:

- HTC Dream 

- T-Mobile G1

### Android 1.1 release ###

No Bluetooth changes since 1.0

### Android 1.5 release (cupcake) ###

Platform features:

- Based on Bluez 3.36 with Linux Kernel 2.6.27

- Bluetooth 2.0+EDR host stack
  
    - Support for auto-pairing with '0000' devices
  
- Headset Profile 1.1 in Audio Gateway role

- Handsfree Profile 1.5 in Audio Gateway role
  
    - Three-way calling    
    - Phonebook over AT commands    
    - Volume synchronization
    - eSCO
    - Extensive bug fixes and compatibility improvements
  
- Stereo Bluetooth (A2DP 1.2) in Source role
  
    - AVDTP 1.2 in Acceptor and Initiator roles
    - GAVDTP 1.0 in Acceptor and Initiator roles
    - 44.1 khz, stereo, software SBC codec
  
- Remote Control (AVRCP 1.0) in Target role
  
    - AVCTP 1.3 in Target role
    - play/pause/stop/prev/next
  
Qualifications:

- QDID B015261: Host stack (SDP, L2CAP, GAP, RFCOMM, SPP, AVCTP, AVRCP, GAVDP, AVDTP, A2DP)

- QDID B015262: EPL for HTC Sapphire (HSP, HFP)

### Android 2.0/2.1 release (eclair) ###

Platform features:

- Based on Bluez 4.47 with Linux Kernel 2.6.29

- Bluetooth 2.1+EDR host stack
  
    - Support for auto-pairing with '0000' devices
    - Support for Simple Secure Pairing
  
- Headset Profile 1.1 in Audio Gateway role

- Handsfree Profile 1.5 in Audio Gateway role
  
    - Three-way calling    
    - Phonebook over AT commands    
    - Volume synchronization
    - eSCO
    - Extensive bug fixes and compatibility improvements
  
- Stereo Bluetooth (A2DP 1.2) in Source role
  
    - AVDTP 1.2 in Acceptor and Initiator roles
    - GAVDTP 1.0 in Acceptor and Initiator roles
    - 44.1 khz, stereo, software SBC codec
  
- Remote Control (AVRCP 1.0) in Target role
  
    - AVCTP 1.3 in Target role
    - play/pause/stop/prev/next
  
-  Object Push Profile version 1.1 
  
     - Adds ability to transfer pictures, videos
     - Transfer of contacts using vCard is not supported in this release.
  
- Phone Book Address Profile version 1.0
  
    - Phone Book Server Equipment (PSE) role supported
  
- Using Java Bluetooth APIs, an Android application can peform the
  following:
  
   - Scan for other Bluetooth devices 
   - Query the local Bluetooth adapter for paired Bluetooth devices 
   - Establish RFCOMM channels 
   - Connect to other devices through service discovery 
   - Transfer data to and from other devices 
   - Manage multiple connections 
  
- Support for Bluetooth enabled car and desk docks
  
   - Framework support for routing Phone Call Audio and A2DP streaming using
   car and desk docks. 
  
### Android 2.2 release (froyo) ###

Platform features:

- Based on Bluez 4.47 with Linux Kernel 2.6.32

- No new profiles added.

- Added ability to share contacts using vCard

- Added ability to export all contacts - useful to transfer contacts to car kits 

- Improved compatibility with headsets and car kits. 

### Future releases ###

This section offers a rough guide of which features the team is developing for the next release. This feature list may change without notice. It isn't possible to post scheduling advice to the mailing lists.

- More profiles...

- Improved compatibility with headsets and car kits

- Bluetooth emulator support

- Bluetooth Low Energy 

### Development Notes ###

- **HID Support**: Cupcake features some early work--Bluez has an HID plugin, `external/bluez/utils/input/Android.mk`, which gets compiled. 

    You can interact directly with this plugin using `dbus-send` and `dbus-monitor`. While not officially supported, you should be able to connect and use a HID keyboard and mouse using the Bluez HID plugin API. Next steps include plumbing the plugin API in the Android Java framework and offering better support for HID input methods (new keymaps and mouse support).
  
- **Tethering - DUN and PAN Support**: Cupcake features some early work--Bluez has has DUN and PAN daemons which get compiled and  `BNEP` support is compiled into the kernel with cupcake. 

    While not officially supported, you should be able to run `dund` or `pand` daemons and, using `pppd` or `iptables`, test tethering support. Next steps include plubming the DBUS APIs to these daemons up into the Android Java framework and adding code to setup the network paths via `pppd` and/or `iptables`.
  
