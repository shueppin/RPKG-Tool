# RPKG Tool

The RPKG tool allows for easy (un)packing of files from the Glacier Engine RPKG file format!  
The goal of this fork is to add support for transparent textures, for example when exporting whole maps. For this, the main focus lies on the CLI tool.

Consider checking out the upstream [RPKG-TOOL](https://github.com/glacier-modding/RPKG-Tool).

This repository contains 3 components - [the CLI](src/cli), [the GUI](rpkg-gui) & [the DLL](src/lib).

[The CLI](src/cli) allows you to perform simple operations from a terminal. </br>
[The GUI](rpkg-gui) allows you to easily perform operations with a user interface. (No executable will be provided for the GUI in this fork) </br>
And [the DLL](src/lib) allows you to embed RPKG into your own apps. (No executable will be provided for the GUI in this fork) </br>


## Building it
### MSVC Toolchain
You need to have the MSVC Toolchain for x64 installed.  

1. Clone this repository
2. Open the MSVC Shell for x64 and go into the directory of the RPKG-Tool
3. Run `cmake --preset RelWithDebInfo` to prepare for build (you only need to execute this once)
4. Run `cmake --build cmake-build-RelWithDebInfo --target rpkg-cli` to build the CLI
5. To use this project you need to download the newest hashes from [here](https://github.com/glacier-modding/Hitman-Hashes/releases/latest/download/latest-hashes.7z) and extract `hash_list.txt`
6. To execute the CLI you need the following files in the same directory:
```
hash_list.txt
quickentity_ffi.dll
ResourceLib_HM2.dll
ResourceLib_HM3.dll
ResourceLib_HM2016.dll
rpkg-cli.exe
```

### Possible bug fix on error with GTest
First configure the CMake project. Then, in the generated file `Build-Path/_deps/gltfsdk-src/CMakeLists.txt`, locate the following code block (around line 45):

```cmake
find_package(GTest CONFIG)
if(NOT GTest_FOUND)
    add_subdirectory(External/googletest)
    add_library(GTest::gtest_main ALIAS gtest_main)
endif()
```

Replace it with:

```cmake
find_package(GTest CONFIG)
if(NOT GTest_FOUND)
    add_subdirectory(External/googletest)
    if(NOT TARGET GTest::gtest_main)
        add_library(GTest::gtest_main ALIAS gtest_main)
    endif()
endif()
```

This change checks whether the `GTest::gtest_main` target already exists before attempting to create the alias. If the target already exists, CMake skips creating it, preventing the duplicate-target error that would otherwise cause CMake configuration to fail.
