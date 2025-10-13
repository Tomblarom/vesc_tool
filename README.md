# VESC® Tool

This is the source code of VESC Tool. A pre-compiled binary of both the stable release as well as the development release packaged with all the matching firmware for all supported hardware can be downloaded at http://vesc-project.com/

The stable binary is available for **Linux**, **Windows**, **MacOS**, **Android** and **iOS**. The development binary is available for **Linux**, **Windows** and **Android** and is updated every few days.

All binaries can also be downloaded free-of-charge for all platforms except for iOS, which only is available via the [Apple App Store](https://apps.apple.com/app/vesc-tool/id1605488891) as they do not allow any other distribution channel.

## Code Contribution, Distribution and Trademark Usage

> [!IMPORTANT]
> VESC is a registered trademark of Benjamin Vedder. Read the [trademark policies](https://vesc-project.com/trademark_policies) for more information.

The "official" binary release of VESC Tool is done via VESC Project only, as that gives users a way to verify that releases, that use the registered VESC trademark, originate from the VESC Project. It is not ok to host a binary release on a different channel and use the VESC trademark for that release.

It is ok to use the github fork function to make contributions to the code. That is because 1) it is the most convenient way to make contributions and 2) the forked repository states clearly that it is a fork and points back to the main repository where the original code can be found. Further, it is easy to see what the code changes are from the forked repository compared to the main repository via github, but that information is lost in a binary release.

Forks of VESC Tool on github are not encouraged to provide a binary release in the repository. That is because there is no way to tell the final binary apart from the official release once downloaded. Further, packaging the firmware, which has to be done as an additional step from a different repository, also cannot be verified whether it is done correctly.

**Forks without branding**  

Because the topic came up, here are some words about forking VESC Tool and removing the branding.  

If you make a fork of VESC Tool and remove all traces of the VESC trademark you are not breaking the trademark policies, but we still do not encourage that. The reason is that such forks are 1) confusing to users and 2) divert users away from the VESC Project itself and take away the opportunity to learn about it and to make donations if they choose to. For example, the majority of VESC donations today come from VESC Tool downloads.  

If you see a missing feature and you want to put in some work and make that feature available, we would appreciate if you contribute that back to the main VESC repositories. That way there is only one consistent and compatible release for everyone that is managed by the main authors of the VESC code who make the vast majority of the development. It also gives the main authors, who are the most familiar with the code, a chance to review features to make sure that they are as safe as possible and don't break other parts of the functionality.

## Add Your Hardware to the Binary Release

If you have custom hardware and you want to add support for it in the official release of VESC Tool, you can use the following steps:

1) Go to https://github.com/vedderb/bldc and use the github fork function.  
2) Make your changes, test them and make a pull request to the main repository.  
3) If the pull request gets accepted your hardware will become part of the next official release. It will show up in the binary beta typically after a few days and in the stable version the next time a stable release is made.

## Development

Generally speaking it's highly recommended to use a Linux-distribution, when compiling any of the VESC products ([tool](https://github.com/vedderb/vesc_tool), [bldc](https://github.com/vedderb/bldc), [express](https://github.com/vedderb/vesc_express), [package](https://github.com/vedderb/vesc_pkg) ..). Most commonly used and supported is Ubuntu. If you are on Windows, it's possible install various Linux-distributions as [subsystem](https://learn.microsoft.com/de-de/windows/wsl/install). Additionally you can use VS-Code + [WSL extension](https://code.visualstudio.com/docs/remote/wsl) for development. As perfect as this might sound, there are some caveats: 

> [!IMPORTANT]
> WSL is significantly more limited in its ressoures and access to the filesystem! To get the best performance, you always have to run the distro natively (bare-metal)!

The following steps are targeting building on Linux. Building Windows-executables on Windows is not too complex: Download and install Qt (as shown below) and use Qt Creator to compile. For compiling Linux on Windows, you have to start messing with MinGW.. 💀

###  Install Qt Creator + Designer (optional)

#### Using a package manager
```shell
sudo apt-get install qtcreator qttools5-dev-tools # not most-recent, but works very well
```

#### Using Qt-Online-Installer

Pick the most recent url from [qt.io](https://www.qt.io/download-qt-installer-oss), pull with `wget` and install the necessary dependencies.
```shell
wget https://xx.cloudfront.net/onlineinstallers/qt-online-installer-linux-x64-x.xx.x.run

sudo apt install libxkbcommon-x11-0 libxcb-icccm4 libxcb-image0 libxcb-keysyms1 libxcb-render-util0 libxcb-shape0 libxcb-cursor0 # installs X11/XCB dependencies
chmod +x qt-online-installer-linux-x64-4.10.0.run
./qt-online-installer-linux-x64-4.10.0.run
```

Login, install to home dir, select Qt Creator+Designer, accept license..
[ image? ]

```shell
sudo apt install libsecret-1-0 libsecret-tools gnome-keyring # use ldd to find missing libs
./Qt/Tools/QtCreator/bin/qtcreator

export QTWEBENGINE_LOCALES_PATH=$(find ~/Qt -type f -name 'en-US.pak' 2>/dev/null) # fixes localization warning
sudo apt install libatomic1 libnss3 libnspr4 libxkbfile1 libasound2 # use ldd to find missing libs
./Qt/Tools/QtDesignStudio/bin/qtdesignstudio
```

### Import wsl

```shell
wsl --unregister Ubuntu
wsl --import Ubuntu - <path to ubuntu-bak>
ubuntu config --default-user <user>
wsl --export Ubuntu <path to ubuntu-bak> # backup
wsl --update
wsl --shutdown
```

Add networkingMode=mirrored to .wslconfig to enable devices being discovered in network

### Install Qt5 (from sources)

Read build_lin

### Install Qt5 (from packages)

```shell
sudo apt-get update && sudo apt upgrade -y
sudo apt-get install -y \
  build-essential \
  qt5-qmake \
  qtbase5-dev \
  qtbase5-private-dev \
  qtpositioning5-dev \
  qtconnectivity5-dev \
  qtquickcontrols2-5-dev \
  libqt5svg5-dev \
  libqt5gamepad5-dev \
  libqt5serialport5-dev
  
sudo apt install -y \
  qml-module-qtquick2 \
  qml-module-qtquick-window2 \
  qml-module-qtquick-layouts \
  qml-module-qtquick-controls2 \
  qml-module-qtquick-controls \
  qml-module-qtquick-dialogs \
  qml-module-qtgraphicaleffects \
  qml-module-qt-labs-platform \
  qml-module-qt-labs-settings \
  qml-module-qtquick-extras
```

## Compile executable

**Note:** These instructions build VESC Tool without the BLDC firmwares bundled.

### Linux

Make sure that the required dependencies are installed. There is some advice in the [build_lin](./build_lin) file's comments. If you have Nix installed see below.

```shell
qmake -config release "CONFIG += release_lin build_original exclude_fw"
make -j8
./build/lin/vesc_tool_x.xx
```
Your new executable is in `build/lin/`.

**Experimental:** If you have a supported CAN-adapter (e.g. [CANable](https://canable.io)), you can add `HAS_CANBUS` as flag. See https://github.com/vedderb/vesc_tool/pull/39 and https://github.com/vedderb/vesc_tool/pull/43. Be aware, that adding serialbus to Qt seems to break the serial port on static builds. This feature is not included by default in the stable builds.

### Windows (on Linux)

To compile windows executables on Linux "cross-compilation" is needed. In this case you are going to use [MXE](https://mxe.cc/). First install all the necessary dependencies for building: https://mxe.cc/#requirements

Clone the repo, build MXE and add to `PATH`:
```shell
sudo apt install -y automake autoconf
sudo mkdir /opt/mxe2 && sudo chown $USER /opt/mxe2
git clone https://github.com/mxe/mxe.git /opt/mxe2 && cd /opt/mxe2
make -j$(nproc) MXE_TARGETS=i686-w64-mingw32.static-qmake-qt5 # or x86_64-w64-mingw32.static-qmake-qt5 for 64bit-only
# now grab a coke0! this takes a while..

export PATH=/opt/mxe2/usr/bin/:$PATH
```

Configure, build and run VESC-Tool:
```shell
cd vesc_tool && qmake -config release "CONFIG += release_win build_original exclude_fw"
make -j$(nproc)
```
Your new executable is in `build/win/`.

### Nix

The most easy way to build and run VESC Tool is to just run the provided program:

```shell
nix run
```

This will rebuild the program from scratch on each invokation. To enter a build environment with the dependencies installed for building it manually with QMake, run

```shell
nix develop
```

Then follow the normal build instructions for Linux.

**Note:** The Nix flake's outputs currently only supports x86 Linux.

### Starting QT Creator in Nix

QT Creator allows you to easily build and run the project. It also allows you to edit the page UIs with it's graphical editor. To run it using Nix simply start QT Creator from a shell with the build dependencies:

```shell
nix develop
nix run nixpkgs#qtcreator
```

This makes sure that QT Creator has access to the required dependencies.
