# Build Accessories for Android #

Android Open Accessory support allows external USB hardware (an Android USB accessory) to interact
with an Android-powered device in a special accessory mode. When an Android-powered powered device
is in accessory mode, the connected accessory acts as the USB host (powers the bus and enumerates
devices) and the Android-powered device acts in the USB accessory role. Android USB accessories are
specifically designed to attach to Android-powered devices and adhere to the Android Open Accessory
Protocol, that allows them to detect Android-powered devices that support
accessory mode. Accessories must also provide 500mA at 5V for charging power. Many previously
released Android-powered devices are only capable of acting as a USB device and cannot initiate
connections with external USB devices. Android Open Accessory support overcomes this limitation
and allows you to build accessories that can interact with an assortment of Android-powered
devices by allowing the accessory to initiate the connection.

**Note:** Accessory mode is ultimately dependent on the device's hardware and not all devices
support accessory mode. Devices that support accessory mode can be filtered using a `<uses-feature>`
element in your corresponding application's Android manifest. For more information, see the
[USB Accessory](http://developer.android.com/guide/topics/connectivity/usb/accessory.html#manifest)
developer guide.

Android Open Accessory support is included in Android 3.1 (API Level 12) and higher, and supported
through an [Add-On Library](https://developers.google.com/android/add-ons/google-apis/) in Android
2.3.4 (API Level 10) and higher.


## Audio Accessories ##

Android 4.1 and higher has support for audio output over a USB connection or Bluetooth. Find out
how to build audio docks and other plug-in audio output hardware for Android.

[&raquo; Build Audio Accessories](/tech/accessories/guide/audio.html)


## Custom Accessories ##

What do you want to connect to your Android device? Alarm clock? Keyboard? Thermostat? Robot?
Learn how to connect existing equipment or your own unique hardware to Android.

[&raquo; Build Custom Accessories](/tech/accessories/guide/custom.html)