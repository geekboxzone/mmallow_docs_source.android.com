<!--
   Copyright 2011 The Android Open Source Project

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

# Building Kernels #

If you are only interested in the kernel, you may use this guide
to download and build the appropriate kernel.

The following instructions assume that you have not downloaded all
of AOSP. If you have downloaded all of AOSP, you may skip the git
clone steps other than the step to download the actual kernel sources.

We will use the Pandaboard kernel in all the following examples.


## Figuring out which kernel to build ##

This table lists the names and locations of the kernel sources and binaries:

Device   | Binary location               | Source location | Build configuration
---------|-------------------------------|-----------------|--------------------
manta    | device/samsung/manta/kernel   | kernel/exynos   | manta_defconfig
mako     | device/lge/mako-kernel/kernel | kernel/msm      | mako_defconfig
grouper  | device/asus/grouper/kernel    | kernel/tegra    | tegra3_android_defconfig
tilapia  | device/asus/grouper/kernel    | kernel/tegra    | tegra3_android_defconfig
maguro   | device/samsung/tuna/kernel    | kernel/omap     | tuna_defconfig
toro     | device/samsung/tuna/kernel    | kernel/omap     | tuna_defconfig
panda    | device/ti/panda/kernel        | kernel/omap     | panda_defconfig
stingray | device/moto/wingray/kernel    | kernel/tegra    | stingray_defconfig
wingray  | device/moto/wingray/kernel    | kernel/tegra    | stingray_defconfig
crespo   | device/samsung/crespo/kernel  | kernel/samsung  | herring_defconfig
crespo4g | device/samsung/crespo/kernel  | kernel/samsung  | herring_defconfig

You will want to look at the git log for the kernel binary in the device
project that you are interested in.
Device projects are of the form device/&lt;vendor&gt;/&lt;name&gt;.

    $ git clone https://android.googlesource.com/device/ti/panda
    $ cd panda
    $ git log --max-count=1 kernel

The commit message for the kernel binary contains a partial git log
of the kernel sources that were used to build the binary in question.
The first entry in the log is the most recent, i.e. the one used to
build that kernel. You will need it at a later step.

## Downloading sources ##

Depending on which kernel you want,

    $ git clone https://android.googlesource.com/kernel/common.git
    $ git clone https://android.googlesource.com/kernel/exynos.git
    $ git clone https://android.googlesource.com/kernel/goldfish.git
    $ git clone https://android.googlesource.com/kernel/msm.git
    $ git clone https://android.googlesource.com/kernel/omap.git
    $ git clone https://android.googlesource.com/kernel/samsung.git
    $ git clone https://android.googlesource.com/kernel/tegra.git

  - The `goldfish` project contains the kernel sources for the emulated
platforms.
  - The `msm` project has the sources for ADP1, ADP2, Nexus One, Nexus 4,
and can be used as a starting point for work on Qualcomm MSM chipsets.
  - The `omap` project is used for PandaBoard and Galaxy Nexus,
and can be used as a starting point for work on TI OMAP chipsets.
  - The `samsung` project is used for Nexus S,
and can be used as a starting point for work on Samsung Hummingbird chipsets.
  - The `tegra` project is for Xoom and Nexus 7,
and can be used as a starting point for work on NVIDIA Tegra chipsets.
  - The `exynos` project has the kernel sources for Nexus 10,
and can be used as a starting point for work on Samsung Exynos chipsets.

## Downloading a prebuilt gcc ##

Ensure that the prebuilt toolchain is in your path.

    $ export PATH=$(pwd)/prebuilts/gcc/linux-x86/arm/arm-eabi-4.6/bin:$PATH

Or

    $ export PATH=$(pwd)/prebuilts/gcc/darwin-x86/arm/arm-eabi-4.6/bin:$PATH

On a linux host, if you don't have an Android source tree, you can download
the prebuilt toolchain from:

    $ git clone https://android.googlesource.com/platform/prebuilts/gcc/linux-x86/arm/arm-eabi-4.6

## Building ##

As an example, we would build the panda kernel using the following commands:

    $ export ARCH=arm
    $ export SUBARCH=arm
    $ export CROSS_COMPILE=arm-eabi-
    $ cd omap
    $ git checkout <commit_from_first_step>
    $ make panda_defconfig
    $ make

To build the tuna kernel, you may run the previous commands replacing all
instances of "panda" with "tuna".

The kernel binary is output as `arch/arm/boot/zImage`, and needs to be copied
into the Android source tree in order to build the matching boot image.
