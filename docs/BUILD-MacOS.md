
------------------------------------------------------------------------------
# MacOS Prerequisites:
You will need Xcode and the Xcode commandline tools installed. Xcode can be downloaded from the Apple Store
for your Mac. For this example I used Xcode 16.2 on MacOS 15 Sequoia

Obtain cmake v4.03 from https://github.com/Kitware/CMake/releases/download/v4.0.3/cmake-4.0.3.tar.gz
Unpack the cmake source archive with Finder and follow the instructions in the README.rst to build
and install cmake on MacOS. This will be in Terminal by using cd to change into the cmake-4.0.3 directory
and running the following command:
```
./bootstrap && make && sudo make install
```

Install Homebrew by opening Terminal and run the following command
```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

After Homebrew installs, install ninja by typing or copy and paste this command at the command prompt in
Terminal
```
brew install ninja
```

------------------------------------------------------------------------------
# Compiling the Libraries on MacOS
Below are the required libraries and tested versions for a JS8Call build on MacOS along with links to download them.

NOTE: You only have to build the libraries once. You can use the built libraries to compile various branches of the JS8Call
repository. Or make modifications to the source code and compile. You only need to rebuild the js8libs library if one
of the library versions is being changed, e.g. compiling with a newer version of Hamlib, etc..

* [libusb-1.0.29](https://github.com/libusb/libusb/releases/download/v1.0.29/libusb-1.0.29.tar.bz2)
    
* [Hamlib-4.6.4](https://github.com/Hamlib/Hamlib/releases/download/4.6.4/hamlib-4.6.4.tar.gz)
    
* [fftw-3.3.10](https://fftw.org/fftw-3.3.10.tar.gz)
    
* [boost_1_88_0](https://archives.boost.io/release/1.88.0/source/boost_1_88_0.tar.gz)
    
* Qt-6.8.1 (we'll cover how to get this via git later in this document)

*   Note that the use of the tilde ~ in the following filesystem paths designates your user directory, e.g.
    on MacOS ~ is the same as /Users/YourUserName . In most instances the use of the tilde will work fine but
    some of the libraries (libusb in particular) may require you to replace the ~ with the full path.
    
    In Terminal create a directory in which to build JS8Call and inside that directory yet another directory
    for the JS8Call libraries; the name of these directories doesn't matter but must be used consistently, e.g., `~/development/js8libs`
    
    `~/development` is the directory where all build project source code will be stored.
    `~/development/js8libs` is the library directory for building JS8Call and will be referenced with a --prefix
    flag in most build options. The following command in Terminal will accomplish this:
    ```
    mkdir ~/development && mkdir ~/development/js8libs
    ```

*   Download the libraries with the above links and drag the source archives to your `~/development` folder in
    Finder. Double click on each one in Finder to unpack it. Make sure you unpack these in the root `~/development`
    folder, NOT in `~/development/js8libs`. The built product will be installed in js8libs.
    
*   We will now build and install the libraries you downloaded with a series of commands. Some of these may take
    awhile - be patient. Remember if the command fails, replace the tilde ~ with your full user path.
    
    - libusb-1.0.29
        ```        
        cd ~/development/libusb-1.0.29 \
            && ./configure --prefix=~/development/js8libs \
            && make \
            && make install
        ``` 

    - Hamlib-4.6.4
        ```
        cd ~/development/hamlib-4.6.4 \
            && ./configure --prefix=~/development/js8libs \
            && make \
            && make install
        ```

    - fftw-3.3.10
        ```
        cd ~/development/fftw-3.3.10 \
            && ./configure CFLAGS=-mmacosx-version-min=11.0 \
                    --prefix=~/development/js8libs \
                    --enable-single \
                    --enable-threads
        ```
        NOTE: Depending on the architecture in use, a non-trivial speedup can be performed by enabling SIMD instructions.
        For example, to do so on Apple silicon Macs, add `--enable-neon` to the above command.
    
        See https://www.fftw.org/fftw3_doc/Installation-on-Unix.html for further details on other architectures.
    
        After fftw is configured build and install it with this command:
        ```
        make && make install
        ```

    - boost_1_88_0
        ```
        cd ~/development/boost_1_88_0 \
            && ./bootstrap.sh --prefix=~/development/js8libs \
            && ./b2 -a -q \
            && ./b2 -a -q install
        ```

    - Qt-6.8.1

        We'll clone Qt6 from github using git with the following command. This will create a qt6 directory in
        `~/development`
        ```
        cd ~/development && git clone https://github.com/qt/qt5.git qt6
        ```
        Then we cd into the newly created qt6 directory with
        ```
        cd qt6
        ```
        Now we use git to switch to the 6.8.1 branch with
        ```
        git switch 6.8.1
        ```
        Then we initialize the submodules. Be patient, this is going to take awhile.
        ```
        ./init-repository
        ```
        
        We should be ready to build Qt6. This is going to take a long time - Qt is a monster. First we'll make a
        build directory to keep the qt6 source tree clean. Basically execute these commands in this order. After you
        execute the build you may as well go get lunch, coffee, take an extended vacation, whatever. It still might
        not be done when you get back. If you haven't bought an Apple silicon Mac, now might be the time to consider
        buying one.
        ```
        mkdir ~/development/qt6-build && cd ~/development/qt6-build \
            && ../qt6/configure -prefix ~/development/js8libs \
            && cmake --build . --parallel \
            && cmake --install .
        ```

------------------------------------------------------------------------------
# Building JS8Call on MacOS
If we've made it this far we have built the libraries needed to compile JS8Call. We'll get the JS8Call sourcecode
with git like we did with Qt6.
```
cd ~/development && git clone https://github.com/js8call/js8call.git
```
        
NOTE: the main (dev) branch will be checked out by default. If you want to check out a different branch you can
use `git switch release/2.3.1` to switch to and build the 2.3.1 branch, for instance. Or to list all the branches
you can use `git branch`. There are no submodules to pull in for JS8Call so it is a simple matter of creating a
build directory and run `cmake` to configure the build, followed by a `make install`.
```
cd ~/development/js8call && mkdir build && cd build \
    && cmake -DCMAKE_PREFIX_PATH=~/development/js8libs \
        -DCMAKE_BUILD_TYPE=Release .. \
    && make install
```
        
If all goes well, you should end up with a `js8call.app` application in the build directory. Test by typing
`open ./js8call.app`. Once you're satisfied with the test results, copy js8call.app to /Applications.
