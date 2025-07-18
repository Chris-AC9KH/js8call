------------------------------------------------------------------------------
# Building JS8Call on Linux

**Prerequisites**\
Linux Versions:  Debian 12 or newer (or derivatives, e.g., Mint 22.1, Ubuntu 24.04, MX Linux); Fedora 39 or newer.  Enable Source Code archives in the repositories, then issue the following commands at the command-line prompt:

Debian & derivatives:\
   `sudo apt install build-essential`\
   `sudo apt build-dep hamlib`\
   `sudo apt build-dep js8call`
  
Fedora:\
   `sudo dnf groupinstall "Development Tools" "Development Libraries"`\
   `sudo dnf build-dep hamlib`\
   `sudo dnf build-dep js8call`

**Building Hamlib**\
It is easiest to build Hamlib 4.6.4 and install it on the system.  To see what versions of Hamlib are available, while in the hamlib directory, type `git branch -r` or `git tag` to get a list.  To get the source and build Hamlib, open a command prompt and issue the following commands:

   `cd Downloads`\
   `git clone https://github.com/Hamlib/Hamlib.git hamlib`\
   `cd hamlib`\
   `git checkout 4.6.4` (or whatever version you choose)\
   `./bootstrap`\
   `./configure`\
   `make -j 4` (where 4 is the number of CPU cores you want to use)\
   `sudo make install-strip`\
   `sudo ldconfig` (this is necessary to load/configure system libraries)\
   `rigctl --version` (this is to test that Hamlib is installed and at the correct version level)

**Building JS8Call**\
To obtain the JS8Call source and build, issue the following commands in a terminal window:\
   `cd Downloads`\
   `git clone https://github.com/js8call/js8call.git js8call`\
   `cd js8call`\
   `mkdir build && cd build`\
   `make ..`\
   `make -j 4` (where 4 is the number of CPU cores you want to use)
   
   When the build is finished, to package an installer, type `cpack -G DEB` for Debian-based systems like Debian, Ubuntu, and Mint; or `cpack -G RPM` for Fedora-based systems.