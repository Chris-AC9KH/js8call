------------------------------------------------------------------------------
# Building JS8Call on Linux

## Linux versions

This file contains build instructions for

* Debian 12 or newer, or Debian derivatives, e.g., Mint 22.1, Ubuntu 24.04, MX Linux
* Fedora 39 or newer.

## Compile prerequisites

### Debian &amp; derivatives:

Issue the following commands in a terminal window:

```
sudo apt update &&
sudo apt install -y build-essential git cmake \
   libhamlib-dev \
   qt6-base-dev qt6-multimedia-dev qt6-serialport-dev \
   libusb-1.0-0-dev libudev-dev libxkbcommon-dev \
   libfftw3-dev libboost1.83-dev 
```
  
### Fedora:

**FIXME These build instructions ask for the build dependencies of whatever `js8call` version Fedora currently has, which is less than optimal if that version happens to be from the 2.2.x series.**

Issue the following commands in a terminal window:

```
    sudo dnf update &&
    sudo dnf install @development-tools @development-libraries libqt6* qt6* rpmbuild &&
    dnf build-dep js8call
```

## Building JS8Call

To obtain the JS8Call source, issue the following commands in a terminal window:

```
cd ~/Downloads &&
git clone https://github.com/js8call/js8call.git
```

(You can use any other directory instead of `~/Downloads` as you please.)

For the build itself, issue the following commands in the same terminal window:

```
cd js8call && mkdir build && cd build &&
cmake -D CMAKE_INSTALL_PREFIX=/opt/js8call .. &&
cmake --build . -- -j 4
```

This builds `js8call`, but does not install it yet.

For quick experiments, you can skip the installation step and run the `js8call` binary that the build has provided in the `build` directory.  Assuming you're still in the same terminal, this should do the trick:

```
./js8call
```

## Installation

### Debian-based systems

In the same terminal window that saw the build, run

```
cpack -G DEB
```

This produces a package file of the type `js8call_*_*.deb`, which you can install with

```
apt-get install ./js8call_*_*.deb
```

### Fedora

In the same terminal window that saw the build, run

```
cpack -G RPM
```

This leaves you with a file `js8call*.rpm` which you can install via

```
sudo dnf ./js8call*.rpm
```

## Optional: Building Hamlib

It is not required to compile Hamlib yourself in order to compile JS8Call.

But you may want to do so, e.g., if your rig is only supported (or better supported) by a newer version of Hamlib, newer than the one that comes with your distribution.

To obtain the source code of Hamlib, issue the following commands in a terminal window:

```
cd ~/Downloads &&
git clone https://github.com/Hamlib/Hamlib.git hamlib &&
cd hamlib
```

To see what versions of Hamlib are available, while in the hamlib directory, type `git tag` to get a list. If, for example, you find a tag `4.6.4` and want to build that, run (in the same terminal):

```
git switch -c my_build 4.6.4
```

### Compiling Hamlib on Debian:

Make sure your `/etc/apt/sources.list` has `deb-src` files echoing the `deb` files.

```
sudo apt update &&
sudo apt build-dep hamlib &&
./bootstrap &&
./configure &&
cmake --build . -- -j 4 &&
sudo make install-strip &&
sudo ldconfig
```

### on Fedora

```
dnf build-dep hamlib  &&
sudo dnf remove hamlib
```

## Compiling js8call to use that Hamlib

**FIXME It is a bit uncertain where that Hamlib gets installed. We may want to pin that by changing the above. I think `~/Downloads/hamlib` may not be the most preferable option, better maybe `~/my_hamlib` or something.  When that has been fixed, add instructions here to set `CMAKE_PREFIX_PATH` accordingly.**
