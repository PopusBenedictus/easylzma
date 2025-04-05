cmake_minimum_required(VERSION 3.16)

# Written in 2009 by Lloyd Hilaiel
#
# License
#
# All the cruft you find here is public domain.  You don't have to credit
# anyone to use this code, but my personal request is that you mention
# Igor Pavlov for his hard, high quality work.
#

project(easylzma VERSION 0.0.9 LANGUAGES C)
set(CMAKE_C_STANDARD 99)
set(EASYLZMA_MAJOR 0)
set(EASYLZMA_MINOR 0)
set(EASYLZMA_MICRO 9)

set(EASYLZMA_DIST_NAME
        "easylzma-${EASYLZMA_MAJOR}.${EASYLZMA_MINOR}.${EASYLZMA_MICRO}")

# Optionally set a default build type if none was specified
if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE "Release")
endif()

if(CMAKE_BUILD_TYPE STREQUAL "Release")
  message(STATUS "** for a debug build: cmake -DCMAKE_BUILD_TYPE=Debug ..")
endif()

# Basic warning flags
set(CMAKE_C_FLAGS "-Wall")

if(WIN32)
  # Windows linker and compiler flags
  set(linkFlags "/PDB:NONE /INCREMENTAL:NO /OPT:NOREF /OPT:NOICF")
  set(CMAKE_EXE_LINKER_FLAGS "${linkFlags}" CACHE STRING "LZMA linker flags" FORCE)
  set(CMAKE_EXE_LINKER_FLAGS_DEBUG "" CACHE STRING "LZMA debug linker flags" FORCE)
  set(CMAKE_EXE_LINKER_FLAGS_RELEASE "" CACHE STRING "LZMA release linker flags" FORCE)
  set(CMAKE_SHARED_LINKER_FLAGS "${linkFlags}" CACHE STRING "LZMA shared linker flags" FORCE)
  set(CMAKE_MODULE_LINKER_FLAGS "${linkFlags}" CACHE STRING "LZMA module linker flags" FORCE)

  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} /wd4668 /wd4820 /wd4996 /wd4255 /wd4130 /wd4100 /wd4711")
  set(CMAKE_C_FLAGS_DEBUG "/D DEBUG /Od /Z7")
  set(CMAKE_C_FLAGS_RELEASE "/D NDEBUG /O2")
else()
  # Linux / macOS compile flags
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fPIC")
  set(CMAKE_C_FLAGS_DEBUG "-DDEBUG -g")
  set(CMAKE_C_FLAGS_RELEASE "-DNDEBUG -O2")
endif()

add_subdirectory(src)
add_subdirectory(elzma)
add_subdirectory(test)
