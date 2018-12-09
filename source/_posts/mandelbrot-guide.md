---
title: Making A Mandelbrot With C++
date: 2018-12-08 19:01:55
tags:
    - C++
    - guide
---

## Introduction

## Setting Up The Environment

For this project we will be using a library called [SFML](https://www.sfml-dev.org/index.php) this will allow us to create and save images. This isn't 100% needed but ur not my mom, you can't tell me what to do. Jokes aside it will allow us to write to .png files and see a more visual output of our code working. Which may be nice. The way that I linked it you'll need to install the library. Below is an example of getting it in a debian environment. 

```bash
$ sudo apt install libsfml-dev
```

Also in terms of build systems I'm going to go with CMake, follow [these](https://cgold.readthedocs.io/en/latest/first-step/installation.html) directions to get that installed. CMake basicly just generates our make files and just makes working with C++ a whole lot easier since you don't ever really have to work about your make files unless you are linking a new library or changing some sort of build flag.

To all we have to do to set up CMake is to create a file called CMakeLists.txt in the project root and fill it with the below contents. 

```
cmake_minimum_required(VERSION 3.1)
project(mandelbrot)

SET(CMAKE_CXX_FLAGS "-std=c++11 -O3")
SET(CMAKE_BUILD_TYPE Debug)

#Set the optomization low for debugging purposes, nothings worse that having <optimized out> when trying to fix an issue
set(CMAKE_CXX_FLAGS "-Wall -Wextra")
set(CMAKE_CXX_FLAGS_DEBUG "-g")
set(CMAKE_CXX_FLAGS_RELEASE "-O3")

#Bring the headers, such as Student.h into the project
include_directories(include)

#Add the source files
file(GLOB SOURCES "src/*.cpp")

#Create the executable
add_executable(mandelbrot ${SOURCES})

find_package(SFML 2.5 COMPONENTS graphics audio REQUIRED)
target_link_libraries(mandelbrot sfml-graphics sfml-audio)

#Set to using the latest and greatest version of c++
set_target_properties(mandelbrot PROPERTIES
  CXX_STANDARD 17
  CXX_EXTENSIONS OFF
)
```

