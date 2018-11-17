### How to install [Ogre3D](https://www.ogre3d.org/) in Linux?

##### _Note: This documentation was written in relation to host Ubuntu 18.04, but should be similarly working with other linux OS's too._

### An easy installation method (However I would prefer building from source):
1. Ubuntu comes with a particular version of Ogre3D that can be installed easily - [reference](https://codeyarns.com/2017/04/17/how-to-build-ogre-in-ubuntu/).
    - Install this package for the same : `sudo apt-get install libogre-1.9-dev`.
  
### Building [Ogre3D from source](https://github.com/OGRECave/ogre):
 1. Compile and build Ogre3D from [their official github source](https://github.com/OGRECave/ogre). Or, download the latest release source tar or zip [from here - you can see the latest release tag](https://github.com/OGRECave/ogre/releases).
 2. Use the instructions provided in their [Ogre3D official wiki](https://ogrecave.github.io/ogre/api/latest/building-ogre.html). Do exactly as they say and you will be fine.
 3. With these instructions you should have by now built and installed Ogre3D.
  
### Now, Write and Run an Ogre3D application:
1. We can follow the instruction provided [here](https://ogrecave.github.io/ogre/api/latest/setup.html) on their official site to do so.
2. You can build a sample/bootstrap ogre project provided in your Ogre3D source you downloaded. Look at location similar to `/path/to/your/ogre3d/source/Samples/Tutorials/`.
    - Here you will find a `CMakeLists.txt` and some sample Ogre cpp files.
    - Create a `build` folder and inside it run `cmake ..` and `make` to compile and build these Ogre Samples.
    - **NOTE - However!!!** that you might get quite an interesting error saying _libfreetype.so.2.6_ (libfreetype version can be different than 2.6 for you) couldn't be found. Fortunately, I found a solution for the same in [this answer in stackoverflow](https://stackoverflow.com/a/48941119).
       - As per the first instruction in this stackoverflow post's answer - you have to copy the shared object (*.so) files related to `libfreetype<your_version_of_libfreetype>` library from `/path/to/ogre/build/Dependencies/lib/libfreetype.so*` to basically where the compiler can find them, say `/usr/local/lib`.
      - Now run `cmake ..` and `make` to compile and build these Ogre Samples.
 3. Run the built sample Ogre application.
 4. You can use this same `CMakeLists.txt` file with minimal changes (say changing the executable and project name to your own project name) to build and run your own Ogre application.
 
### Happy Rendering!!!
