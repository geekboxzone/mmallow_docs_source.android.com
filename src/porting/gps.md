# GPS #

Android defines a user space C abstraction interface for GPS hardware. The interface header is defined in `include/hardware/gps.h`. In order to integate GPS with Android, you need to build a shared library that implements this interface. 

## Building a GPS Library ##

To implement a GPS driver, create a shared library that implements the interface defined in `gps.h`. You must name your shared library `libgps.so` so that it will get loaded from `/system/lib` at runtime. Place GPS sources and Android.mk in `vendor/NAME/CHIPSET_OR_BOARD/gps/` (where "name" is your organization name and "chipset or board" is your hardware target).

The following stub `Android.mk` file ensures that `libgps` compiles and links to the appropriate libraries:

    LOCAL_PATH := $(call my-dir)
    include $(CLEAR_VARS)

    LOCAL_MODULE := libgps

    LOCAL_STATIC_LIBRARIES:= \
    # include any static library dependencies

    LOCAL_SHARED_LIBRARIES := \
    # include any shared library dependencies

    LOCAL_SRC_FILES += \
    # include your source files.  eg. MyGpsLibrary.cpp

    LOCAL_CFLAGS += \
    # include any needed compile flags

    LOCAL_C_INCLUDES:= \
    # include any needed local header files

    include $(BUILD_SHARED_LIBRARY)

Doxygen content is unavailable at the moment as source.android.com undergoes an overhaul. Sorry for the inconvenience!
