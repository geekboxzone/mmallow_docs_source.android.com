# Camera #

Android's camera subsystem connects the camera application to the application framework and user space libraries, which in turn communicate with the camera hardware layer that operates the physical camera.

The diagram below illustrates the structure of the camera subsystem.

<img src="/images/camera_video2.gif">

## Building a Camera Library ##

To implement a camera driver, create a shared library that implements the interface defined in `CameraHardwareInterface.h`. You must name your shared library `libcamera.so` so that it will get loaded from `/system/lib` at runtime.  Place libcamera sources and `Android.mk` in `vendor/acme/chipset_or_board/libcamera/`.

The following stub `Android.mk` file ensures that `libcamera` compiles and links to the appropriate libraries:

    LOCAL_PATH := $(call my-dir)
    include $(CLEAR_VARS)

    LOCAL_MODULE := libcamera

    LOCAL_SHARED_LIBRARIES := \
        libutils \
        librpc \
        liblog

    LOCAL_SRC_FILES += MyCameraHardware.cpp

    LOCAL_CFLAGS +=

    LOCAL_C_INCLUDES +=

    LOCAL_STATIC_LIBRARIES += \
        libcamera-common \
        libclock-rpc \
        libcommondefs-rpc

    include $(BUILD_SHARED_LIBRARY)

## Sequence Diagrams ##

### Preview ###

The following diagram illustrates the sequence of function calls and actions necessary for your camera to preview.

<img src="/images/cameraPreview.jpg">

### Taking a Picture ###

The following diagram illustrates the sequence of function calls and actions necessary for your camera to take a picture.

<img src="/images/cameraTakePicture.jpg">

Doxygen content is unavailable at the moment as source.android.com is downsized for an upgrade. Sorry for the inconvenience!
