# OpenCV Library Cross Compilation WorkFlow for Raspberry Pi

>In easy terms, for cross compilation (for raspberry pi here but generalizes to any architecture), the idea is to create a compilation environment ([we do this by setting correct environment variables](https://autotools.io/pkgconfig/cross-compiling.html)) similar to raspberry pi you wish to compile for and set up environment variables so that [cmake](https://cmake.org/cmake/help/v3.6/manual/cmake-toolchains.7.html) and [pkg-config](https://people.freedesktop.org/~dbn/pkg-config-guide.html) will find required libraries/software and work as if they are compiling in and for raspberry pi. Of course the advantage is the use of the power of the host system, the same compilation can take a lot of time if compilation is explicitly done in raspberry Pi.

#### Host and Target environment used in this documentation:
Environment | Discription
------------ | -------------
Host | 18.04.1 LTS (Bionic Beaver) 64-bit - x86_64 Processor
Target | Raspbian GNU/Linux 8 ([Raspbian - Jessie](http://downloads.raspberrypi.org/raspbian/images/raspbian-2017-07-05/)) - ARMv7 Processor
Raspberry-PI-Toolchains-download | https://github.com/raspberrypi/tools.git
Raspberry-PI-Toolchain-Used-for-this-documentation | *gcc-linaro-arm-linux-gnueabihf-raspbian-x64* - find folder and required files in _Raspberry-PI-Toolchains-download_
SYSROOT | Created an image of the Raspberry Pi Sd Card (after all required libraries were installed in raspberry pi)
OpenCV Source Code | https://github.com/opencv/opencv/archive/3.4.2.tar.gz
OpenCV-Contrib Source Code | https://github.com/opencv/opencv_contrib/archive/3.4.2.tar.gz (notice this and is very important - version of opencv and opencv-contrib should be same)
CMAKE-Toolchain-file | https://github.com/opencv/opencv/blob/master/platforms/linux/aarch64-gnu.toolchain.cmake (similar folder location provided with OpenCV Source Code)

#### Prerequisite knowledge that will be helpful on understanding this documentation (a beginners knowledge is enough):
1. What is cross compilation?
    - What is SYSROOT?
    - What is [PKG-CONFIG](https://people.freedesktop.org/~dbn/pkg-config-guide.html)?
    - Some familiarity with bash command line.
    - Setting PKG-CONFIG environment - so that it uses the libraries and packages from SYSROOT(of target system), and not the packages and libraries of host system.
2. How to use [CMake](https://cmake.org/)?
    - How to use CMake for cross-compilation?
    - Setting CMake flags, [cmake-toolchain file](https://cmake.org/cmake/help/v3.6/manual/cmake-toolchains.7.html#cross-compiling-for-linux) and cmake file.
3. What is _[make](https://www.gnu.org/software/make/)_ and how to use it for building C++ project/binaries?

#### Abstract of OpenCV cross compilation workflow steps :
1. Install all required (I say optional packages as well to be on the safe side), compiler and opencv-dependency packages on raspberry Pi (target system). _Make sure that build-essentials, cmake, git and pkg-config are installed also in your host system_.
2. From this step onwards, all the work of cross compiling OpenCV will be on host system. Now, make an image of your raspberry pi sd-card in your host system.
3. Now, mount the above raspberry pi sd-card image on host system. And then copy required folder from mounted image -{etc,lib,sbin,usr,var} - to any other folder in your host system and name it for example sysroot. This is your required SYSROOT for your cross compilation. _Note that you can also use the mounted image as sysroot itself_.
4. Download raspberry-pi toolchain, opencv and opencv-contrib source (opencv and opencv-contrib version should match).
5. Set environment variables for PKG-CONFIG and CMAKE Flags. Use CMAKE for cross compilation using the _relevant-toolchain.cmake_ file provided by OpenCV in the OpenCV source folder. Be sure to set all the required flags used by CMAKE for cross compiling OpenCV.
6. Use _make_ to build OpenCV library. Optionally use _make install_ to install OpenCV to the path mentioned with _CMAKE_INSTALL_PREFIX_ flag in above step.
7. Copy the installed files to similar location in your raspberry pi.
8. Now you can use this library to cross compile standalone OpenCV application.

#### Step by step guideline on the OpenCV cross compilation workflow:
1. First of all Install all required (I say optional packages as well to be on the safe side) compiler and
opencv-dependency packages on raspberry Pi (target system) (although Jessie was used here you can install the [latest rasbian
image on your raspberry pi](https://www.raspberrypi.org/downloads/raspbian/) - try and download desktop version for ease). Here is a [tutorial on how to connect to raspberry pi](update this with link to the document in github for headless connect raspberry pi).
    1. Install all libraries mentioned in [step 4 and step 5 in this link](https://www.alatortsev.com/2018/04/27/installing-opencv-on-raspberry-pi-3-b/) . I mentioned this link because it was put in more readable manner, but you can also follow this [official documentation on installing opencv dependencies](https://docs.opencv.org/3.4.3/d7/d9f/tutorial_linux_install.html) (see REQUIRED PACKAGES section only). Please remember to install all the compiler and required packages, a good idea is to install all optional packages as well. To make it easy, bash commands are put below for installing the required libraries.
        ```bash
        sudo apt-get install build-essential pkg-config cmake #[essential compiler packages]
        sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev libxvidcore-dev libx264-dev libgtk2.0-dev libgtk-3-dev #[required opencv dependency packages]
        sudo apt-get install python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libtiff5-dev libjasper-dev libpng12-dev libdc1394-22-dev #[optional dependencies for opencv]
        sudo apt-get install libatlas-base-dev gfortran #[optional dependencies for opencv]
        ```

    2. _Optional_ : I did not follow this step (I did not need opengl at this time) but if OPENGL is required any one of the following idea may do the trick.
        1. See this answer for [how to enable OpenGL on raspberry pi](https://raspberrypi.stackexchange.com/questions/75071/running-modern-opengl-on-raspberry-pi/75088#75088), maybe this is enough to get opengl installed on raspberry pi. 
        2. ONLY, if above instruction does not work [see this instruction](https://stackoverflow.com/a/11067289) to install GtkGlExt in the raspberry pi which shall also give you the opengl support with opencv if above mentioned step is not enough.

    3. _Make sure that build-essentials, cmake, git, pkg-config are also installed in your host system as well. Even if you install all above packages it is fine because you will be setting environment for cross compilation where PKG_CONFIG and CMAKE will be using the raspberry pi sysroot environment_.

2. [Now, make an image of your raspberry pi sd-card in your host system](link_to_raspi_image_creation_from_its_sd_card).
