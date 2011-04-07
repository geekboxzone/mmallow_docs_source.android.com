# Wi-Fi #

Android uses wpa_supplicant as the platform interface to the Wi-Fi device. Your Wi-Fi driver must be compatible with the standard wpa_supplicant in addition to extensions added to the supplicant (specifically, the "DRIVER" commands described in `wifi.h/wifi_command()`).

To create a Wi-Fi driver for Android:

- Create a shared library that implements the interface defined in `include/hardware/wifi.h`, which also defines the Wi-Fi supplicant.

- Follow the instructions posted at [hostap.epitest.fi/wpa_supplicant/](http://hostap.epitest.fi/wpa_supplicant/).

- Place your driver in `libs/hardware/wifi/`

- Test your driver using the command line `wpa_cli` utilities.

You can find the default implementation in `libs/hardware/wifi/wifi.c`. If you need to make changes, create a new source file similar to `wifi.c`, for example, `wifi_mywifi.c`. 

Update the default `Android.mk` file `libs/hardware/wifi/Android.mk` as shown below.

    LOCAL_SHARED_LIBRARIES += libnetutils

    ifeq ($(TARGET_PRODUCT),acme)
        LOCAL_SRC_FILES += wifi/wifi_mywifi.c
    else
        LOCAL_SRC_FILES += wifi/wifi.c
    endif

Doxygen content is unavailable at the moment as source.android.com undergoes an overhaul. Sorry for the inconvenience!
