cmake_minimum_required(VERSION 3.14)

project(example LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# defines targets and sources
add_subdirectory(src)

# contains an "external" library we will link to
add_subdirectory(external)
