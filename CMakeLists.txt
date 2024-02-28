cmake_minimum_required(VERSION 3.28)
project(MyProject)

set(CMAKE_CXX_STANDARD 23)

add_subdirectory(Test)
include_directories(include)

add_executable(MyProject main.cpp)
