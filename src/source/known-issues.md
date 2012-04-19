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

# Known Issues #

Even with our best care, small problems sometimes slip in. This page keeps
track of the known issues around using the Android source code.

## Missing CTS Native XML Generator ##

**Symptom**: On some builds of IceCreamSandwich and later, the following
warning is printed early during the build:
`/bin/bash: line 0: cd: cts/tools/cts-native-xml-generator/src/res: No
such file or directory`

**Cause**: Some makefile references that path, which doesn't exist.

**Fix**: None. This is a harmless warning.

## Black Gingerbread Emulator ##

**Symptom**: The emulator built directly from the gingerbread branch
doesn't start and stays stuck on a black screen.

**Cause**: The gingerbread branch uses version R7 of the emulator,
which doesn't have all the features necessary to run recent versions
of gingerbread.

**Fix**: Use version R12 of the emulator, and a newer kernel that matches
those tools. No need to do a clean build.

    $ repo forall platform/external/qemu -c git checkout aosp/tools_r12
    $ make
    $ emulator -kernel prebuilt/android-arm/kernel/kernel-qemu-armv7

## Emulator built on MacOS 10.7 Lion doesn't work. ##

**Symptom**: The emulator (any version) built on MacOS 10.7 Lion
and/or on XCode 4.x doesn't start.

**Cause**: Some change in the development environment causes
the emulator to be compiled in a way that prevents it from working.

**Fix**: Use an emulator binary from the SDK, which is built on
MacOS 10.6 with XCode 3 and works on MacOS 10.7.

## Difficulties syncing the source code (proxy issues). ##

**Symptom**: `repo init` or `repo sync` fail with http errors,
typically 403 or 500.

**Cause**: There are quite a few possible causes, most often
related to http proxies, which have difficulties handling the
large amounts of data getting transfered.

**Fix**: While there's no general solution, using python 2.7
and explicitly using `repo sync -j1` have been reported to
improve the situation for some users.

## `make snod` and emulator builds. ##

**Symptom**: When using `make snod` (make system no dependencies)
on emulator builds, the resulting build doesn't work.

**Cause**: All emulator builds now run Dex optimization at build
time by default, which requires to follow all dependencies to
re-optimize the applications each time the framework changes.

**Fix**: Locally disable Dex optimizations with
`export WITH_DEXPREOPT=false`, delete the existing optimized
versions with `make installclean` and run a full build to
re-generate non-optimized versions. After that, `make snod`
will work.

## "Permission Denied" during builds. ##

**Symptom**: All builds fail with "Permission Denied", possibly
along with anti-virus warnings.

**Cause**: Some anti-virus programs mistakenly recognize some
source files in the Android source tree as if they contained
viruses.

**Fix**: After verifying that there are no actual viruses
involved, disable anti-virus on the Android tree. This has
the added benefit of reducing build times.

## Camera, GPS and NFC don't work on Galaxy Nexus. ##

**Symptom**: Camera, GPS and NFC don't work on Galaxy Nexus.
As an example, the Camera application crashes as soon as it's
launched.

**Cause**: Those hardware peripherals require proprietary
libraries that aren't available in the Android Open Source
Project.

**Fix**: None.
