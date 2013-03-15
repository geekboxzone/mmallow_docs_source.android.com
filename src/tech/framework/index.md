<!--
   Copyright 2013 The Android Open Source Project

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

# Android Framework Technical Information

This document provides some general information about how the Android platform
works that is useful for system integrators.

# Shared libraries

System integrators who are building their own system image can optionally
add in their own runtime shared libraries that can be used by applications --
both other built-in applications as well as regular third party applications.
A shared library is identified with a unique name, following standard Java-style
naming conventions.  Applications link to these libraries using the
[uses-library](http://developer.android.com/guide/topics/manifest/uses-library-element.html)
tag.

There are two types of shared libraries you can create: traditional simple .jar
libraries, and newer shared libraries that are bundled in a regular .apk.

## Jar libraries

A jar library is simply a loose jar file placed somewhere on the system image.  For
the platform to recognize your library, you will also need to add a file in
`/system/etc/permissions` that declares the shared library.  For example,
you may have a `/system/etc/permissions/com.example.mylib.xml` file containing:

    <?xml version="1.0" encoding="utf-8"?>
    <permissions>
        <library name="com.example.mylib"
                file="/system/framework/com.example.mylib.jar" />
    </permissions>

The jar file itself would then be placed at `/system/framework/com.example.mylib.jar`.

Note that jar-based shared libraries can not contain Android resources.  Any resources
they need will generally need to come from the base platform resources, so if your
library does have resources it is recommended that you use an apk library instead.
These libraries also can not be updated outside of system updates that modify the
system image.  If you can live with these limitations, they are the preferred approach,
since they are the most efficient way to provide a shared library.

## APK libraries

Starting with Jelly Bean MR2, Android provides a new shared library feature based
on standard APK files.  Implementing such a shared library is easy: include the
[library](http://developer.android.com/guide/topics/manifest/library-element.html)
tag in your apk that is pre-installed on the system image.  Android's package manager
will automatically detect this shared library and make it available to other
applications.

There are some significant advantages to APK-based shared libraries, but they also
have more overhead than jar libraries as well.  The additional features you get are:

+ These libraries can also run as a regular Android application.  For example, your
apk may primarily consist of a Service that clients interact with.  You
can define an aidl interface to that service in the apk, which the service implements
and the library API uses for calling to the service.  Bundling this all together in
one apk makes maintenance easier since you know that the internal aidl protocol will
always be consistent between clients and service.

+ These libraries can contain Android resources.  Not only can you use them when the
apk operates as a normal app, but you can also access them from clients using the
library.  Note, however, that these resources are **not** linked or merged with the
client application's resources -- to access them from within a client application,
you will need to use
[Context.createPackageContext](http://developer.android.com/reference/android/content/Context.html#createPackageContext(java.lang.String, int)).
The client application can not directly access these resources, so you can not do
things like publish XML attribute or drawable resources that the app could reference
in its layout files.

+ You can deliver updates to these libraries outside of core system updates.  Since
this is just a regular apk, you can update the library through the normal mechanism
of installing updated applications.  When this is done, all client processes will be
killed and use the new version of the library the next time they run.

There are also some limitations and down-sides to apk libraries that you should be
aware of:

+ Pre-installed applications that link against an apk library can not be installed
as a pre-dexopt on the system image.  They must exist as their full apk, with the
platform generating the dexopt file into /data at first boot.  This is required because
the library can change on the application, requiring that it be re-linked to it,
which would break the application if it is in its pre-dexopt form (which on the system
image means the apk doesn't contain the .dex code in the apk, and it has the final
.odex file next to it which is directly used when running).

+ When an update to the shared library apk is installed, all client applications
must be re-dexopt'd in addition to the library apk itself.  If there are many
client applications, this means that installing a library update can take a significant
amount of time.  This will also result in any processes currently using the library
being killed -- for example if a music app is currently playing music and links to
the library, playback will be disrupted during the install.

+ Updates to system apks can not add new shared libraries to the apk.  You must declare
the available libraries in the apk that is on the system image; if you want to add any
libraries to an apk you must update it on the system image.  This is done to avoid getting
into bad situations for example if the user uninstalls updates to the apk -- without such
a restriction, a newer update an be uninstalled, removing the new shared library and breaking
any applications depending on it that had been allowed to be installed when the library
was there.

## Library versioning

Android does not provide any support for versioning shared libraries, besides the normal
application versioning available if you are using an apk library.  The base implementation
of your shared library should always provide the necessary APIs for applications to check
its version.  A typical API for an apk shared library would look like:

    public class SharedLibraryMain {
        static String LIBRARY_PACKAGE = "com.google.android.test.shared_library";

        /**
         * Base version of the library.
         */
        public static int VERSION_BASE = 1;

        /**
         * The second version of the library.
         */
        public static int VERSION_SECOND = 2;

        /**
         * Return the version number of the currently installed library.
         */
        public static int getVersion(Context context) {
            PackageInfo pi = null;
            try {
                pi = context.getPackageManager().getPackageInfo(LIBRARY_PACKAGE, 0);
                return pi.versionCode;
            } catch (PackageManager.NameNotFoundException e) {
                throw new IllegalStateException("Can't find my package!", e);
            }
        }

        /**
         * Check that the library's version is at least the given minimum version,
         * displaying a dialog to have the user install an update if that is not true.
         * The dialog is displayed as a DialogFragment in your activity if a newer
         * version is needed.  If a newer version is needed, false is returned.
         */
        public static boolean ensureVersion(final Activity activity, int minVersion) {
            final FragmentManager fm = activity.getFragmentManager();
            final String dialogTag = LIBRARY_PACKAGE + ":version";
            Fragment curDialog = fm.findFragmentByTag(dialogTag);

            if (getVersion(activity) >= minVersion) {
                // Library version is sufficient.  Make sure any version dialog
                // we had shown is removed before returning.
                if (curDialog != null) {
                    fm.beginTransaction().remove(curDialog).commitAllowingStateLoss();
                }
                return true;
            }

            // The current version of the library does not meet the required version.
            // If we don't already have a version dialog displayed, display it now.
            if (curDialog == null) {
                curDialog = new VersionDialog();
                fm.beginTransaction().add(curDialog, dialogTag).commitAllowingStateLoss();
            }

            // Tell the caller that the current version is not sufficient.
            return false;
        }
    }

If you are writing a simple jar library, you will need to put the current version as
a contant in the code of the library intead of pulling it from the apk version.

## Library samples

Complete samples of an apk library and client can be found in the Android source tree
at `frameworks/base/tests/SharedLibrary`.
