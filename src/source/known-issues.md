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

## Black Gingerbread Emulator ##

**Symptom**: The emulator built directly from the gingerbread branch
doesn't start and stays stuck on a black screen.

**Cause**: The gingerbread branch uses version R7 of the emulator,
which doesn't have all the features necessary to run recent versions
of gingerbread.

**Fix**: Use version R12 of the emulator, and a newer kernel that matches
those tools. No need to do a clean build.

    $ repo forall external/qemu -c git checkout aosp/tools_r12
    $ make
    $ emulator -kernel prebuilt/android-arm/kernel/kernel-qemu-armv7
