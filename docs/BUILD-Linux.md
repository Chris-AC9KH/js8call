------------------------------------------------------------------------------
# Building JS8Call on Linux

**Prerequisites**\
Linux Versions:  Debian 12 or newer (or derivatives, e.g., Mint 22.1, Ubuntu 24.04, MX Linux); Fedora 39 or newer.  Enable Source Code archives in the repositories, then issue the following commands at the command-line prompt:

Debian & derivatives:
```
   sudo apt update && sudo apt install -y build-essential git cmake libhamlib-dev qt6-base-dev \ 
                       qt6-multimedia-dev qt6-serialport-dev libusb-1.0-0-dev \
                       libfftw3-dev libboost1.83-dev libudev-dev libxkbcommon-dev
```
```
   sudo apt build-dep hamlib
```
  
Fedora:
```
    sudo dnf update \
    sudo dnf install @development-tools @development-libraries libqt6* qt6* rpmbuild \
    && dnf build-dep hamlib \
    && dnf build-dep js8call \
    && sudo dnf remove hamlib
```

**Building Hamlib**\
It is easiest to build Hamlib 4.6.4 and install it on the system.  To see what versions of Hamlib are available, while in the hamlib directory, type `git branch -r` or `git tag` to get a list.  To get the source and build Hamlib, open a command prompt and issue the following commands:
```
    cd ~/Downloads \
        && git clone https://github.com/Hamlib/Hamlib.git hamlib
```
```
    cd hamlib && git checkout 4.6.4
```
**on Debian:**
```
    ./bootstrap \
        && ./configure \
        cmake --build . -- -j 4 \
        && sudo make install-strip \
        && sudo ldconfig
```
**on Fedora:**
```
    cd ~/Downloads/hamlib/src \
    ./bootstrap \
    mkdir build && cd build \
    ../src/configure --prefix=~Downloads/hamlib --disable-shared --enable-static --without-cxx-binding --disable-winradio CFLAGS="-g -O2 -fdata-sections -ffunction-sections" LDFLAGS="-Wl,--gc-sections" \
    make -j 4 \
    make install-strip \
    cd bin && ./rigctl --version \
    exit
```
Run `rigctl --version` (this is to test that Hamlib is installed and at the correct version level)

**Building JS8Call**\
To obtain the JS8Call source and build, issue the following commands in a terminal window:
```
    cd ~/Downloads \
        && git clone https://github.com/js8call/js8call.git js8call
```
```
    cd js8call && mkdir build && cd build \
        && cmake -D CMAKE_PREFIX_PATH=~/Downloads/hamlib -D CMAKE_INSTALL_PREFIX=/opt/js8call .. \
        && cmake --build . -- -j 4 \
        && sudo make install
```
   
   When the build is finished, to package an installer, type `cpack -G DEB` for Debian-based systems like Debian, Ubuntu, and Mint; or `cpack -G RPM` for Fedora-based systems.