---
title: Making A Mandelbrot Fractal Visualizer With C++
date: 2018-12-08 19:01:55
tags:
    - C++
    - guide
---

# Introduction

A mandelbrot set is a type of fractal that was discovered a while ago. When it was discovered doesn't really matter all that does is how cool it looks. I mean look at that shit hot damn. If you want to have your mind blown big time watch a zoom video on YouTube, that stuff is wild. What's even crazier is how simple the function that makes this neat fractal is. It's only  {% math %} z_{n+1} = z_{n}^2 + c {% endmath %}, that's it I'll explain how you actually get it from that graph later on in this article. But overall I'll give a full guide for the full setup for everything so it's beginner friendly. Keep in mind though that I'll be working from the context of a linux system. So if anything breaks on Windows or OSX don't sue me. Alright lets get going.

![A picture of a mandelbrot fractal](/assets/images/mandelbrot-guide/pic_1.jpg)

# Setting Up The Environment

For this project we will be using a library called [SFML](https://www.sfml-dev.org/index.php) this will allow us to create and save images. This isn't 100% needed but ur not my mom, you can't tell me what to do. Jokes aside it will allow us to write to .png files and see a more visual output of our code working. Which may be nice. The way that I linked it you'll need to install the library. Below is an example of getting it in a debian environment. 

```bash
{% math %} sudo apt install libsfml-dev
```

Also in terms of build systems I'm going to go with CMake, follow [these](https://cgold.readthedocs.io/en/latest/first-step/installation.html) directions to get that installed. CMake basicly just generates our make files and just makes working with C++ a whole lot easier since you don't ever really have to work about your make files unless you are linking a new library or changing some sort of build flag.

To all we have to do to set up CMake is to create a file called CMakeLists.txt in the project root and fill it with the below contents. 

```
cmake_minimum_required(VERSION 3.1)
project(mandelbrot)
list(APPEND CMAKE_MODULE_PATH "{% math %}{CMAKE_CURRENT_LIST_DIR}/cmake_modules")

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
add_executable(mandelbrot {% math %}{SOURCES})

find_package(SFML 2.4.2 COMPONENTS graphics audio REQUIRED)
target_link_libraries(mandelbrot sfml-graphics sfml-audio)

#Set to using the latest and greatest version of c++
set_target_properties(mandelbrot PROPERTIES
  CXX_STANDARD 17
  CXX_EXTENSIONS OFF
)
```

You'll also need to make a new folder called cmake_modules and download [this](https://raw.githubusercontent.com/SFML/SFML-Game-Development-Book/master/CMake/FindSFML.cmake) file and place it in that folder. This file will allow cmake to find our installation of SFML.

Once we have all that shite set up we can add a simple hello world file at `src/Main.cpp` and while your at it make a `include` directory to store our header files.

```cpp
#include <iostream>

int main(int argc, char **argv) {
  std::printf("hello world\n");

  return 0;
}
```

After all this your directory should looking a little something like this:

```
├── CMakeLists.txt
├── cmake_modules
│   └── FindSFML.cmake
├── include <-- #This is a folder not a file#
└── src
    └── Main.cpp
```

Once we have all that set up we should be able to run our build command to generate our executeable and then run it. 

```bash
{% math %} cmake .
-- The C compiler identification is GNU 7.2.0
-- The CXX compiler identification is GNU 7.2.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Found SFML 2.4.2 in /usr/include
-- Configuring done
-- Generating done
-- Build files have been written to: /home/user/Documents/projects/Mandelbrot

{% math %} make
Scanning dependencies of target mandelbrot
[ 50%] Building CXX object CMakeFiles/mandelbrot.dir/src/Main.cpp.o
[100%] Linking CXX executable mandelbrot
[100%] Built target mandelbrot

{% math %} ./mandelbrot
hello world
```

Eyy, alright so now that we have our code environment we can move on to actually getting some work done on the code

## VSCode helpful config files 

.vscode/tasks.json
```json
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "build",
            "type": "shell",
            "command": "make",
            "group": {
                "kind": "build",
                "isDefault": true
            }
        }
    ]
}
```

---

Make sure you have gdb for this one, this will allow us to debug our code http://www.gdbtutorial.com/tutorial/how-install-gdb

.vscode/launch.json
```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(gdb) Launch",
            "type": "cppdbg",
            "request": "launch",
            "program": "{% math %}{workspaceFolder}/mandelbrot",
            "args": [],
            "stopAtEntry": false,
            "cwd": "{% math %}{workspaceFolder}",
            "environment": [],
            "externalConsole": true,
            "MIMode": "gdb",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ]
        }
    ]
}
```

# Calculating The Mandelbrot set

## The Math Stuff

So a Mandelbrot is defined by a fairly simple recursive mathematical function (see below). The way that we color the points is based on how quickly this function goes off to the moon (goes to infinity / diverges). If the point on the graph doesn't diverge in the set number of iterations we color it black. Otherwise we color it depending on how fast it took to diverge. 

{% math %}
\begin{aligned}
& z_{n+1} = z_{n}^2 + c
\end{aligned}
{% endmath %}

Something that you'll notice though is that we only have one variable that we can control {% math %}c{% endmath %} so how are we able to get an x, y plot of the data. Now here's where it gets kinda fucky for those of us who haven't done high tier math before. The x, y coords are actually represented as a complex number in the following format {% math %}x + yi{% endmath %}. Alright what the shit is i? I is basicity {% math %}\sqrt{-1}{% endmath %}, if you're thinking that's not a thing, well complex numbers disagree. I have no idea how they work or why math decided it needs them but all we really need to know for this is that {% math %}i^2 = -1{% endmath %}. So that means that if we square a number in the formatted stated above for {% math %}c{% endmath %} we get the following simplification.

{% math %}
\begin{aligned}
& c = x + yi \\
& (x + yi)^2 = x^2 + 2xyi + yi^2 \\
& x^2 + 2xyi + yi^2 = x^2 + 2xyi - y^2 \\
& c^2 = (x^2-y^2) + i(2xy)
\end{aligned}
{% endmath %}

So with this we are then able to calculate individually the imaginary (the i thing) and the real components of c separately then it's fairly easy to get our value for {% math %}z_{n+1}{% endmath %} as can be seen below.

{% math %}
\begin{aligned}
& z_0 = c^2 = (x^2-y^2) + i(2xy) \\
& z_{n+1} = z_{n}^2 + c \\ 
& z_{n}^2 + c = ((x_z^2-y_z^2) + i(2x_zy_z)) + (x_c + y_ci) \\ 
& ((x_z^2-y_z^2) + i(2x_zy_z)) + (x_c + y_ci) = (x_z^2 - y_z^2 + x_c) + i(2x_zy_z + y_c)
\end{aligned}
{% endmath %}

If this looks fucked (which it does not gonna lie) right now don't worry, it will start to make a lot more sense when we start looking at the code for this (I hope). So now we just need to figure out if the value for z has diverged or not. Luckily some smart guy has already done this for us. If the following statement is true it means that the value for z will diverge.

{% math %}
\begin{aligned}
& \sqrt{x^2 + y^2} >= 2 \\
& x^2 + y^2 >= 2^2 \\
& x^2 + y^2 >= 4
\end{aligned}
{% endmath %}

## The Code Stuff

Alright enough with that math stuff, this is computer science not math science. Lets get on to the real code. We're going to start off by giving you the full code and then I'll explain it line by line (ish) and hopefully by the end it will make sense. So we are going to create a new file  `src/Mandelbrot.cpp` and fill it with the following code:

```cpp
#include <math.h>

const double THREASHOLD = 2.0;
const int ITERATIONS = 200;

namespace mandelbrot {

int calculate(double x, double y) {
  double realVal = x;
  double imgVal = y;

  int i = 0;
  while (pow(realVal, 2.0) + pow(imgVal, 2.0) <= 4.0) {
    if (i == ITERATIONS) {
      return -1;
    }
    double tmp = pow(realVal, 2.0) - pow(imgVal, 2.0) + x;
    imgVal = 2.0 * realVal * imgVal + y;
    realVal = tmp;

    // a repetition indicates that the point is in the Mandelbrot set
    if (realVal == x && imgVal == y) {
      // points in the Mandelbrot set are represented by a return value of -1
      return -1;
    }
    i++;
  }

  return i;  // return how long it took for the point to diverge
}

}  // namespace mandelbrot
```