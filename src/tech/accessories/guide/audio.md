# Building Audio Accessories #

In building an audio accessory, such as an audio dock or other playback device, you should
consider how your accessory will connect with Android devices. In particular, you should decide
if your accessory will use Universal Serial Bus (USB) or a Bluetooth connection to stream music or
other audio content.


## Audio over USB ##

An accessory that connects with Android over USB connection must use the Android Open
Accessory (AOA) protocol version 2.0. This version of the AOA protocol is supported on Android 4.1
(API Level 16) and higher. Once an Android device connects to an accessory that supports this
protocol, the Android system treats it as a standard audio output device and routes all audio to
that accessory. No secondary software application is required on the Android device.

**Note:** Due to the low power output of Android devices, the Android Open Accessory
Protocol requires that accessories act as a USB host, which means that the connecting accessory
must power the bus.


### Next steps ###

To get started on building an audio accessory that uses a USB connection:

- Select a hardware platform or build a hardware device that can support USB host mode.
- Review the [AOA 2.0 protocol](/tech/accessories/aoap/aoa2.html) specification to understand
  how to implement this protocol on your accessory hardware.
- Review the ADK 2012 [firmware source code](http://developer.android.com/tools/adk/adk2.html#src-download)
  (`<adk-src>/adk2012/board/library/ADK2/`), which includes an example implementation
  of an audio playback accessory using a USB connection.

**Note:** The AOA 2.0 protocol also supports the
[human interface device](/tech/accessories/aoap/aoa2.html#hid-support) (HID) protocol through a USB
connection, enabling accessories such as audio docks to provide hardware play back controls such
as pause, fast-forward or volume buttons.


## Audio over Bluetooth ##

An accessory that connects with Android over Bluetooth can use an Advanced Audio Distribution
Profile (A2DP) connection stream music for playback. Playing audio over a Bluetooth with A2DP is
supported on Android 1.5 (API Level 3) and higher. An Android user can connect to an accessory
that supports this profile using the system Settings > Bluetooth and play music directly to the
accessory without the need for a secondary application.

**Note:** If you want to provide a custom application for output to your audio
accessory, note that the Android 3.0 (API Level 11) allows applications to operate an A2DP
connection using the
[`BluetoothA2dp`](http://developer.android.com/reference/android/bluetooth/BluetoothA2dp.html)
class.


### Next steps ###

To get started on building an audio accessory that uses a Bluetooth connection:

- Select a hardware platform or build an hardware device that can support Bluetooth
  communications and the A2DP connection profile.
- Review the ADK 2012
  [firmware source code](http://developer.android.com/tools/adk/adk2.html#src-download)
  (`<adk-src>/adk2012/board/library/ADK2/`), which includes an example implementation
  of an audio playback accessory using a Bluetooth connection.

**Note:** The ADK 2012 source code includes an open source Bluetooth stack that
is built for the Texas Instruments CC2564 chip, but can work with any Bluetooth chip that
implements a standard Host/Controller Interface (HCI).