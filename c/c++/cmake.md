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
# include headers
`target_include_directories(Main PRIVATE "include/")`
This would essentiallymount the `include/` directory to the build process
In cpp files, you just need to specify header file paths in relation to the `include/` directory.
For example, if we have `include/proj/main.h`, then in cpp file, we need `#include "proj/main.h"`
# glossary
## target
- executables, you can run from command line
- libraries, can be linked to executables

