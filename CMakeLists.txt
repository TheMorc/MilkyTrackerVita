#
#  CMakeLists.txt
#
#  Copyright 2016 Dale Whinham
#
#  This file is part of MilkyTracker.
#
#  MilkyTracker is free software: you can redistribute it and/or modify
#  it under the terms of the GNU General Public License as published by
#  the Free Software Foundation, either version 3 of the License, or
#  (at your option) any later version.
#
#  MilkyTracker is distributed in the hope that it will be useful,
#  but WITHOUT ANY WARRANTY; without even the implied warranty of
#  MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#  GNU General Public License for more details.
#
#  You should have received a copy of the GNU General Public License
#  along with MilkyTracker.  If not, see <http://www.gnu.org/licenses/>.
#

cmake_minimum_required(VERSION 3.2)

if(NOT DEFINED CMAKE_TOOLCHAIN_FILE)
  if(DEFINED ENV{VITASDK})
    set(CMAKE_TOOLCHAIN_FILE "$ENV{VITASDK}/share/vita.toolchain.cmake" CACHE PATH "toolchain file")
  else()
    message(FATAL_ERROR "Please define VITASDK to point to your SDK path!")
  endif()
endif()

include("/usr/local/vitasdk/share/vita.cmake" REQUIRED)

cmake_minimum_required(VERSION 3.10)
project(Tracker)


# Enable IDE solution folders
set_property(GLOBAL PROPERTY USE_FOLDERS ON)

# Enable tracker-specific functions in MilkyPlay
add_definitions(-DMILKYTRACKER)

# Adhere to GNU filesystem layout conventions
include(GNUInstallDirs)

# Force SDL if requested
option(FORCESDL "Force SDL instead of native" ON)
    unset(APPLE)
    unset(WIN32)
    add_definitions(-D__FORCE_SDL_AUDIO__)
add_definitions(-mno-unaligned-access)
add_definitions(-fno-short-enums)

# Lowercase project name for binaries and packaging
string(TOLOWER ${PROJECT_NAME} PROJECT_NAME_LOWER)
string(TOLOWER ${CMAKE_SYSTEM_NAME} SYSTEM_NAME_LOWER)

# Additional CMake modules
list(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)

# Version number in format X.YY.ZZ
set(VER_X  1)
set(VER_YY 03)
set(VER_ZZ 00)
set(VER_FULL "${VER_X}.${VER_YY}.${VER_ZZ}")

# Generate version header from the above
configure_file(
    ${PROJECT_SOURCE_DIR}/src/tracker/version.h.in
    ${PROJECT_BINARY_DIR}/src/tracker/version.h
)


    # Workaround for SDL bug #3295, which occurs in SDL2 <2.0.5
    # https://bugzilla.libsdl.org/show_bug.cgi?id=3295
    cmake_policy(SET CMP0004 OLD)

    find_package(SDL2 REQUIRED)


    set(SUFFIXES_ORIG ${CMAKE_FIND_LIBRARY_SUFFIXES})
    set(CMAKE_FIND_LIBRARY_SUFFIXES .a ${CMAKE_FIND_LIBRARY_SUFFIXES})

find_package(SDL2 REQUIRED)


link_directories(
  ${CMAKE_CURRENT_BINARY_DIR}
  $ENV{VITASDK}/arm-vita-eabi/lib
)
include_directories(
# Include the CMake-generated version header from the build directory
../common)


add_subdirectory(docs)
add_subdirectory(resources/music)
add_subdirectory(src/compression)
add_subdirectory(src/fx)
add_subdirectory(src/milkyplay)
add_subdirectory(src/ppui)
add_subdirectory(src/tracker)



set(VITA_APP_NAME "MilkyTracker")
set(VITA_TITLEID  "MILKTRACK")
set(VITA_VERSION  "01.00")
