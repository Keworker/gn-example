# GN example

This project contains most simple example of project with GN build system. 

## Building

Before building, you should make sure that you have installed 
C++ compiler, for example, MinGW (https://www.mingw-w64.org/downloads), 
Ninja build system (https://github.com/ninja-build/ninja), 
GN meta build system (https://gn.googlesource.com/gn/). 

1. Generate `main.o` file with GCC compile:
```
gcc -Wall -c -o out/main.o main.cc
```
2. Generate ninja files with GN:
```
gn gen out
```
3. Compile proj with Ninja:
```
ninja -C out hello
```
4. Run compiled `.exe` file:
```
out/hello
```

## About Chromium project structure

You can read about Chromium GN project structure [here](https://github.com/Keworker/gn-example/tree/master/chromium-structure). 
