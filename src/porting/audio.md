# Audio #

AudioHardwareInterface serves as the glue between proprietary audio drivers and the Android AudioFlinger service, the core audio service that handles all audio-related requests from applications.

<img src="/images/android_audio_architecture.gif">

Solid elements represent Android blocks and dashed elements represent partner-specific blocks.

## Building an Audio Library ##

To implement an audio driver, create a shared library that implements the interface defined in `AudioHardwareInterface.h`. You must name your shared library `libaudio.so` so that it will get loaded from `/system/lib` at runtime.  Place libaudio sources and `Android.mk` in `vendor/acme/chipset_or_board/libaudio/`.

The following stub `Android.mk` file ensures that `libaudio` compiles and links to the appropriate libraries:

    LOCAL_PATH := $(call my-dir)
    include $(CLEAR_VARS)

    LOCAL_MODULE := libaudio

    LOCAL_SHARED_LIBRARIES := \
        libcutils \
        libutils \
        libmedia \
        libhardware

    LOCAL_SRC_FILES += MyAudioHardware.cpp

    LOCAL_CFLAGS +=

    LOCAL_C_INCLUDES +=

    LOCAL_STATIC_LIBRARIES += libaudiointerface

    include $(BUILD_SHARED_LIBRARY)

Doxygen content is unavailable at the moment as source.android.com is reverse engineered and then engineered again in the forward direction. Sorry for the inconvenience!
