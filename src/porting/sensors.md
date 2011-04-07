# Sensors #

Android defines a user space C abstraction interface for sensor hardware. The interface header is defined in 
`hardware/libhardware/include/hardware/sensors.h`.
In order to integrate sensors with Android you need to build a shared library that implements this interface. 

The types of sensors currently supported by Android include:

- Accelerometer
- Magnetic Field
- Orientation
- Gyroscope
- Light
- Pressure
- Temperature
- Proximity
 
## Building a Sensor Library ##

To implement a Sensors driver, create a shared library that implements the interface defined in `sensors.h`. You must name your shared library 
`libsensors.so` so that it will get loaded from `/system/lib` at runtime. 

The following stub file, `Android.mk`, ensures that `libsensors` compiles and links to the appropriate libraries:

    LOCAL_PATH := $(call my-dir)
    include $(CLEAR_VARS)

    LOCAL_MODULE := sensors

    LOCAL_PRELINK_MODULE := false

    LOCAL_MODULE_PATH := $(TARGET_OUT_SHARED_LIBRARIES)/hw

    LOCAL_SHARED_LIBRARIES := liblog
    # include any shared library dependencies

    LOCAL_SRC_FILES := sensors.c

    include $(BUILD_SHARED_LIBRARY)
 
Doxygen content is unavailable at the moment as source.android.com is deconstructed and then reconstructed. Sorry for the inconvenience!
