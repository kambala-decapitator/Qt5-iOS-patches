# Qt5.10-iOS9
Qt 5.10 decided to drop support for 32-bit devices and iOS 9 too early, in my opinion. This set of patches allows compiling Qt 5.10.1 from sources with 32-bit (armv7 / i386) and iOS 9 enabled.

### Steps:

1. [Download Qt 5.10.1 sources](https://download.qt.io/official_releases/qt/5.10/5.10.1/single/qt-everywhere-src-5.10.1.tar.xz)
2. `cd` to sources root directory (qt-everywhere-src-5.10.1) and apply patches
3. `configure` and `make`

Successfully tested with the following configuration:

> ../qt-everywhere-src-5.10.1/configure -prefix ../ios-release -opensource -confirm-license -xplatform macx-ios-clang -appstore-compliant -c++std c++14 -skip qt3d -skip qtactiveqt -skip qtcanvas3d -skip qtcharts -skip qtconnectivity -skip qtdatavis3d -skip qtgamepad -skip qtlocation -skip qtmultimedia -skip qtnetworkauth -skip qtremoteobjects -skip qtscript -skip qtscxml -skip qtsensors -skip qtserialbus -skip qtserialport -skip qtspeech -skip qttranslations -skip qtvirtualkeyboard -skip qtwayland -skip qtwebchannel -skip qtwebengine -skip qtwebglplugin -skip qtwebsockets -skip qtwebview -skip qtwinextras -skip qtx11extras -skip qtxmlpatterns -nomake examples -nomake tests -no-compile-examples -qml-debug -system-sqlite -release -shared

and running a small QtQuick project on iPad 2 and iPod 5 that links to frameworks:

- QtCore.framework
- QtGui.framework
- QtNetwork.framework
- QtQml.framework
- QtQuick.framework
- QtQuickControls2.framework
- QtQuickTemplates2.framework
