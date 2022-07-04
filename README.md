# Patches for Qt 5

Various iOS and Android patches for Qt 5. They have been tested with Qt versions 5.15.4 and 5.15.5.

To apply the patches:

1. Download and unpack Qt 5.15.x sources.
2. Open terminal and `cd` to the unpacked directory.
3. For every patch file run: `patch -p1 < PATH_TO_PATCH_FILE`

## General patches from [KDE project](https://community.kde.org/Qt5PatchCollection)

They were generated on 20.06.2022 using command:

    git format-patch v5.15.5-lts-lgpl..origin/kde/5.15

To apply them:

```bash
# set variable to point to this repo, example:
# qt5PatchesRepo=~/Downloads/Qt5-iOS-patches
for f in "$qt5PatchesRepo"/5.15.5/kde-patches/* ; do
  patch -p1 < "$f"
done
```

## iOS

By default, Qt 5.15 has iOS 12 deployment target. These patches lower it to iOS 10:

```bash
patch -p1 < "$qt5PatchesRepo"/5.15.5/ios10.patch
patch -p1 < "$qt5PatchesRepo"/5.15.5/qmake.patch
```

Qt configuration that has been tested with these patches (device-only build using Xcode 13.4.1), the command is executed from build directory:

> ../qt-everywhere-src-5.15.5/configure -prefix "$(pwd)/../ios10-device-xc13.4.1" -opensource -confirm-license -release -strip -static -xplatform macx-ios-clang -appstore-compliant -silent -sdk iphoneos -skip qt3d -skip qtactiveqt -skip qtandroidextras -skip qtcharts -skip qtconnectivity -skip qtdatavis3d -skip qtdoc -skip qtgamepad -skip qtlocation -skip qtlottie -skip qtmultimedia -skip qtnetworkauth -skip qtpurchasing -skip qtquick3d -skip qtquickcontrols -skip qtquicktimeline -skip qtremoteobjects -skip qtscript -skip qtscxml -skip qtsensors -skip qtserialbus -skip qtserialport -skip qtspeech -skip qttools -skip qtvirtualkeyboard -skip qtwayland -skip qtwebchannel -skip qtwebengine -skip qtwebglplugin -skip qtwebsockets -skip qtwebview -skip qtwinextras -skip qtx11extras -skip qtxmlpatterns -make libs -no-compile-examples -no-widgets -no-dbus -system-zlib -no-openssl -no-freetype -no-harfbuzz -no-gif -no-ico -system-sqlite -no-tiff -no-webp

### Build as shared libraries/frameworks

It's also possible to build shared (dynamic) Qt libraries thanks to the reporter of [QTBUG-85974](https://bugreports.qt.io/browse/QTBUG-85974):

    patch -p1 < "$qt5PatchesRepo"/5.15.5/ios-shared/ios_shared.patch

When configuring Qt build, use `-shared` option instead of `-static`. You can also specify `-no-framework`.

#### Shared libraries and AppStore

If you want to use a shared Qt build and submit such app to AppStore, you must also pass `-appstore-compliant` configure option.

However, be aware that more patches are required for that, as well as TestFlight version wouldn't work on older iOS versions (and possibly the later released AppStore version). See [this comment](https://bugreports.qt.io/browse/QTBUG-85974?focusedCommentId=662672&page=com.atlassian.jira.plugin.system.issuetabpanels:comment-tabpanel#comment-662672) for in-depth information and related patches.

## Android

By default, Qt 5.15 has minimum SDK set to API level 21 (Android 5.0). To allow deploying to lower Android versions (lowest possible is API level 16 - Android 4.1), apply the following patches:

```bash
patch -p1 < "$qt5PatchesRepo"/5.15.5/android/android.patch
patch -p1 < "$qt5PatchesRepo"/5.15.5/android/android-abis.patch
```

You must make separate 32- and 64-bit builds because 64-bit is possible only when minimum SDK is set to API level 21. Build example from macOS:

```bash
androidSdkPath=~/dev/android/sdk
ndkVersion=22.1.7171670
export JAVA_HOME=/usr/local/Cellar/openjdk@11/11.0.12/libexec/openjdk.jdk/Contents/Home
export PATH="$JAVA_HOME/bin:$PATH"

for bits in 32 64 ; do
  if [[ "$bits" == "32" ]]; then
    minSdkVersion=16
    abis=armeabi-v7a,x86
  else
    minSdkVersion=21
    abis=arm64-v8a,x86_64
  fi
  ../qt-everywhere-src-5.15.5/configure -prefix "$(pwd)/../android-$bits" -shared \
    -android-sdk "$androidSdkPath" \
    -android-ndk "$androidSdkPath/ndk/$ndkVersion" \
    -android-ndk-platform android-$minSdkVersion \
    -android-ndk-host darwin-x86_64 \
    -android-abis $abis \
    <other configure options>
  make -j6
  make install
  rm -rf * .*
done
```
