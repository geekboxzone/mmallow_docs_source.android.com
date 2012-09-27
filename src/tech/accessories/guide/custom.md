# Building Custom Accessories #

An accessory for Android can be anything: keyboard, thermometer, robot, lighting control or
anything else you can imagine. Accessories for Android all have one thing in common; they all
connect to an Android device in some way. When starting out to build an accessory, you should
decide how your accessory will connect to Android devices. This page gives you quick overview of
your options for connecting your Android accessory and resources to help you get started.


## Connecting over USB ##

An accessory that connects to an Android device through a USB cable must support the Android
Open Accessory (AOA) protocol, which specifies how an accessory can establish communication with
an Android device over a USB cable. Due to the low power output of Android devices, the AOA
protocol requires the accessory act as a USB host, which means that the connecting accessory must
power the bus.

The AOA protocol has two versions which support different types of communication. Version
1.0 supports a generic accessory communication and adb debugging. This version of the protocol is
supported by the platform in Android 3.1 (API Level 12) and higher, and supported through an
[Add-On Library](https://developers.google.com/android/add-ons/google-apis/) in Android
2.3.4 (API Level 10) and higher. Version 2.0 of the protocol is available in Android 4.1 (API Level
16) and adds audio streaming and human interface device (HID) capabilities.

If you use the general accessory protocol to communicate with your accessory (rather than the
adb or audio protocol), you must provide an Android application that can detect the connection of
your USB accessory and establish communication.


### Next steps ###

To get started on building an Android accessory that uses a USB connection:

- Select a hardware platform or build a hardware device that can support USB host mode.
- Review the [AOA protocol](/tech/accessories/aoap/index.html) specifications to understand
  how to implement this protocol on your accessory hardware. Implementing the
  [AOA 2.0 protocol](/tech/accessories/aoap/aoa2.html) is recommended for all new Android USB
  accessories.
- Review the ADK 2012 [firmware source code](http://developer.android.com/tools/adk/adk2.html#src-download)
  (`<adk-src>/adk2012/board/library/ADK2/`), which demonstrates an implementation of an accessory
  using a USB connection for general data communications and audio streaming.
- If you are planning to build an Android application that communicates with your accessory
  via USB, review the ADK 2012 Android
  [application source code](http://developer.android.com/tools/adk/adk2.html#src-download)
  (`<adk-src>/adk2012/app/`).


## Connecting over Bluetooth ##

An accessory that connects with Android devices over a Bluetooth connection can use the
various connection profiles supported by Android, including the Simple Serial Protocol (SSP) and
Advanced Audio Distribution Profile (A2DP) profile. An accessory that uses Bluetooth to connect to
Android devices must support Bluetooth communications and at least one of the supported connection
profiles.

Users must enable Bluetooth on their Android device and pair with your accessory in order to
use it. You can also provide a secondary Android application that handles any specialized
communication, such as data input or control outputs, to interface with your accessory.


### Next steps ###

To get started on building an Android accessory that uses a Bluetooth connection:

- Select a hardware platform or build an hardware device that can support Bluetooth
  communications and an Android supported connection profile, such as SSP or A2DP.
- Review the ADK 2012 [firmware source code](http://developer.android.com/tools/adk/adk2.html#src-download)
  (`<adk-src>/adk2012/board/library/ADK2/`), which includes an example implementation
  of general data communications and audio streaming using a Bluetooth connection.
- If you are planning to build an Android application that communicates with your accessory
  via Bluetooth, review the ADK 2012 Android
  [application source code](http://developer.android.com/tools/adk/adk2.html#src-download)
  (`<adk-src>/adk2012/app/`).

**Note:** The ADK 2012 source code includes an open source Bluetooth stack which
is built for the Texas Instruments CC2564 chip, but can work with any Bluetooth chip that
supports a standard Host/Controller Interface (HCI).