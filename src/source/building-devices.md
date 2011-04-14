<!--
   Copyright 2010 The Android Open Source Project

   Licensed under the Apache License, Version 2.0 (the "License");
   you may not use this file except in compliance with the License.
   You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License.
-->

# Building for devices #

This page complements the main page about [Building](building.html) with information
that is specific to individual devices.

The only supported device is Nexus S, a.k.a. "crespo". Nexus S is the recommended
device to use with the Android Open-Source Project. Nexus One a.k.a. "passion" is
experimentatal and unsupported. Android Developer Phones (ADP1 and ADP2, a.k.a. "dream"
and "sapphire") are obsolete, were experimental and unsupported in froyo, and can't
be used with gingerbread.

## Building fastboot and adb ##

If you don't already have those tools, fastboot and adb can be built with
the regular build system. Follow the instructions on the page about
[building](building.html), and replace the main `make` command with

    $ make fastboot adb

## Booting into fastboot mode ##

During a cold boot, the following key combinations can be used to boot into fastboot mode,
which is a mode in the bootloader that can be used to flash the devices:

Device   | Keys
---------|------
crespo   | Press and hold *Volume Up*, then press and hold *Power*
passion  | Press and hold the trackball, then press *Power*
sapphire | Press and hold *Back*, then press *Power*
dream    | Press and hold *Back*, then press *Power*

Also, on devices running froyo or later where adb is enabled,
the command `adb reboot bootloader` can be used to reboot from
Android directly into the bootloader with no key combinations.

## Unlocking the bootloader ##

It's only possible to flash a custom system if the bootloader allows it.

This is the default setup on ADP1 and ADP2.

On Nexus One and Nexus S, the bootloader is locked by default. With the
device in fastboot mode, the bootloader is unlocked with

    $ fastboot oem unlock

The procedure must be confirmed on-screen, and deletes the user data for
privacy reasons. It only needs to be run once.

On Nexus One, the operation voids the warranty and is irreversible.

On Nexus S, the bootloader can be locked back with

    $ fastboot oem lock

## Obtaining proprietary drivers ##

While it's possible to build and run a system using exclusively source code
from Android Open-Source Project, such a system can only use the devices'
hardware capabilities for which Open-Source drivers exist.

Official drivers for Nexus S can be downloaded from
[Google's Nexus driver page](http://code.google.com/android/nexus/), which
add access to additional hardware capabilities with non-Open-Source drivers.

There are no official drivers for Nexus One, ADP2 or ADP1.

Each driver comes as a self-extracting script in a compressed archive.
After uncompressing each archive, run the included self-extracting script
from the root of the source tree, confirm that you agree to the terms of the
enclosed license agreement, and the drivers and their matching makefiles
will get installed in the `vendor/` hierarchy of the source tree.

In order to make sure that the newly installed drivers are properly
taken into account after being extracted, the existing output of any previous
build needs to be deleted with

    $ make clobber

## Picking and building the configuration that matches a device ##

The steps to configure and build the Android Open-Source Project
are described in the page about [Building](building.html).

The recommended builds for the various devices are available through
the lunch menu, accesed when running the `lunch` command with no arguments:

Device   | Build configuration
---------|---------------------
crespo   | full_crespo-userdebug
passion  | full_passion-userdebug
sapphire | full_sapphire-userdebug
dream    | full_dream-userdebug

## Flashing a device ##

Set the device in fastboot mode if necessary (see above).

Because user data is typically incompatible between builds of Android,
it's typically better to delete it when flashing a new system.

    $ fastboot erase cache
    $ fastboot erase userdata

An entire Android system can be flashed in a single command: this writes
teh boot, recovery and system partitions together after verifying that the
system bring flashed is compatible with the installed bootloader and radio,
and reboots the system.

    $ fastboot flashall

On crespo, sapphire and dream (but not on passion), the commands above can
be replaced with a single command

    $ fastboot -w flashall

### Nexus S Bootloader and Cell Radio compatibility ###

On Nexus S, each version of Android has only been thoroughly tested with
on specific version of the underlying bootloader and cell radio software.
However, no compatibility issues are expected when running newer systems
with older bootloaders and radio images according to the following table:

Android Version | Preferred Bootloader | Preferred Radio | Also possible
----------------|----------------------|-----------------|--------------
2.3 to 2.3.2    | I9020XXJK1           | I9020XXJK8
2.3.3           | I9020XXKA3           | I9020XXKB1      | versions from 2.3

If you're building a new version of Android, if your Nexus S has an older
bootloader and radio image that is marked as being also possible in the
table above but is not recognized by the build system, you can locally
delete the version-bootloader and version-baseband lines in
device/samsung/crespo/board-info.txt
