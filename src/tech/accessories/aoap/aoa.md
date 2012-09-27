# Android Open Accessory Protocol 1.0 #

An Android USB accessory must adhere to Android Accessory Protocol, which defines how
an accessory detects and sets up communication with an Android-powered device. In general, an
accessory should carry out the following steps:

- Wait for and detect connected devices
- Determine the device's accessory mode support
- Attempt to start the device in accessory mode if needed
- Establish communication with the device if it supports the Android accessory protocol

The following sections explain how to implement these steps.

## Wait for and Detect Connected Devices ##

Your accessory should have logic to continuously check for connected Android-powered devices.
When a device is connected, your accessory should determine if the device supports accessory mode.


## Determine Accessory Mode Support ##

When an Android-powered device is connected, it can be in one of three states:

- The attached device supports Android accessory mode and is already in accessory mode.
- The attached device supports Android accessory mode, but it is not in accessory mode.
- The attached device does not support Android accessory mode.

During the initial connection, the accessory should check the vendor and product IDs of the
connected device's USB device descriptor. The vendor ID should match Google's ID (`0x18D1`) and the
product ID should be `0x2D00` or `0x2D01` if the device is already in accessory mode (case A). If
so, the accessory can now
[establish communication with the device](#establish-communication-with-the-device) through
bulk transfer endpoints with its own communication protocol. There is no need to start the device
in accessory mode.

**Note:** `0x2D00` is reserved for Android-powered devices that
support accessory mode. `0x2D01` is reserved for devices that support accessory mode as well as the
ADB (Android Debug Bridge) protocol, which exposes a second interface with two bulk endpoints for
ADB. You can use these endpoints for debugging the accessory application if you are simulating
the accessory on a computer. In general, do not use this interface unless your accessory is
implementing a passthrough to ADB on the device.

If the vendor and product ID do not match, there is no way to distinguish between states b and c, so
the accessory [attempts to start the device in accessory mode](#attempt-to-start-in-accessory-mode)
to determine if the device is supported.


## Attempt to Start in Accessory Mode ##

If the vendor and product IDs do not correspond to an Android-powered device in accessory
mode, the accessory cannot discern whether the device supports accessory mode and is not in that
state, or if the device does not support accessory mode at all. This is because devices that
support accessory mode but aren't in it initially report the device's manufacturer vendor ID and
product ID, and not the special Android Open Accessory ones. In either case, the accessory should
try to start the device into accessory mode to figure out if the device supports it. The following
steps explain how to do this:

<ul>
  <li>Send a 51 control request ("Get Protocol") to figure out if the device supports the Android
  accessory protocol. A non-zero number is returned if the protocol is supported, which
  represents the version of the protocol that the device supports (currently, only version 1
  exists). This request is a control request on endpoint 0 with the following characteristics:

<pre>
requestType:    USB_DIR_IN | USB_TYPE_VENDOR
request:        51
value:          0
index:          0
data:           protocol version number (16 bits little endian sent from the
                device to the accessory)
</pre>
  </li>
  <li>If the device returns a proper protocol version, send identifying string information to the
  device. This information allows the device to figure out an appropriate application for this
  accessory and also present the user with a URL if an appropriate application does not exist.
  These requests are control requests on endpoint 0 (for each string ID) with the following
  characteristics:

<pre>
requestType:    USB_DIR_OUT | USB_TYPE_VENDOR
request:        52
value:          0
index:          string ID
data            zero terminated UTF8 string sent from accessory to device
</pre>

  <p>The following string IDs are supported, with a maximum size of 256 bytes for each string
  (must be zero terminated with `\0`).</p>

<pre>
manufacturer name:  0
model name:         1
description:        2
version:            3
URI:                4
serial number:      5
</pre>
  </li>
  <li>When the identifying strings are sent, request the device start up in accessory mode. This
  request is a control request on endpoint 0 with the following characteristics:

<pre>
requestType:    USB_DIR_OUT | USB_TYPE_VENDOR
request:        53
value:          0
index:          0
data:           none
</pre>
  </li>
</ul>

After sending the final control request, the connected USB device should re-introduce itself
on the bus in accessory mode and the accessory can re-enumerate the connected devices. The
algorithm jumps back to
[determining the device's accessory mode support](#determine-accessory-mode-support)
to check for the vendor and product ID. The vendor ID and product ID of the device will be
different if the device successfully switched to accessory mode and will now correspond to
Google's vendor and product IDs instead of the device manufacturer's IDs. The accessory can now
[establish communication with the device](#establish-communication-with-the-device).

If at any point these steps fail, the device does not support Android accessory mode and the
accessory should wait for the next device to be connected.


## Establish Communication with the Device ##

If an Android-powered device in accessory mode is detected, the accessory can query the
device's interface and endpoint descriptors to obtain the bulk endpoints to communicate with the
device. An Android-powered device that has a product ID of `0x2D00` has one interface with two bulk
endpoints for input and output communication. A device with product ID of `0x2D01` has two
interfaces with two bulk endpoints each for input and output communication. The first interface
is for standard communication while the second interface is for ADB communication. To communicate
on an interface, all you need to do is find the first bulk input and output endpoints, set the
device's configuration to a value of 1 with a `SET_CONFIGURATION` (`0x09`) device request, then
communicate using the endpoints.