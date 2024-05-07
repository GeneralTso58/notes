# basics
In CMakeLists.txt
```
cmake_minimum_required(VERSION 3.19.3)
project(perceptrons)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED True)

add_executable(main main.cpp)
```
to build, run following command
- cmake -S [cmake file localtion] -B [build location]
- cd into build folder
- cmake --build .