# General Cross Compilation WorkFlow for Raspberry Pi - Building a cross compile SDK for raspberry pi

#### Cross Compilation WorkFlow for Raspberry Pi using [raspberry pi tools (compilers)](https://github.com/raspberrypi/tools)  + [cmake-toolchains](https://cmake.org/cmake/help/v3.6/manual/cmake-toolchains.7.html#cross-compiling).

>In easy terms, for cross compilation (for raspberry pi here but generalizes to any architecture), the idea is to create a virtual and isolated (from host) compilation environment ([we do this by setting correct environment variables](https://autotools.io/pkgconfig/cross-compiling.html)) similar to raspberry pi you wish to compile for and set up environment variables so that [cmake](https://cmake.org/cmake/help/v3.6/manual/cmake-toolchains.7.html) and [pkg-config](https://people.freedesktop.org/~dbn/pkg-config-guide.html) will find required libraries/software and work as if they are compiling in and for raspberry pi. Of course the advantage is the use of the power of the host system, the same compilation can take a lot of time if compilation is explicitly in raspberry Pi.

#### Host and Target environment used in this documentation:

Environment | Discription
------------ | -------------
Host | 18.04.1 LTS (Bionic Beaver) 64-bit - x86_64 Processor
Target | Raspbian GNU/Linux 8 ([Raspbian - Jessie](http://downloads.raspberrypi.org/raspbian/images/raspbian-2017-07-05/)) - ARMv7 Processor
Raspberry-PI-Toolchains-download | https://github.com/raspberrypi/tools.git
Raspberry-PI-Toolchain-Used-for-this-documentation | *gcc-linaro-arm-linux-gnueabihf-raspbian-x64* - find folder and required files in _Raspberry-PI-Toolchains-download_
SYSROOT | folder readily provided with the raspberry Pi toolchain folder : /path/to/your/raspberryPi/toolchain_folder/arm-bcm2708/arm-rpi-4.9.3-linux-gnueabihf/arm-linux-gnueabihf/sysroot/
CMAKE-Toolchain-file | Custom written toolchain file.
Example | A C++ hello world program.

#### Prerequisite knowledge that will be helpful on understanding this documentation (a beginners knowledge is enough):
1. What is cross compilation?
    - What is SYSROOT?
        - skdjf
            - skldfj
                - klsdjf
    - What is [PKG-CONFIG](https://people.freedesktop.org/~dbn/pkg-config-guide.html)?
    - Some familiarity with bash command line.
    - Setting PKG-CONFIG environment - so that it uses the libraries and packages from SYSROOT(of target system), and not the packages and libraries of host system.
2. How to use [CMake](https://cmake.org/)?
    - How to use CMake for cross-compilation?
    - Setting CMake flags, [cmake-toolchain file](https://cmake.org/cmake/help/v3.6/manual/cmake-toolchains.7.html#cross-compiling-for-linux) and cmake file.
3. What is _[make](https://www.gnu.org/software/make/)_ and how to use it for building C++ project/binaries?

#### Abstract of cross compilation workflow steps :

1. There are two options for sysroot (at least for simple C++ applications) to create SYSROOT for cross compilation.
    - _[This documentation uses this option]_ One is to use the SYSROOT folder readily provided with the raspberry Pi toolchain folder : _/path/to/your/raspberryPi/toolchain_folder/arm-bcm2708/arm-rpi-493-linux-gnueabihf/arm-linux-gnueabihf/sysroot/_
    - Other, is to create a SYSROOT from your raspberry Pi image - [See this documentation for steps to do the same](Link to OpenCV Cross compilation SYSROOT creation steps here) . This is better if you want to work with some libraries or software as dependencies.
2. Download raspberry-pi toolchain. We will be using SYSROOT provided in this download.
3. Write a _toolchain.cmake_ file that will be used by CMAKE to understand that you are doing a cross-compilation. In the _toolchain.cmake_ file you have to specify:
    - Where is the C cross compiler.
    - Where is the CXX (C++) cross compiler.
    - Where is the SYSROOT folder.
4. Use _make_ to build the C++ application binary.
5. Copy the binary to your raspberry pi and execute it there. _Note:_ If you try and execute this file in your host it should not run and throw an _exec_ error.

#### Step by Step Guideline on the workflow:
1. Download raspi toolchain from [their official git repo](https://github.com/raspberrypi/tools). (Use git clone preferable but all up to you.)
2. _Optional :_ Just to be organized let us create a working space (folder) for our C++ application. For example: _cross\_compile\_for\_raspi_ and be inside this folder.
3. Create _toolchian.cmake_ file. This file specifies the following with CMake Flags:
    - **Location of C cross-compiler with CMAKE_C_COMPILER flag :** We use the C-Compiler located at similar address in downloaded toolchain folder as this: _/home/username/path/to/raspberry-pi/tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian-x64/bin/arm-linux-gnueabihf-gcc-4.8.3_
    - **Location of C cross-compiler with CMAKE_CXX_COMPILER flag :** We use the CPP-Compiler located at similar address in downloaded toolchain folder as this: _/home/username/path/to/raspberry-pi/tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian-x64/bin/arm-linux-gnueabihf-g++_
    - **Location of SYSROOT folder with CMAKE_FIND_ROOT_PATH flag :** We use the SYSROOT folder readily provided with the raspberry Pi toolchain folder as this: _/home/username/path/to/your/raspberryPi/toolchain_folder/arm-bcm2708/arm-rpi-4.9.3-linux-gnueabihf/arm-linux-gnueabihf/sysroot/_
    - Example _toolchain.cmake_ file:
    ``` cmake
    # Raspberry PI toolchain cmake file
    # Define your host system
    SET(CMAKE_SYSTEM_NAME Linux)
    SET(CMAKE_SYSTEM_VERSION 1)
    # Define the cross compiler locations
    SET(CMAKE_C_COMPILER /path/to/your/raspberryPi_toolchain_folder/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian-x64/bin/arm-linux-gnueabihf-gcc)
    SET(CMAKE_CXX_COMPILER /path/to/your/raspberryPi_toolchain_folder/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian-x64/bin/arm-linux-gnueabihf-g++)
    # When you start porting a code to a specific target platform, it is likely that the first problem you will face is to satisfy a few (many?) missing dependencies. 
    # These, dependencies can be satisfied in two ways: with static libraries or with shared libraries. Which are usually in the SYSROOT.
    # Thus we define the sysroot path for the RaspberryPi distribution in our raspberry-pi-tools folder.
    SET(CMAKE_FIND_ROOT_PATH /path/to/your/raspberryPi_toolchain_folder/arm-bcm2708/arm-rpi-4.9.3-linux-gnueabihf/arm-linux-gnueabihf/sysroot/)
    # Search for programs or compiler tools only in the build host directories
    SET(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)
    # Search for libraries and headers only in the target directories
    SET(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)
    SET(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)
    ```
4. Create a C/C++ project (file) to be compiled with cmake.
    ``` C++
    // Sample hello.cpp file
    #include <iostream>
    using namespace std;
        int main() {
        cout << "!!!Hello World From Pi!!!" << endl;
        return 0;
    }
    ```
5. Write a CMake file e.g., _CMakeLists.txt_ for compiling the above file.
    ``` cmake
    cmake_minimum_required (VERSION 2.6) # can use CMake version you are comfortable with
    project (cmake_arm_hello)
    add_executable(cmake_arm_hello /path/to/the/cpp/file/hello.cpp)
    ```
6. Now run the _cmake_ command here. CMake flag **CMAKE_TOOLCHAIN_FILE**, is to be set to make sure CMake knows the compilation is a cross compilation.
    ``` bash
    cmake -DCMAKE_BUILD_TYPE=RELEASE -DCMAKE_TOOLCHAIN_FILE=</path/to/toolchian.cmake> </path/to/CMakeLists.txt>
    ```
7. Run _make_ in this same location. CMake auto generates a make file.
8. Now [connect to your raspberry pi](update/link/to/readme.md/for/how/to/connect/headless/raspberrypi/to/your/host/with/ssh), copy the binary there and execute it.
9. _Note:_ for specific libraries you might get a cmake-toolchian.cmake file provided by themselves. For example, [look this instructions for OpenCV](link_to_opencv_cross_compile_docs).
