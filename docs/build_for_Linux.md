# Building JS8Call on Linux

...and also building Hamlib.

## Linux versions

This file contains build instructions for

* Debian 12 or newer, or Debian derivatives, e.g., Mint 22.1, Ubuntu 24.04, MX Linux, Raspberry Pi OS (64-bit)
* Fedora 41 or newer.

## Compile prerequisites

### Debian &amp; derivatives:

Issue the following commands in a terminal window (running `bash` or compatible):

```bash
sudo apt-get update &&
sudo apt-get install -y build-essential file git cmake \
   libhamlib-dev \
   qt6-base-dev qt6-multimedia-dev qt6-serialport-dev \
   libusb-1.0-0-dev libudev-dev libxkbcommon-dev \
   libfftw3-dev libboost1.81-dev
```

If you get `Couldn't find any package by glob 'libboost1.81-dev'`, you need to replace `libboost1.81-dev` with another version. To find out which versions are available, issue the command

```bash
apt-cache search --names-only libboost1 | grep -P 'libboost1.\d+\-dev'
```

JS8Call has been compiled successfully with `libboost1.81-dev`, `libboost1.83-dev`, and `libboost1.88-dev`.

You should also check which version of Qt this gives you.

```shell
dpkg -s qt6-base-dev | grep Version:
```

The version should be at least 6.5.X for some X. If it is only some 6.4.X, that Qt version cannot be used to compile. You'll need to refer to "getting Qt" below.

### Fedora:

Issue the following commands in a terminal window:

```bash
sudo dnf update &&
sudo dnf install git cmake clang \
  boost-devel fftw-devel libusb1-devel hamlib-devel \
  qt6-qtbase-devel qt6-qtmultimedia-devel qt6-qtserialport-devel \
  libudev-devel rpm-build
```

Check whether the version of Qt this gives you is at least 6.5.X for some X, e.g., by checking the output of

```
dnf info qt6-qtbase-devel
```

and looking at the installed version.

### Obtaining Qt if you have to, or to fix audio problems

Some distributions, notably Debian 12 (aka Bookworm), Ubuntu Noble, and Linux Mint 22.2, come with old Qt 6.4.X versions.  These are not good enough for building JS8Call.  So you need to obtain a newer version.

Qt is deprecating use of native audio back-ends in favor of standardizing on FFmpeg audio. This requires either PulseAudio or PipeWire on Linux. Some Linux distributions ship Qt 6.8.3 without proper FFmpeg audio support.  So if audio does not work with your version of JS8Call, obtaining Qt 6.8.3 together with FFmpeg audio support built-in could help you.

Once you need to obtain Qt anyway, obtaining Qt 6.8.3 (or even newer) is recommended.  It is possible to use older versions of Qt, down to 6.5, but there is significant risk that unwanted audio or PTT bugs may be injected into your build.

There are several ways of obtaining Qt.  What we present here results in the new Qt version being used just for JS8Call, without touching the system Qt.  The two can co-exist in parallel.

#### Obtaining from us

You can download pre-built Qt 6.8.3 library packages with FFmpeg audio support built-in [https://github.com/Chris-AC9KH/js8lib/releases/tag/js8lib-2.3](https://github.com/Chris-AC9KH/js8lib/releases/tag/js8lib-2.3) for both x86_64 and arm64.  Unpack the appropriate one to whatever directory you want, maybe `/opt/js8call-qt`.  These files should stay there as long as you want to be able to use the `js8call` binary you'll build.

#### Obtaining form Qt

Alternatively, you can register with [https://www.qt.io/](https://www.qt.io/), the company behind Qt, and download and install Qt with their "Online Installer".  You can install the default choice, which gives you a selection of the most recent Desktop version of Qt. Alternatively, you can perform a custom installation to pull Qt 6.8.3. If you used the default, you'll need to afterwards run the program `qt/MaintenanceTool` to also install the additional Qt library "Qt6 Serial Port".

#### Using the newly installed Qt for the build

Let us say you define a variable `$qt_path` to point at your new Qt installation.  This needs to point to a directory that contains a subdirectory `lib`, and `ls -l $qt_path/lib/cmake/Qt6/Qt6Config.cmake` needs to be successful.  So possibly `qt_path=/opt/js8call-qt/Qt` (if you obtained from us) or something like `qt_path=/opt/js8call-qt/qt/6.9.3/gcc_64` if you obtained directly from Qt.

With that, the first `cmake` command below needs to become:

```bash
cmake -D CMAKE_PREFIX_PATH=$qt_path -D CMAKE_INSTALL_PREFIX=/opt/js8call ../js8call 
```

## Choice of build directory

To keep everything in one place, we recommend you use a build directory.  This can be anywhere.  One choice is `$HOME/js8-build`, which is what is used in the sequel.

Create that directory via

```bash
mkdir $HOME/js8-build
```

## Building JS8Call

To obtain the JS8Call source, issue the following commands in a terminal window:

```bash
cd $HOME/js8-build &&
git clone https://github.com/js8call/js8call.git
```

This creates a new directory `$HOME/js8-build/js8call` containing the sources.

If you did this earlier and want to update your copy of the sources, there is no need to remove the `$HOME/js8-build/js8call` directory and start from scratch.  Instead, issue

```bash
cd $HOME/js8-build/js8call &&
git pull --ff-only origin
```

For the build itself, issue the following commands in the same terminal window (but read the paragraph that immediately follows first):

```bash
cd $HOME/js8-build && mkdir build && cd build &&
cmake -D CMAKE_INSTALL_PREFIX=/opt/js8call ../js8call &&
cmake --build . -- -j 4
```

The final `-- -j 4` part instructs the build to do up to 4 different things in parallel. This is not essential, but speeds up the build process considerably. You can tune this by changing the 4 to some other number. To stay on the safe side, use a number that is less than the total amount of RAM in GByte of the computer that runs the build, and also less than the number of CPU kernels. Other than slowness, it never hurts to use 1.  Build instability can result if you use too high a number.

This builds `JS8Call` (which takes a while), but does not install it yet.  For quick experiments, you can skip that still-missing installation step and run the `JS8Call` binary that the build has provided in the `build` directory:

```bash
cd $HOME/js8-build/build &&
./JS8Call
```

## Installation

**FIXME Installation currently does not work. It should be**

```
cmake --install .
```

**but that does not work.**

Workaround: For now, run `./JS8Call` from the directory where you built.

Alternatively, you can copy that binary manually wherever you want it to sit.

## Optional: Building Hamlib

**It is not required to compile Hamlib yourself in order to compile JS8Call.  It is not required to compile JS8Call to make use of a self-compiled Hamlib.**

You may want to do compile Hamlib, e.g., if your rig is only supported (or better supported) by a newer version of Hamlib, newer than the one that comes with your distribution.

To obtain the source code of Hamlib, issue the following commands in a terminal window:

```bash
mkdir -p $HOME/js8-build &&
cd $HOME/js8-build &&
git clone https://github.com/Hamlib/Hamlib.git hamlib &&
cd hamlib
```

To see what versions of Hamlib are available, while in the hamlib directory, type `git tag` to get a list. If, for example, you find a tag `4.6.4` and want to build that, run (in the same terminal):

```bash
git switch -c my_build 4.6.4
```

So, now we have the source we want to compile. What is needed to do the compilation? Unfortunately, the Hamlib folks do not make it too easy to find that out.

But there's a trick: Simply use whatever prerequisites the version has that comes with your distribution. There is a good chance the compilation of the newer version can thrive on the same diet.

### Getting packages needed to build `hamlib` on Debian

For that, see to it that your `/etc/apt/sources.list` or your `/etc/apt/sources.list.d/debian.sources` provides not only the binary packages, but also the **sources** of all packages. In the old format, simply copy every `deb` line (or at least every `deb` line that has `main`), and changing the initial `deb` to `deb-src` in your copy. In the new format, look for the `Types: deb` lines of the stances that have `main` in the `Components:` list, and change each such line to `Types: deb deb-src`.

Now the following incantation will pull in whatever your distribution used to build its version of Hamlib:

```bash
sudo apt-get update &&
sudo apt-get build-dep hamlib
```

### Getting packages needed to build `hamlib` on Fedora

```bash
sudo dnf build-dep hamlib
```

### Build and install Hamlib

To actually build and install Hamlib to `/opt/hamlib-for-js8call`:

```bash
cd $HOME/js8-build/hamlib &&
./bootstrap &&
./configure --prefix=/opt/hamlib-for-js8call &&
make -j 4 &&
sudo make install-strip
```

As for the `-j 4`, the same as above applies.  If in doubt (especially while using a modest machine such as a Raspberry Pi), use `1` or remove the entire `-j 4` part.

### Back to square one

If you want to start a new build from a clean slate, it is not necessary to remove the entire `hamlib` directory and checkout anew. Instead, use this:


```bash
cd $HOME/js8-build/hamlib &&
git status --ignored --porcelain | perl -wnle 'print $2 if m/^(\!\!|\?\?)\s+(.+)$/' | xargs rm -rf
```

### How to feed my custom-crafted Hamlib to JS8Call?

**It is not necessary to build JS8Call in order to benefit from your newly compiled Hamlib version.** You can compile JS8Call if you have a reason to do so.  Just wanting a special Hamlib isn't forcing you to do that.  You can use any standard `js8call` binary, including one that you installed from a pre-built package.

Starting `js8call` in a special way makes it use your custom-crafted Hamlib.  That special way is simple: Set an environment variable `LD_LIBRARY_PATH` to an appropriate value. In a terminal, run

```bash
export LD_LIBRARY_PATH=/opt/hamlib-for-js8call/lib
```

and then start `js8call` **in that same terminal**.

To verify which version of `hamlib` is actually being used, run

```bash
ldd js8call | grep hamlib
```

If you compiled and installed `js8call` yourself and the terminal's shell doesn't find it (it ought to, though!), you can push its nose at the program by typing the full path `/opt/js8call/bin/js8call` rather than just `js8call`.


## Cross-building

This section is intended for experts and assumes a somewhat advanced level of Linux familiarity.  It discusses building for a &ldquo;foreign&rdquo; target Linux distribution, i.e., one that is different from the host Linux distribution that governs the computer running the build.

### Docker

Docker makes it relatively convenient to compile for the same CPU architecture, but another Linux distribution.  E.g., use Debian Bookworm to compile for Fedora 42.

Prerequisites:

* Have Docker up and running on your host computer.
* Grab a docker image from [https://hub.docker.com/](https://hub.docker.com/) that reflects the desired target distribution, e.g., set `image=fedora:42` and then do `docker pull $image`

Do the `git clone` part for the JS8Call sources as above if you haven't already. Then:

```bash
cd $HOME/js8-build &&
docker run --rm --name=js8build-container -ti -v $(pwd):/home/js8-build -w /home/js8-build --entrypoint=/usr/bin/bash $image
```

In this terminal, type the stuff that needs `sudo`, but without the `sudo`, as you are already `root` in the container.

In another terminal, run the regular user (non-root) stuff via

```bash
docker exec -ti -u "$(id -u)" -e HOME=/home -w /home/js8-build js8build-container /usr/bin/bash
```

In this terminal, type all commands from the above instructions that do **not** need `sudo`.

After you are done with the build and produced the `.deb` or `.rpm` installation archive, simply terminate the shell in the `docker run` terminal.  As that Docker container was started with `--rm`, termination will clean up the container.  You will find the installation archive on your host machine at `$HOME/js8-build/build`.

### Qemu

This is how to compile for Raspberry Pi without actually using one.

Before starting, obtain an ample supply of patience, as this typically ends up being _sloooow_ (hours).

Basically, the instructions from [https://interrupt.memfault.com/blog/emulating-raspberry-pi-in-qemu](https://interrupt.memfault.com/blog/emulating-raspberry-pi-in-qemu) (the part without Docker) work to virtualize a Raspi 3 and run the build on it.  As a distribution, Raspi OS `2025-05-13-raspios-bookworm-arm64.img` from [https://www.raspberrypi.com/software/operating-systems/](https://www.raspberrypi.com/software/operating-systems/) can be used.

The following caveats apply:

* There is some glitch in the console setup, so no log is seen in the terminal running `qemu`. This does not affect `ssh` access.
* After the `qemu-img resize` step, `gparted` can be used to enlarge the second partition, if/as the system doesn't do this automatically on first start.
* It is easy to forget `losetup -d /dev/loopN` before starting `qemu`. You probably shouldn't.
* With just 1GB of RAM for a virtual Raspi, a build with the instructions as above was unstable with `-j 4`, leading to a `qemu` crash (eating CPU but no longer responding to `ssh` connection attempts; this may have triggered some `qemu` bug?).  Removing the entire `-- -j 4` stance and re-starting the build in a cleaned directory helped.

In a separate experiment, it was tried to use `qemu-user`, `qemu-user-binfmt`, and `chroot` (on host Debian Bookworm). An environment for `chroot` was created via `losetup -P --show -f 2025-05-13-raspios-bookworm-arm64.img` and mounting `loopNp2`. It was not even possible to start a humble `bash` in the `chroot`-environment, the ubiquitous error message was &ldquo;file not found&rdquo;.

